---
layout: post
title: Arrays e loops em Javascript
excerpt: Aprenda os diferentes tipos de loops e como utilizar Arrays em JS.
categories: [desenvolvimento]
tags: [javascript]
comments: true
---

## Intodrução

Um loop somente é possível se existir um `Array` em Javascript. Hoje, vamos discutir sobre como utilizar `Arrays` e fazer um bom uso do mesmo. Há uma grande discussão sobre como devemos inicializar um `Array` e como iremos chamá-lo para um uso adequado. Certamente, cada um tem sua opinião, porém há fatos que não podemos negar sobre mal uso. Começaremos com o exemplo abaixo utilizando o `new Array()`.

Douglas Crockford, um grande escritor sobre JS, costuma dizer para nunca usarmos nenhum tipo de construtor com `new` pois é má prática, a não ser que seja obrigatório como o `new Date()`. Neste caso eu concordo com ele pois há falhas na linguagem e na forma de utilizá-la. Vamos seguir para um exemplo.

#### Chamando um array com construtor quebrado

```javascript
  // Até aqui tudo bem
  const array1 = new Array(10, 20);
  console.log(array1.length) // 2
  console.log(arra1) // [10, 20];

  // Mas, o quê que houve?
  const array2 = new Array(10);
  console.log(array2.length); // 10 ???
  console.log(array2); // [undefined x 10] ???
```

Cair na armadilha de `new Array()` é bem comum para novos desenvolvedores Javascript, pois em outras linguagens como o `Java`, instanciar objetos é somente com `new`. Neste caso, como podemos ver, um novo `Array` é chamado indevidamente e ao receber somente um parâmetro ele entende como fosse para especificar o seu tamanho total. [Neste link](http://stackoverflow.com/questions/29260212/why-is-using-new-to-create-a-javascript-array-considered-bad/29260262#29260262) podemos ver que inicializando um novo Array com `new Array()` nunca é uma boa prática e devemos evitar a qualquer custo.

#### Como podemos melhorar isto?

```javascript
  const array1 = [10, 20];
  console.log(array1.length); // 2
  console.log(array1); // [10, 20]

  const array2 = [10];
  console.log(array2.length); // 1
  console.log(array2); // [10]
```

Sempre utilize a forma dinâmica `[]` para gerar um novo `Array` pois é o único jeito de utilizá-lo corretamente sem efeitos colaterais inesperados. Com isto em mente, vamos seguir em frente aos loops!

## Loops

#### `for`

O for parece complexo mas não é. Esta é a forma mais rápida e simples de lidar com loops, mesmo não sendo tão bonito de escrever. Esta expressão do `for` vem da linguagem `C`.

``` javascript
  for( /*expressão inicial*/, /*condicional*/, /*expressão incremental*/ ) {
    // Código
  }

  // Veja abaixo um exeplo práticos
  for (var i = 0; i < 5; i++) {
    console.log(i) // Em ordem: 0, 1, 2, 3, 4
  }
```

#### `do..while`

Para cada loop onde o `while` seja `truthy`, o que estiver dentro do bloco `do` é exutado.

``` javascript
  let i = 0;

  do {
    console.log(i);
    i++;
  } while(i < 5);
```

#### `while`

Este é bem simples, enquanto o que estiver dentro da função `while` for `truthy` o bloco em seguida é executado.

``` javascript
  let i = 0;

  while (i < 5) {
    console.log(i); // Em ordem: 0, 1, 2, 3, 4
    i++; // Aumentando o valor em +1 a cada iteração
  }
```

#### `for..in` e `for..of`

O `for..in` faz uma iteração nas propriedades do array, já o `for..of` faz uma iteração nos valores.

``` javascript
  let arr = [3, 5, 7];
  arr.foo = "teste";

  for (let i in arr) {
    console.log(i); // Em ordem: "0", "1", "2", "foo"
  }

  for (let i of arr) {
    console.log(i); // Em ordem: "3", "5", "7"
  }
```

#### `forEach`

Função já integrada na herança de `prototype`, ao inicializar um `Array` o mesmo já pode ser usado.

``` javascript
  let array = ['name1', 'name2', 'name3'];

  array.forEach( function (name) {
    typeof name === 'string' ? console.log(name.substring(1)) : console.log('Não é string'); // Em ordem: ['ame1', 'ame2', 'ame3']
  });
```

Particularmente, este é o meu favorito justamente pela sintaxe muito bonita, apesar de não ser o maior custo/benefício em memório. Afinal, este se mistura muito bem para finalizar loops com `chain`.

``` javascript
  let arr = [1, 2, 3, 4, 5];

  arr.map( num => num * 2 )
     .filter( num => num % 2 === 0)
     .forEach( num => {
      console.log(num); // Em ordem: 2, 4, 6, 8, 10
    });
```


### Finalizando

É importante notar que nenhum desses loops retorna algum valor por `default`, ou seja, são somente fuções facilitadoras para manipular `Arrays`. Futuramente iremos discutir sobre loops que `imutáveis` como o `map`, `reduce`, `filter` e afins. Um abraço!
