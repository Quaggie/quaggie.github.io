---
layout: post
title: Unwrapping optionals com eficiência - Swift
excerpt: Swift não precisa ser complexo
categories: [desenvolvimento]
tags: [swift, ios]
comments: true
---

## Swift optionals

Quem migrou do Objective-C sem dúvidas tomou uma porradinha quando a Apple introduziu o Swift. Não por conta da dificuldade da linguagem, mas sim por conter sintaxes e conceitos diferentes. Um desses aprendizados novos pode ter sido com os "Optionals"

> __Optional__: Uma variável de qualquer tipo que pode ser nil, sendo verificado como sucesso ou falha.

``` swift
var number: Int? = nil

print("\(number)") // nil

number = 10
print("\(number)") // Optional(10)
print("\(number!)") // 10

```

No caso acima, a exclamação significa que o valor opcional será desembrulhado. Mas lembre-se, caso o valor com _'!'_ seja _nil_, o app irá dar crash!

É claro que isso foi pensado e tem algumas formas de resolver e tratar. Abaixo, vamos ver um exemplo com _guard_ e outro com _if let_.

### guard ..

O _guard_ declara uma variável de valor fixo de acordo com uma variável opcional para ser usado no código abaixo dele dentro do próprio escopo. Caso o _optional_ seja _unwrapped_, o código continua sendo executado, caso contrário cairá no _else_ do _guard_.

``` swift
class GuardExample {

    private var optional: String? = nil
    private var str: String = "Normal String"

    func canValidate () -> Bool {

        // Variável 'string' pode ser usado abaixo deste bloco se 'optional' for != nil
        // Caso seja nil, cairá no else e retorna 'false'
        guard let string = optional else {
            return false
        }

        print("The string is unwrapped! \(string)")
        print("The optional wrapped \(optional)")
        return true
    }
}

let guardExample = GuardExample()
guardExample.canValidate() // false
```

### if let ..

O _if let_ declara uma variável para ser usado somente dentro do escopo aberto, como abaixo.

``` swift
class IfLetExample {

    private let optional: String? = nil
    private var anotherOpt: String? = nil
    private var str: String = "Normal String"

    func canValidate () -> Bool {

        // Variável 'string' poderá ser usada do mesmo jeito que o 'guard', porém apenas dentro do escopo do 'if'
        if let string = optional {
            // O mesmo se aplica aqui para o 'anotherString'
            if let anotherString = anotherOpt {
                // ... E assim vai
                return true
            }
        }

        // A variável não existe neste escopo
        //print("The string is unwrapped! \(string)")
        return true
    }
}

let example = IfLetExample()
example.canValidate() // false
```

Não diria que tem forma melhor ou pior para dar _unwrap_ nos opcionais, mas sim situações de melhor caso de uso. O Swift chama muito a minha atenção por ser muito bem tipada e simples de ler/escrever.

Sendo uma linguagem orientada à objetos, não tem como fugir de certos conceitos, mas dá para brincar bastante com programação funcional e até criar sua própria estrutura, mesmo que a boa prática fuja do contexto.

No futuro farei posts falando sobre _infix operators_, pois acho uma das maiores inovações da linguagem.

#### Referências

Gosto muito da [documentação própria da apple](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/OptionalChaining.html) e do [blog do Ray Wenderlich](https://www.raywenderlich.com) para dúvidas e aprendizado em iOS. Claro que também é ótimo olhar como as libs são escritas, como por exemplo o [Gloss](https://github.com/hkellaway/Gloss/blob/develop/Sources/Operators.swift) que escreveu um _infix operator_ bem peculiar.

Fico por aqui. Um abraço!
