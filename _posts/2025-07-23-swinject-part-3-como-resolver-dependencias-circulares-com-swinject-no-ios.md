---
title: "Swinject Part III — Como Resolver Dependências Circulares com Swinject no iOS"
author: Julio Cesar
date: 2025-07-23
tags: [iOS, Swinject, Dependency Injection, Swift]
---

Neste post, vamos entender o que são **dependências circulares** e como podemos resolvê-las usando **Swinject**, um framework de injeção de dependência no iOS. A explicação é baseada na documentação oficial e em exemplos práticos tirados de um projeto real.

## 📑 Sumário

1. [O que são dependências circulares?](#1-o-que-são-dependências-circulares)
2. [Por que o Swinject não consegue resolver via inicializador?](#2-por-que-o-swinject-não-consegue-resolver-via-inicializador)
3. [Como resolver com property injection + `initCompleted`](#3-como-resolver-com-property-injection--initcompleted)
4. [Exemplo 1: Driver e Car](#4-exemplo-1-driver-e-car)
5. [Exemplo 2: Capitão e Barco](#5-exemplo-2-capitão-e-barco)
6. [Atenção com recursão infinita](#6-atenção-com-recursão-infinita)
7. [Dica de performance](#7-dica-de-performance)
8. [Conclusão](#8-conclusão)

## 1. O que são dependências circulares?

Dependências circulares acontecem quando duas classes dependem uma da outra. Exemplo:

- `Motorista` tem uma propriedade do tipo `Carro`
- `Carro` tem uma propriedade do tipo `Motorista`

Esse ciclo gera um problema, porque uma precisa da outra para ser criada, e o Swinject não consegue resolver isso automaticamente via inicializadores.

## 2. Por que o Swinject não consegue resolver via inicializador?

O Swinject **não suporta** a resolução de duas dependências circulares **via construtor** (`init`) em ambas as classes. Isso causaria uma **recursão infinita**:

```swift
// Não permitido
init(motorista: Motorista)
init(carro: Carro)
```

A documentação deixa claro: pelo menos uma das dependências **precisa ser resolvida via propriedade** (`property injection`), e o ideal é usar o bloco `initCompleted`.

## 3. Como resolver com property injection + `initCompleted`

O `initCompleted` é um bloco executado **depois** que a instância da classe foi criada. É nele que conseguimos injetar dependências circularmente **sem causar loops infinitos**.

## 4. Exemplo 1: Driver e Car

Imagine este cenário:

- `Car` recebe `Driver` no **init**
- `Driver` tem uma propriedade `car`, que será injetada **depois**, via property injection

```swift
// Registro no container
container.register(Car.self) { r in
    Car(driver: r.resolve(Driver.self)!)
}

container.register(Driver.self) { _ in
    Driver() // sem carro ainda
}.initCompleted { r, driver in
    driver.car = r.resolve(Car.self)
}
```

Neste caso, o Swinject resolve o `Driver` primeiro, e só depois injeta o `Car` via propriedade, **evitando recursão infinita**.

## 5. Exemplo 2: Capitão e Barco

Neste exemplo, **ambas** as dependências são injetadas via propriedades.

```swift
// Barco depende de Capitão
container.register(Boat.self) { _ in
    Boat()
}.initCompleted { r, boat in
    boat.captain = r.resolve(Captain.self)
}

// Capitão depende de Barco
container.register(Captain.self) { _ in
    Captain()
}.initCompleted { r, captain in
    captain.boat = r.resolve(Boat.self)
}
```

Esse padrão é útil quando nenhuma das classes pode ser resolvida via `init` e evita acoplamento direto nos construtores.

## 6. Atenção com recursão infinita

Se você tentar injetar **ambas as dependências via construtor**, o Swinject vai entrar num ciclo como:

> Cria `Motorista` → precisa de `Carro` → cria `Carro` → precisa de `Motorista` → cria `Motorista` → e assim por diante...

Resultado: **crash por recursão infinita**. Por isso, o `initCompleted` é essencial nesse cenário.

## 7. Dica de performance

A documentação do Swinject recomenda que você:

> **Evite colocar operações custosas fora do `initCompleted`**, pois o Swinject pode executar a resolução mais de uma vez em casos de dependência circular.

Ou seja, tudo que for mais pesado, deixe para rodar dentro do `initCompleted`, garantindo que o código só será executado **após** a instância estar pronta e **apenas uma vez**.

## 8. Conclusão

Trabalhar com **injeção de dependência** e **ciclos** exige cuidado. O Swinject lida bem com isso, desde que você siga estas regras:

✅ Use `property injection`  
✅ Evite usar construtor em ambas as classes  
✅ Utilize `initCompleted` para injetar a segunda dependência  
✅ Mantenha o código leve fora do `initCompleted`

Se quiser ver esses exemplos em código real, confira o vídeo completo no YouTube.

## 🎥 Assista também meu vídeo sobre esse tema no Youtube

[📺 Clique aqui para assistir ao meu vídeo no YouTube](https://youtu.be/r_jDAQiyadQ?si=yBoO1hhoxU5pi55O)
