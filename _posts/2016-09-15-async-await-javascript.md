---
layout: post
title: Forma simples de reduzir o boilerplate de promises em JS
excerpt: Async/Await antes do tempo com a ajuda da biblioteca 'asyncawait'
categories: [desenvolvimento]
tags: [javascript]
comments: true
---

## Conceito

Para desenvolver em Javascript, naturalmente é necessário conhecer alguma forma de lidar com chamadas assíncronas. Independente do jeito que faça, o problema tem que ser resolvido, mesmo que não fique tão bonito de ser visto ou escrito. Todavia, código limpo é uma ajuda à documentação do seu script.

### Callbacks

```javascript
function sayMyName (firstName, lastName, callback) {
  const jessy = "Jessy";
  console.log(`${firstName} ${lastName}`);
  callback(jessy);
}

sayMyName("Walter", "White", function (person) { // Callbacks podem ser executadas direto na chamada ou passadas como valor
  console.log(`You're goddamn right, ${person}`)
});

// Walter White
// You're goddamn right, Jessy
```

Inclusive, eventos utilizam este padrão. Veja como uma chamada XHR é executada:

```javascript
function reqListener () {
  console.log(this.responseText);
}

var xhr = new XMLHttpRequest();
xhr.addEventListener("load", reqListener); // Executa o callback assim que o evento é chamado
xhr.open("GET", "http://www.example.org/example.txt");
xhr.send();
```

### Promises

Com o es6, promises foram incluídas na linguagem após anos de uso de maneira não oficialziada nas bibliotecas open source. Com a sua chegada, chamadas foram simplificadas na escrita e na leitura. Assim que uma promise é executada, ela precisa ser resolvida ou rejeitada. Caso contrário, ficará em modo de espera, sem utilidade. No exemplo abaixo, utilizei o sequelize em Node para gerar um histórico de pedidos.

```javascript
exports.ordersHistory = (rows, count) => {
  const orders = rows.map( order => {
    return order.getProducts()
    .then( products => Object.assign({}, order.toJSON(), { product: products.find( i => i) }));
  }); // Contém um array de promises

  return Promise.all(orders) // Aqui contém um array de pedidos já resolvidos
  .then( returnedOrders => {

    const theOrders = returnedOrders.map( ord => {
      return OrderStatus.findById(ord.orderStatusId)
      .then( orderStatus => {
        const query = {
          where: { ProductId: ord.product.id }
        };
        return ProductUrl.findAll(query)
        .then( images => {
          const product = ord.product.toJSON();
          product.images = images.map( i => i.toJSON());
          return Object.assign({}, ord, { orderStatus: orderStatus.toJSON() }, { product });
        }); // product
      }); // orderStatus
    }); // ord
    return Promise.all(theOrders);
  });
}
```

### Async/Await

Mesmo as promises facilitando e simplificando bastante os callbacks, alguns desenvolvedores preferem a ideia do Async/Await, que nada mais é uma forma de escrever as promises mais estrutural e sem a eternidade de *thenables* (Cadeia de .then(...).then(...)). Enquanto fazia um servidor, descobri a biblioteca [asyncawait](https://github.com/yortus/asyncawait), que possibilita a execução destes mesmo sem o ES7 e escrito com o JS existente. Veja uma função de pedido resumido com o uso.

```javascript
...
doPayment: req => {
  const { body } = req;
  const { productId, UserId, productQuantity, mercadoPago, shippingType } = body;
  const { doPayment } = require('../MercadoPago/mercadoPago.model');

  const shippingAmount = Number(body.shippingAmount.includes(',') ? body.shippingAmount.replace(',', '.') : body.shippingAmount);
  const shippingDays = Number(body.shippingDays);

  return startTransaction( async ( transaction => {

    let response;
    try {
      const user = await (sequelize.models.User.findById(UserId, { transaction }));

      const addresses = await (user.getAddresses());
      if (addresses.length !== 2 ) throw 'Complete o seu cadastro!';

      const product = await (Product.findById(productId, { transaction }));

      const canBuy = await (user.canBuyProduct(product, transaction));

      if (!canBuy) throw 'Você já está em processo de pagamento. Aguarde até 10 minutos e tente novamente.';

      const currentProduct = await (Product.getCurrentProductWithoutStatus(transaction));

      console.log(`Product Quantity -> ${productQuantity}`);
      console.log(`Product -> ${product.id} | currentProduct -> ${currentProduct.id}`);

      if (productQuantity < 0 || productQuantity === undefined) throw 'Quantidade de produtos inválido!';
      if (productQuantity > currentProduct.purchaseLimit) throw 'Quantidade selecionada maior do que o limite por usuário';
      if (_.isEmpty(currentProduct) || product.id !== currentProduct.id) throw 'Parece que este produto não está mais ativo';
      if (currentProduct.stockQty <= 0) {
        const isAbleToQueue = await (user.isAbleToQueue(currentProduct, transaction));

        if (!isAbleToQueue) throw 'Todos os produtos foram vendidos. Você já está na fila de espera, aguarde para ser notificado caso haja desistência (:';
        else throw 'sendNotification';
      }

      response = await (doPayment(mercadoPago));
      const status = response.status;
      const billingTrackingNumber = response.id;

      const userStatistics = await (sequelize.models.UserStatistics.getByUser(user, transaction));

      const analytics = await (sequelize.models.Analytics.getTodaysAnalytics(transaction));

      //passou nas validações -> criar order
      const newOrder = Object.assign(
        {},
        _.omit(body, ['productQuantity', 'productId', 'mercadoPago', ]),
        { totalAmount: parseFloat((currentProduct.discountedPrice * productQuantity) + shippingAmount) },
        { paymentTime: new Date() }, { inQueue: false }, { billingTrackingNumber }
      );

      const order = await (Order.create(newOrder, { transaction }));

      const orderItem = {
        OrderId: order.id,
        ProductId: product.id,
        productQuantity: productQuantity
      };

      const createdOrderItem = await (OrderItem.create(orderItem, { transaction }));

      const orderStatus = await (OrderStatus.getByDescription(status, { transaction }));

      await (order.setOrderStatus(orderStatus.id, { transaction }));

      if (status !== 'rejected') {
        // Decrement no estoque
        await (currentProduct.decrement('stockQty', { transaction, by: productQuantity }));
        // Incrementando o total de compras
        await (currentProduct.increment('totalBought', { transaction, by: productQuantity }));
        // Incrementando o total economizado pelo user
        await (user.increment('totalSaved', { transaction, by: Number(currentProduct.storePrice - currentProduct.discountedPrice)}));
        // Incrementando o total compartilhado pelo user
        await (user.increment('totalBought', { transaction, by: productQuantity }));
        // Incrementando o total de compras do user
        await (userStatistics.increment('totalBought', { transaction, by: productQuantity }));
        // Incrementando o total de compras no total
        await (analytics.increment('totalBought', { transaction, by: productQuantity }));
        await (analytics.increment('totalAmount', { transaction, by: order.totalAmount }));
      }

    } catch (err) {
      console.log(err);
      throw err;
    }
    // Se o response vier com sucesso, commit no transaction, senão 'throw err';
    return response;
  }));
}
...
```

É apenas necessário colocar o escopo atual dentro do `async` e então as funções que retornam promises dentro do `await` para deixar o código um pouco mais legível.

> Lembre-se que com o Babel dá para fazer isto sem a ajuda de bibliotecas. [Veja](http://discuss.babeljs.io/t/using-es7-features-async-await/65)

#### Referências

Novamente, o site [developer.mozilla](https://developer.mozilla.org) é sempre uma boa fonte para pesquisa, assim como o blog do grande [Willian](https://willianjusten.com.br/), que é um dos maiores (se não o maior) de frontend do Brasil.
