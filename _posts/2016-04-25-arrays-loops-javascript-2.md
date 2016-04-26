---
layout: post
title: Arrays e loops em Javascript [Parte II]
excerpt: Funções puras e um inicio em programação funcional
categories: [desenvolvimento]
tags: [javascript]
comments: true
---

## Introdução

Decidi enquadrar um pouco de programação funcional neste post sobre `arrays` pois nada mais justo do que mostrar o outro lado da força dos loops. A princípio, há alguns conceitos básicos para aprender: Funções puras, valores imutáveis, funções de primeira classe, recursividade e separação/modularização de responsabilidades. É importante lembrar que há um grande *gap* entre orientação à objetos e a herança clássica. Com isto em mente, vamos entender o que isto quer dizer.

### O conceito

Programação funcional contém muitas palavras grandas e bonitas, porém o conceito é relativamente simples: Ao receber um valor em uma função, o resultado tem de ser previsível e constante, ou seja, sempre será igual não importa quantas vezes for chamado. Esta forma de organizar o código mantém o programa limpo e prático, inclusive na hora da documentação. Um simples exemplo pode ser colocado na seguinte forma:

``` javascript
  let num = 1;
  function impure () {
    return num++;
  }

  impure(); // 2
  impure(); // 3..

  let numTwo = 1;
  function pure (x) {
    return x * 2
  }
  pure(numTwo); // 2
  pure(numTwo); // 2
  pure(numTwo); // 2.........
```

Conforme mencionado, funções sem efeito colateral trazem uma beleza ao código e facilidade de leitura que vale o esforço de aprendizado. Acima, a função `impure` mantém o estado de `num`, onde a cada vez que ela é chamada o resultado é diferente. Já o `impure` pode ser chamado infinitas vezes que será previsível. Isto é apenas o início.

Você deve ter percebido que funções puras contém pelo menos um parâmetro que funciona como um filtro. Ao receber um tipo de valor ocorre uma transformação e um novo valor é retornado sem modificar o valor inicial. Entretanto, não é legal assumir que somente parâmetros são puros e ponto final.

#### Não manter estado (Stateless) e Funções de primeira classe (First class functions)

``` javascript
  let one = 1;
  function gotcha (x) {
    x - 10; // Mudando estado
    return 10;
  }
  gotcha(one); // 10
  gotcha(one); // 10...

  function firstClass (x) {
    return function (y) {
      return x * y;
    }
  }
  const example = firstClass(10);
  console.log(example); // example = (y) => 10 * y;
  const answer = example(20);
  console.log(answer); // 200
```
A explicação para a ocorrência acima é bem simples. Em `gotcha`, a função está manipulando o estado de `x` mesmo a resposta sendo 10 para inúmeras chamadas. Já em `firstClass`, o retorno é outro função e isto é devido ao fato que funções são passadas como objetos em Javascript e podem ser atreladas como valores. O mesmo poderia ser chamado assim:

``` javascript
  firsClass(10)(200);

  // Em node isto é altamente comum
  const port = 1337;
  const mongodb = require('Mongodb')(port);
  mongodb.connect( () => console.log(`Connected on port ${port}!`)); // Connected on port 1337!
```

#### Valores imutáveis

Certamente, isto não é algo tão comum nem tão fácil de replicar para os mais novos devs JS. Objetos são criados e modificados sem piedade pois não há compiladores reclamando nem `index out of bounds` ocorrendo. Com isto, a segurança de um servidor pode se danificar absurdamente quando feito sem perceber. Claro, há libs como o [Immutable.js](https://facebook.github.io/immutable-js/) para ajudar com o fato, mas para situações mais simples não há necessidade. O próprio JS contém funções que ajudam com isso, mesmo que com certas limitações, sendo esta o `Object.freeze`. O `Object.freeze` impede que novas propriedades sejam adicionadas ou removidas e impede que a configuração via `Object.defineProperty` seja modificado. Só não esqueça de uma coisa: os valores das propriedades podem sim ser modificados, então na essência isto não é um objeto 100% `imutável`.


``` javascript
  'use strict';
  // Retorna o objeto posto imutável ou um novo objeto imutável
  var cantTouchThis = function (obj) {
    return Object.freeze(typeof obj === 'object' ? obj : {});
  }

  let immutable = cantTouchThis({
    inside: {
      name: 'Jonathan'
    }
  });
  immutable.inside.name = "John"; // Ok, pois somente se o objeto -> 'inside' também tomasse freeze que seria imutável
  immutable.age = 21 // TypeError
  immtuable.inside = [] // TypeError
  delete immutable.inside // TypeError
```

*Os objetos que tentam ser modificados fora do `use strict` não jogam erro em tempo de execução mas não modificam nada. Já ao usar o `use strict` o compilador o rejeito com `TypeError`. Também é importante notar que o nível de `freeze` é somente 1.*

#### Recursividade

Nada mais do que uma função chamando a mesma função declarada até que o resultado desejado aconteca. Simples e sem loops! Quem nunca teve que fazer um fatorial? Eu pelo menos achava um saco na escola.. Talvez fosse porque não conhecesse computação a fundos pois hoje acho 10/10. Isto faz parte de programação funcional pois nenhum tipo de loop é usado e sim chamadas infinitas e modularizadas. Bom, segue o exemplo:

``` javascript
function factorialize (num) {
  if (num === 1 || num === 0) return 1;
  else if (num === 2) return 2;
  else if (num > 2) {
    return num * factorialize(num - 1);
  }
}

const result = factorialize(5);
console.log(result); // 120
```

### Praticando

O grande [Eric Elliot](https://twitter.com/_ericelliott) publicou um [post](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976#.tfzxmqe5f) no medium onde a ideia era explciar um pouco sobre programação funcional com grande profundidade, e no final ainda tem um exercício bônus(*Este abaixo*) que ajuda bastante a fixar isto na mente. Seria ideal que vocês ao menos tentassem pois vale muito a pena. Para quem ainda não conseguiu, segue [minha forma resolvida do problema](https://gist.github.com/Quaggie/d4679ca5bff5a4dcb4c9).

<p data-height="266" data-theme-id="0" data-slug-hash="MyojLq" data-default-tab="result" data-user="ericelliott" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/ericelliott/pen/MyojLq/">MyojLq</a> by Eric Elliott (<a href="http://codepen.io/ericelliott">@ericelliott</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

## Recomendação

[Estes caras do freecodecamp](https://www.freecodecamp.com/) são uns monstros nos exercícios! Há dos mais básicos até o nível expert sem dó. Recomendo para todos querendo aprender/se aprofundar em Javascript.

[Este link](http://reactivex.io/learnrx/) é sinceramente as explicação mais absurda e bem escrito da internet na minha opinião. Nele há uma grande quantidade de exercícios seguidos de explicação em Javascript sobre programação funcional. Ao fazê-lo, você rapidamente pega o jeito de recursividade, funções puras e usabilidade em geral, e nisso eu dou a minha palavra. O autor deste post é o [Jafar Hussain](https://twitter.com/jhusain?lang=en), grande mestre de Javascript que trabalha na netflix. Recomendo fortemente seguir este ser humano no Twitter, Github, facebook, etc pois além de entendedor do assunto o cara é fera demais explicando. Para quem quiser ou tiver grana para gastar no aprendizado o [Frontend Masters](https://frontendmasters.com/) tem um curso de `Asynchronous Programming in Javascipt (With Rx.js Observables)` onde ele explica em detalhes tudo isto que dei uma breve introdução sobre. Realmente vale 100% do valor agregado neste curso, recomendo fortemente.

Por hoje é só. Um abraço!
