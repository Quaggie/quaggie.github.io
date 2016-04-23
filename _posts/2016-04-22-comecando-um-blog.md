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

### Null

Parece tão simples. **`Null`**. Tão indefeso e com tanto efeito colateral. Vamos ao que importa.

{% highlight javascript %}
  console.log(null); // "object"
{% endhighlight %}

Neste ponto, você deve estar pensando que esta linguagem é quebrada, e de certa forma eu concordo.

> Porque diabos programar em Javascript?

blblal

#### E também pode ser chamado assim
------
Podemos ver que o objective-c lbpblbalablballba
{% highlight objc %}
- (void)returnNothing {
  UIView *view = [[UIView alloc] init];
}
{% endhighlight %}
