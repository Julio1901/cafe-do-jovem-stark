---
title: "Swinject Part II — Padrões de Injeção de Dependência"
date: 2025-06-21
tags: [Swinject, Dependency Injection, Swift, iOS]
---

# Swinject Part II — Padrões de Injeção de Dependência

Quando falamos sobre injeção de dependência, existem alguns padrões diferentes que podemos usar. Neste post, vou listar **três padrões suportados pelo Swinject**, conforme a documentação oficial, com exemplos práticos em Swift.

## Sumário

- [Initializer Injection](#initializer-injection)
- [Property Injection](#property-injection)
- [Method Injection](#method-injection)
- [Conclusão](#conclusão)

---

## Initializer Injection

Neste padrão, você injeta as dependências **via construtor da classe**. Ou seja, ao instanciar o objeto, já passa a dependência esperada.

### Exemplo:

```swift
protocol OwnerProtocol {
    var name: String { get }
}

class Owner: OwnerProtocol {
    var name: String

    init(name: String) {
        self.name = name
    }
}

protocol CatProtocol {
    var owner: OwnerProtocol { get }
}

class Cat: CatProtocol {
    var owner: OwnerProtocol

    init(owner: OwnerProtocol) {
        self.owner = owner
    }
}
```

### Registro no container:

```swift
let container = Container()

container.register(OwnerProtocol.self) { _ in Owner(name: "Julio") }

container.register(CatProtocol.self) { resolver in
    let owner = resolver.resolve(OwnerProtocol.self)!
    return Cat(owner: owner)
}
```

### Resolução:

```swift
let cat = container.resolve(CatProtocol.self)
```

---

## Property Injection

Neste padrão, você instancia o objeto e **injeta a dependência diretamente em uma propriedade**. É útil para dependências opcionais ou para quando a instância ainda não está disponível no init.

### Exemplo:

```swift
protocol PetProtocol {
    var owner: OwnerProtocol? { get set }
}

class Dog: PetProtocol {
    var owner: OwnerProtocol?
}
```

### Registro no container:

```swift
container.register(PetProtocol.self) { resolver in
    let dog = Dog()
    dog.owner = resolver.resolve(OwnerProtocol.self)
    return dog
}
```

---

## Method Injection

Parecido com property injection, mas você injeta a dependência através de **um método próprio da classe**.

### Exemplo:

```swift
protocol PetProtocol2 {
    func inject(owner: OwnerProtocol)
}

class Cat2: PetProtocol2 {
    private var owner: OwnerProtocol?

    func inject(owner: OwnerProtocol) {
        self.owner = owner
    }
}
```

### Registro no container:

```swift
container.register(PetProtocol2.self) { resolver in
    let cat = Cat2()
    let owner = resolver.resolve(OwnerProtocol.self)!
    cat.inject(owner: owner)
    return cat
}
```

---

## Conclusão

Esses são os três principais padrões de injeção de dependência suportados pela documentação do Swinject:

- **Initializer Injection** (mais comum e direto)
- **Property Injection** (mais flexível para propriedades opcionais)
- **Method Injection** (mais explícito e controlado)

Nos próximos posts, vamos abordar outros tópicos de DI no iOS com Swift usando Swinject.

---

*Publicado por Julio Cesar - Café do Jovem Stark*
