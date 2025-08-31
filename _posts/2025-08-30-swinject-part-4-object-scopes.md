---
title: "Swinject Part III — Escopos de Objetos (Object Scopes)"
date: 2025-07-30
tags: [Swinject, Dependency Injection, Swift, iOS, DI Container]
---

# Swinject Part III — Escopos de Objetos (Object Scopes)

Este é o terceiro vídeo da série sobre Swinject.  
Neste artigo vamos falar sobre **escopos de objetos** (*Object Scopes*), que definem o ciclo de vida das instâncias criadas pelo Swinject.

## Sumário

- [O que são Object Scopes?](#o-que-são-object-scopes)
- [Tipos de Escopos no Swinject](#tipos-de-escopos-no-swinject)
  - [Transient](#transient)
  - [Graph](#graph)
  - [Container](#container)
  - [Weak](#weak)
  - [Custom](#custom)
- [Exemplo prático com Container (Singleton)](#exemplo-prático-com-container-singleton)
- [Dependências compartilhadas entre múltiplos objetos](#dependências-compartilhadas-entre-múltiplos-objetos)
- [Cuidados com dependências circulares](#cuidados-com-dependências-circulares)
- [Dicas finais](#dicas-finais)
- [Conclusão](#conclusão)

---

## O que são Object Scopes?

No Swinject, o *scope* define **o tempo de vida de uma instância** resolvida.  
Quando você pede ao container uma dependência, ele pode:

- Criar uma nova instância a cada resolução  
- Reutilizar uma instância já existente  
- Controlar a vida útil de acordo com o escopo definido  

---

## Tipos de Escopos no Swinject

### Transient
- Cria uma nova instância **sempre que solicitado**.  
- Vida curta.  
- ⚠️ Não funciona bem com **dependências circulares**.  

```swift
container.register(DriverProtocol.self) { _ in Driver(name: "Julio") }
    .inObjectScope(.transient)
```

---

### Graph
- Padrão do Swinject.  
- Vida curta, mas **compartilhada dentro de uma mesma resolução**.  
- Evita criar a mesma dependência repetidamente durante a construção da árvore de objetos.  

---

### Container
- Vida longa: a instância vive enquanto o container existir.  
- Permite criar **Singletons** facilmente.  

```swift
container.register(DriverProtocol.self) { _ in Driver(name: "Julio") }
    .inObjectScope(.container)
```

---

### Weak
- A instância vive **enquanto houver alguém usando**.  
- Se não houver referências fortes, o objeto é desalocado.  

---

### Custom
- Permite criar um controle manual da vida útil do objeto.  
- Mais avançado — não abordado neste post.

---

## Exemplo prático com Container (Singleton)

```swift
let container = Container()

container.register(DriverProtocol.self) { _ in Driver(name: "Julio Cesar") }
    .inObjectScope(.container)

let driver1 = container.resolve(DriverProtocol.self)!
let driver2 = container.resolve(DriverProtocol.self)!

print(driver1 === driver2) // true → mesma instância
```

> Sempre que pedirmos `DriverProtocol`, será retornada **a mesma instância**, pois o escopo foi definido como `.container`.

---

## Dependências compartilhadas entre múltiplos objetos

Se duas classes (`Car` e `Bus`) dependem de `DriverProtocol`, e o escopo do driver for `.container`, ambos receberão **o mesmo motorista**:

```swift
container.register(Car.self) { resolver in
    Car(driver: resolver.resolve(DriverProtocol.self)!)
}

container.register(Bus.self) { resolver in
    Bus(driver: resolver.resolve(DriverProtocol.self)!)
}
```

Alterar o nome do motorista no `Car` refletirá no `Bus`, pois ambos compartilham a mesma instância.

---

## Cuidados com dependências circulares

- **Problema:** no escopo `.transient`, dependências circulares causam **loop infinito**.  
- Exemplo: `A` depende de `B` e `B` depende de `A`.  
- Como `.transient` não guarda cache, cada criação gera outra instância indefinidamente.  

> Se precisar de dependências circulares, evite `.transient` ou reestruture seu design.

---

## Dicas finais

- Escopos ajudam a:
  - Evitar criação desnecessária de instâncias  
  - Melhorar a performance  
  - Controlar ciclos de dependência  
  - Gerenciar memória de forma mais eficiente  

---

## Conclusão

No Swinject, os **object scopes** são essenciais para definir o ciclo de vida das instâncias.  
Os principais são:

- **Transient** → nova instância sempre  
- **Graph** → compartilhado apenas durante a resolução  
- **Container** → Singleton  
- **Weak** → vive enquanto houver referências  
- **Custom** → controle manual  

Teste cada um conforme sua necessidade e **leia a documentação oficial** para aprofundar.

---

*Publicado por Julio Cesar — Café do Jovem Stark*
