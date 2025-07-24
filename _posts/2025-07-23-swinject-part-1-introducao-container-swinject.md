---
title: "Swinject Part I — Introdução ao Container no Swinject: Conceitos e Exemplos Básicos"
date: 2025-07-24
tags: [Swinject, Dependency Injection, Swift, iOS, DI Container]
---

# Swinject Part I — Introdução ao Container no Swinject: Conceitos e Exemplos Básicos

Este é o primeiro vídeo de uma série onde vamos explorar o Swinject, um framework para injeção de dependências (Dependency Injection - DI) no Swift. Nesta publicação, abordarei o conceito do container no Swinject e como ele facilita o gerenciamento de dependências em projetos iOS.

## Sumário

- [O que é Injeção de Dependências (DI)?](#o-que-é-injeção-de-dependências-di)
- [Princípio da Inversão de Dependência (DIP) e sua relação com DI](#princípio-da-inversão-de-dependência-dip-e-sua-relação-com-di)
- [O Container no Swinject](#o-container-no-swinject)
- [Terminologia do Swinject](#terminologia-do-swinject)
- [Exemplos Básicos de Uso do Container](#exemplos-básicos-de-uso-do-container)
- [Registro com Nome (Named Registration)](#registro-com-nome-named-registration)
- [Registro com Argumentos](#registro-com-argumentos)
- [Cuidados e Considerações Finais](#cuidados-e-considerações-finais)
- [Próximos Passos](#próximos-passos)

---

## O que é Injeção de Dependências (DI)?

Injeção de dependências é um padrão de design usado para melhorar modularidade, testabilidade e manutenção do código. Ao invés de uma classe criar suas próprias dependências, elas são fornecidas externamente por um gerenciador, o que evita acoplamento direto entre classes.

---

## Princípio da Inversão de Dependência (DIP) e sua relação com DI

O DIP, um dos princípios do SOLID, diz que módulos de alto nível não devem depender de módulos de baixo nível, ambos devem depender de abstrações. Ou seja, uma classe deve depender de interfaces (protocolos) e não de implementações concretas. Isso facilita a troca de implementações e manutenção do código.

---

## O Container no Swinject

No Swinject, o *container* é a classe responsável por gerenciar as dependências. Ele funciona como um "estoque" onde você registra suas classes e associa protocolos a implementações concretas. Quando necessário, o container resolve essas dependências automaticamente, entregando instâncias configuradas, sem que você precise criar manualmente os objetos no seu código.

Vantagens:

- Reduz acoplamento entre classes  
- Facilita a manutenção e troca de implementações  
- Auxilia na escalabilidade  
- Facilita o uso de mocks para testes  

---

## Terminologia do Swinject

| Termo       | Definição                                       |
|-------------|------------------------------------------------|
| Service     | Protocolo que define a interface de uma dependência |
| Component   | Implementação concreta que conforma o protocolo |
| Factory     | Função que cria/configura as instâncias         |
| Container   | Coleção que armazena instâncias e regras de injeção |

---

## Exemplos Básicos de Uso do Container

Imagine que temos um protocolo `Vehicle` e uma implementação concreta `Car`.

No container, você registra que toda vez que alguém pedir `Vehicle`, deve receber uma instância de `Car`:

```swift
container.register(Vehicle.self) { _ in Car(name: "Honda Civic") }
```

Para resolver a dependência:

```swift
let vehicle = container.resolve(Vehicle.self)
print(vehicle?.name) // Honda Civic
```

Outro exemplo, registrando um protocolo `Person` para retornar um `Driver`:

```swift
container.register(Person.self) { resolver in
    Driver(vehicle: resolver.resolve(Vehicle.self)!)
}
```

Aqui, a dependência aninhada é resolvida automaticamente — o `Driver` precisa de um `Vehicle`, que o container já sabe criar.

---

## Registro com Nome (Named Registration)

Se você tem múltiplas implementações do mesmo protocolo e quer diferenciá-las, pode usar um registro com nome:

```swift
container.register(Drink.self, name: "coffee") { _ in Coffee() }
container.register(Drink.self, name: "tea") { _ in Tea() }
```

Ao resolver, especifique o nome para obter a implementação desejada:

```swift
let coffee = container.resolve(Drink.self, name: "coffee")
let tea = container.resolve(Drink.self, name: "tea")
```

---

## Registro com Argumentos

Quando você quer passar parâmetros na criação da instância, o Swinject permite:

```swift
container.register(Product.self) { (_, name: String, sold: Bool) in
    Book(name: name, sold: sold)
}
```

Na resolução, passe os argumentos:

```swift
let book = container.resolve(Product.self, arguments: "Swift Programming", true)
```

> **Dica:**  
> - Use `argument` para um único parâmetro e `arguments` para múltiplos.  
> - O container pode lançar erro se não conseguir resolver a dependência, por isso geralmente é necessário usar `!` ao acessar o resultado.

---

## Cuidados e Considerações Finais

- Ao registrar dependências com mesmas chaves (mesmo protocolo, nome e argumentos), o último registro sobrescreve o anterior.  
- Tenha atenção para evitar conflitos que possam causar erros difíceis de detectar.

---

## Próximos Passos

Nos próximos vídeos/postagens, aprofundaremos em outros tópicos do Swinject, como escopos de instância, ciclos de vida, e integraremos exemplos mais complexos para projetos reais.

---

Se ficou alguma dúvida ou quer sugestões sobre o próximo tema da série, deixe um comentário!

---

**Julio Cesar**

---

*Este conteúdo é baseado em uma série de vídeos explicativos sobre Swinject, Dependency Injection e boas práticas no desenvolvimento iOS com Swift.*
