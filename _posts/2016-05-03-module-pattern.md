---
layout: post
title: Module pattern em JS
excerpt: Padrão de projeto utilizado para encapsular objetos
categories: [desenvolvimento]
tags: [javascript]
comments: true
---

## Module pattern

Este padrão tem o foco em encapsular e impedir que variáveis e funções sejam expostas para fora da `closure`. Com este encapsulamento, declarar variáveis dentro do módulo não terá impacto sobre o código e nada terá seu `hoisting` para o topo do script. Segue um caso de uso:

``` javascript
var tree = (function () {

  var apples = 1;
  var birds = 0;

  function getApples () {
    return apples;
  }

  function getBirds () {
    return birds;
  }

  function setBirds (num) {
    if (typeof num === 'number') {
      birds = num;
    } else {
      throw new TypeError('Número obrigatório');
    }
  }

  return {
    getApples: getApples,
    getBirds: getBirds,
    setBirds: setBirds
  };

})();

console.log(tree.apples); // undefined
console.log(tree.getApples()); // 1
console.log(tree.birds); // undefined
console.log(tree.setBirds(5));
console.log(tree.getBirds()); // 5
```

### Alternativa OOP

Para os programadores clássicos, há uma maneira de simular este padrão com orientação a objetos e contrutores. Segue o exemplo:

``` javascript
var Person = (function () {

  var age = 21; // Somente acessível pelos getters e setters

  function Secret (name) { // Construtor para 'new'
    this.name = name;
    Object.freeze(this.name);
  }

  Secret.prototype.init = function (name) { // Construtor para Object.create();
    if (this.name) return;
    this.name = name;
    Object.freeze(this.name);
  };

  Secret.prototype.getAge = function () { // Getter
    return age;
  };

  Secret.prototype.birthday = function () { // Modificando a idade
    age++;
  };

  return Secret;
})();

var guy = new Person('Jonathan');
console.log(guy.age); // undefined
console.log(guy.getAge()); // 21
guy.birthday();
console.log(guy.getAge()); // 22
console.log(guy.name); // "Jonathan"
guy.name = "John"; // error;

var girl = Object.create(Person.prototype);
girl.init('Rose'); // Inicializando o construtor com o nome
console.log(girl.name); // Rose
console.log(girl.age); // undefined
console.log(girl.getAge()); // 22 ??
girl.birthday();
console.log(girl.getAge()); // 23 ??

```

> A variável `age` mantém o valor mesmo com outra instância pois a `closure` mantém referência, diferente do `this`. Tome cuidado.

#### Referências e exemplos

O estudo e feito para este post tiveram grandes referências [deste livro](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#decoratorpatternjavascript) excelente sobre padrões em Javscript e [deste](https://www.nostarch.com/oojs) livro sobre orientação a objetos e padrões. Vale a leitura, mesmo os livros sendo um pouco antigos pois com um aprendizado em JS puro, não há framework que seja impossível de aprender. Um abraço!
