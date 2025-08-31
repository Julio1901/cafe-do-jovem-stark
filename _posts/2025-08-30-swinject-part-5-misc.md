---
title: "Swinject VI - Misc: Self-registration, Value Types & Resolution Failure Logging"
date: 2025-08-30
categories: [Swinject, iOS, Swift]
tags: [Swinject, Dependency Injection, Swift, iOS, DI Container]
---

# Swinject VI - Misc: Self-registration, Value Types & Resolution Failure Logging

Neste post, vamos explorar algumas funcionalidades avançadas do **Swinject**, incluindo:

- Tipos de valor (Value Types)  
- Self-registration  
- Logging de falha na resolução (Resolution Failure Logging)

> Observação: Esta é uma continuação de uma série sobre Swinject. Recomendo conferir a documentação oficial para detalhes mais completos.

---

## Sumário

1. [Value Types](#value-types)  
2. [Self-registration](#self-registration)  
3. [Resolution Failure Logging](#resolution-failure-logging)

---

## Value Types

O Swinject não trabalha apenas com **tipos de referência** (classes), mas também com **tipos de valor** (structs). Isso permite que você registre structs diretamente no container e ele as resolva normalmente.

Por exemplo:

```swift
protocol Animal {
    var name: String { get set }
}

struct Turtle: Animal {
    var name: String
}

let container = Container()
container.register(Animal.self) { _ in Turtle(name: "Leonardo") }

let turtle = container.resolve(Animal.self)
print(turtle?.name) // "Leonardo"
```

> Ao criar uma nova instância a partir da primeira, você terá uma **cópia** da struct, e não uma referência compartilhada como acontece com classes.

---

## Self-registration

O Swinject também permite **self-registration**, ou seja, você pode registrar uma classe concreta sem precisar de um protocolo. A própria classe faz o papel de serviço.

Exemplo:

```swift
class PetOwner {
    var name: String = "Julio"
}

let container = Container()
container.register(PetOwner.self) { _ in PetOwner() }

let owner = container.resolve(PetOwner.self)
print(owner?.name) // "Julio"
```

> Você não precisa obrigatoriamente criar um protocolo para cada serviço. O self-binding simplifica o registro de classes concretas.

---

## Resolution Failure Logging

O Swinject fornece um **log padrão de falhas de resolução**, exibido no terminal. É possível:

- **Desabilitar o log**
- **Criar uma função de log customizada**

Exemplo de desativação do log:

```swift
let container = Container()
container.loggingFunction = nil
```

> Atenção: desabilitar o log não é recomendado em produção, pois você perde informações importantes sobre problemas de resolução.

---

Este post abordou funcionalidades intermediárias e avançadas do Swinject que podem ajudar a simplificar a configuração de containers e melhorar o controle sobre instâncias de tipos de valor, classes e logs de resolução.  

Para mais detalhes, confira a [documentação oficial do Swinject](https://github.com/Swinject/Swinject).
