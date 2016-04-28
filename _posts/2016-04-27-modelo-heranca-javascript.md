---
layout: post
title: Um modelo de herança com Object.create em JS
excerpt: Uma das formas mais simples de lidar com objetos herdados
categories: [desenvolvimento]
tags: [javascript]
comments: true
---

## Prototypal inheritance

Javascript tem suas complicações de sintaxe e o monstruoso `new` apenas por jogo de marketing na época que o Java estava em alta. Mesmo o funcionamento sendo absolutamente diferente, desenvolvedores costumam achar que a herança e as instâncias podem funcionar iguais às do Java. Há um grande número de posts e artigos que explicam muito bem isto, inclusive [este aqui](http://javascript.crockford.com/prototypal.html) do Douglas Crockford(Post muito antigo) já dá para ter uma ideia do que estamos falando.

### E olha.. nenhum tipo de 'new'
``` javascript
  // Person constructor
  const Person = Object.create(null);
  Person.whoAmI = function () {
    return 'Person';
  };
  Person.name = "Person";
  Person.attributes = function () {
    return this;
  };
  Person.super = function() {
    return Object.getPrototypeOf(this); // Retorna o objeto superior à este
  };

  // Adult constructor
  const Adult = Object.create(Person);
  Adult.whoAmI = function () {
    return 'Adult';
  };

  // Child constructor
  const Child = Object.create(Adult);
  Child.whoAmI = function () {
    return 'Child';
  };

  // Baby constructor
  const Baby = Object.create(Child);
  Baby.whoAmI = function () {
    return 'Baby';
  };
  Baby.getName = function () {
    return this.name;
  };

  // john is an instance of Baby
  const john = Object.create(Baby);
  john.name = "John";
  john.whoAmI = function () {
    return 'John Smith';
  };
  console.log(john.name); // "John

  // lilJohn is an instance of john
  const lilJohn = Object.create(john);
  console.log(lilJohn.super().name); // "John"

  // janna is an instance of Baby
  const janna = Object.assign(Object.create(Baby), john);
  janna.name = "Janna";
  console.log(janna.name); // "Janna"
  console.log(janna.super().name) //"Person"

  console.log(Object.getPrototypeOf(janna) === Object.getPrototypeOf(john)); // true
  console.log(john.super().super().super().whoAmI()); // "Person"
  console.log(john.super().super().super().whoAmI()); // "Adult"
  console.log(john.super().super().whoAmI()); // "Child"
  console.log(john.super().whoAmI()); // "Baby"
  console.log(john.whoAmI()) // "John Smith
```

## Entenda o conceito e você irá longe

Estude o código acima.. Veja o que está acontecendo por trás dos panos. Eu garanto que não será tão assustado quanto parece. Neste caso de uso o `instanceof` e o atributo `prototype` explicitamente não são chamados, e nem precisa. O grande `Object.create` já toma conta disso tudo sem a gente ver.

#### [Referência do Doc](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create)
``` javascript
  // Polyfill do Object.create
  if (typeof Object.create != 'function') {
    Object.create = (function() {
      var Temp = function() {};
      return function (prototype) {
        if (arguments.length > 1) {
          throw Error('Second argument not supported');
        }
        if(prototype !== Object(prototype) && prototype !== null) {
          throw TypeError('Argument must be an object or null');
       }
       if (prototype === null) {
          throw Error('null [[Prototype]] not supported');
        }
        Temp.prototype = prototype; // Uso do prototype
        var result = new Temp(); // Uso do new
        Temp.prototype = null;
        return result;
      };
    })();
  }
```

### Orientação a objetos!

Afinal de contas, a linguagem dinâmica consegue sim replicar os conceitos teóricamente de outro mundo de outras linguagens. Leia bastante livros, siga os gurus nas redes sociais e veja muito código no Github. Com o tempo, isto virá na cabeça com um estalo de dedos. Um abraço!
