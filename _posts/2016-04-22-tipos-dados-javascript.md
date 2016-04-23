---
layout: post
title: Verificação de tipos em Javascript
excerpt: "O mundo === null"
categories: [javascript, desenvolvimento]
comments: true
---

# Primeiro post (:

Fala pessoal! Aqui segue o primeiro post do meu blog, onde será uma fonte de informações sobre desenvolvimento e também uma local onde eu consiga
recolher e transmitir qualquer tipo de conhecimento, seja ele profissional ou não.

Começaremos hoje com um assunto atípico e, na minha opinião, pouco explorado.

## Tipos de dados em Javascript

### Boolean

`Boolean` é um tipo primitivo, assim como o `null`, `undefined`, `Number`, `String` e o novo `Symbol`, introduzido no *ECMAScript 6*.
O tipo Boolean pode ser `true` ou `false`, portanto é o dado mais simples de entender em qualquer linguagem de programação.

{% highlight javascript %}
  const truthy = true;
  if (truthy) {
    // Esta chamada será executada
  }
  if (!truthy) {
    // Não será executado
  }
{% endhighlight %}
Acima, vimos um simples exemplo de um `if statement` onde o bloco interno só será chamado caso o `boolean` for `true`.
Por enquanto, vamos manter isto no famoso _KISS_ (Keep It Simple Stupid).

### Number

`Number` no Javascript é genérico. Não existe certamente um tipo `float`, `double` e `integer` como em linguagens como o **Java**, **C#**, **Objective-C** e afins.

{% highlight javascript %}
  console.log(typeof 1 === typeof 2.0); // true
{% endhighlight %}

Certamente fica muito confuso trabalhar e mexer com dados deste tipo, mas claro, sempre há uma saída com o JS.

{% highlight javascript %}
  const one = 1;
  const two = 2.0;

  console.log(parseInt(two)); // 2
  console.log(parseFloat(one)); // 1
  console.log(typeof parseFloat(one).toFixed(2)); // "string"
  console.log(Number(parseFloat(one).toFixed(2))); // 1
{% endhighlight %}

É, não necessariamente. O `toFixed(2)` apenas diz que o número deve ter 2 casas decimais, porém o transforma em tipo `string`..
Teóricamente o `parseFloat` e o `parseInt` deveriam transformar os números em `Floats` e `Integers`, e na maioria das vezes isto acontece
normalmente, mas há de se tomar um cuidado muito grande ao mexer com os mesmos.
Novamente, Há bibliotecas e frameworks para ajudar com isto, mas é importante realmente conhecer os efeitos colaterais da linguagem
e saber como melhorar estes acontecimentos inesperados.

### Null e Undefined

Parece tão simples. **`Null`** e `Undefined`. Tão indefesos e com tantos efeitos colaterais. Vamos ao que importa.

{% highlight javascript %}
const obj = null;
if (typeof obj === 'object') {
  console.log('Opa, cheguei');
}
{% endhighlight %}

Neste ponto, você deve estar pensando que esta linguagem é quebrada, e de certa forma eu concordo. Ao criarem a linguagem, os desenvolvedores
simplesmente esqueceram de consertar este bug e quando se deram conta já era tarde demais.

{% highlight javascript %}
  console.log( null == undefined) // true
  console.log( null === undefined) // false
{% endhighlight %}

[Neste link](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness 'Diferença dos iguais')
você consegue estudar a diferença entre `===` e `==`.
Uma vantagem do `null` é que ele pode ser passado como valor em chamadas onde um valor é necessário, diferente de `undefined`.

{% highlight javascript %}
  Number(null) // 0
  Number(undefined) // NaN
{% endhighlight %}

2 tipos extremamante similares e incrivelmente diferentes. A diferença entre eles é tão grande que há livros de grandes desenvolvedores
focados em explicar estes caras nos mínimos detalhes. [Este livro](http://shop.oreilly.com/product/0636920033745.do 'Types & Grammar - Kyle Simpson') aborda o tema com linguagem simples e termos práticos. Recomendo para todos, mesmo que você seja experiente.

### String

O tipo de dado mais fácil de entender para o ser humano pode ser o `String`, pois é usado para informações textuais. Na verdade, `String` é uma sequência de `char`(caractér único) onde a manipulação dos mesmos se torna muito rápido e de grande diversidade.

{% highlight javascript %}
  const name = 'Jonathan';

  console.log(name.substring(1, 5)); // "onat";
  console.log(name.split('').map( (n) => n + '-').join('')); // "J-o-n-a-t-h-a-n-"
{% endhighlight %}

Claro que as strings tem muita função inbutida no `protoype` da função `String` e há muito do que explorar. Vamos em frente.

### Object

Chegando ao fim, e não menos importante, o grande `Object`. Alguns dizem que tudo em Javascript se resume em Objecto no final das contas, porém
já vimos que não é bem assim que funciona.

> Uma função é um objeto que pode ser chamado e executado

Isto é uma frase 100% verdadeira, pois funções podem ser passadas como valores e referências enquanto na verdade são apenas objetos no fim das contas.

{% highlight javascript %}
  const oldObj = {name: 'Jonathan'};

  function generateObject(obj) {
    return Object.assign({}, obj);
  }

  const newObj = generateObject(oldObj);
  console.log(newObj == oldObj); // false
  console.log(newObj.name === oldObj.name); // true
{% endhighlight %}

O exemplo acima contém o novo `Object.assign`, que junta o primeiro parâmetro *{}* com o segundo parâmtro *obj* e retorna um novo objeto.
O `newObj` tem os mesmos valores que o oldObj, mas é uma referência nova.

`"Last but not least"`, `Array` herda de `object`, mas claro que não há *Orientação à objeto* em JS. `Array` é um tipo de lista onde valores são armazenados de forma não sequencial e podem guardar todos os tipos de dados no mesmo Array. Inclusive, `undefined` e `null` podem também aparecer como parte da lista e fazer parte da contagem ao executar um `length`.

{% highlight javascript %}
  const types = [1, 'Jonathan', {}, [], null, undefined];
  console.log(types.length) // 6

  // Apenas uma forma prática de limpar arrays.
  types = []; // Cria uma nova referência e gasta mais memória
  types.length = 0; // Limpa o array e mantém a referência, gastando menos memória
{% endhighlight %}

Particularmente, eu adotei Javascript como minha linguagem principal justamente por funções e objetos serem tão divertidos e diferentes das outras linguagens. `Closure`, `Scope` e `Hoisting` tem um grande impacto neles, mas isto é também um tema altamente complexo e que não entra na cabeça de primeira.

Recomendação
------
[Documentação oficial de Javascript do Mozilla ](https://developer.mozilla.org "Javascript Mozilla Documentation"). Sem dúvidas a melhor fonte de informação de Javascript.

Abordei os temas de forma simples e para tentar dar um gostinho de como JS pode ser interessante de se estudar.
Espero que tenham gostado. **Um abraço**!
