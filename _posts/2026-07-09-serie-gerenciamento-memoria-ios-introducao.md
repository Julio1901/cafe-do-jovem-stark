---
title: "Série sobre Gerenciamento de Memória no iOS: Por que um Programa Precisa Gerenciar Memória?"
date: 2026-07-09
categories: [iOS]
tags: [ios, swift, memory-management, arc, ram, chunking]
---

<div style="text-align:center;">
  <iframe
    width="560"
    height="315"
    src="https://www.youtube.com/watch?v=8T9d_KqOccw"
    title="YouTube video player"
    frameborder="0"
    allowfullscreen>
  </iframe>
</div>

Neste artigo vou iniciar uma série sobre **gerenciamento de memória no iOS**.

Se você acompanha o blog **Café do Jovem Stark**, sabe que gosto de estudar assuntos complexos dividindo-os em partes menores. É exatamente essa abordagem que utilizaremos ao longo desta série.

## O método de estudo

A metodologia que vamos utilizar é o **chunking**, que consiste em dividir um assunto grande em pequenos blocos de conhecimento (chunks).

Eu já costumava estudar dessa forma intuitivamente e, recentemente, descobri que existe uma definição formal para essa técnica. Não estou dizendo que seja a melhor metodologia para todas as pessoas, mas é a que mais funciona para mim. Por isso, ela será utilizada tanto nos meus estudos quanto nas aulas desta série.

Em vez de uma única aula enorme sobre gerenciamento de memória, teremos várias aulas curtas, cada uma focando em apenas um conceito.

Todos esses conceitos fazem parte do mesmo corpo de conhecimento, mas durante cada explicação vamos evitar entrar em outros assuntos para não perder o foco.

Isso ajuda bastante porque, quando estudamos um tema complexo, é muito fácil começar investigando um conceito e acabar desviando para diversos outros assuntos relacionados. Quando percebemos, estamos há dias estudando tópicos diferentes e esquecemos completamente o objetivo inicial.

Com o chunking, fazemos exatamente o contrário:

- dividimos o conhecimento em pequenas "caixas";
- estudamos uma caixa por vez;
- evitamos misturar conceitos;
- ao final, unimos todas as peças para compreender o assunto completo.

## O que vamos estudar

A série será composta pelos seguintes tópicos:

1. Por que um programa precisa gerenciar memória
2. O que é Heap
3. O que é uma referência (Reference) no Swift
4. O que significa um objeto estar vivo na memória
5. Quem decide quando um objeto deve morrer
6. O que é ARC (Automatic Reference Counting)
7. Como o ARC conta referências internamente
8. O que acontece quando a contagem chega a zero
9. O que é um Retain Cycle
10. Por que Retain Cycles causam Memory Leaks
11. O que são referências `strong`
12. O que são referências `weak`
13. O que são referências `unowned`
14. Quando usar `weak` e quando usar `unowned`
15. Closures também podem causar Memory Leaks
16. O que é uma Capture List (`[weak self]`)
17. Como identificar um Memory Leak
18. Como usar o Memory Graph do Xcode
19. Como o Instruments detecta vazamentos de memória
20. Boas práticas de gerenciamento de memória em projetos reais

À primeira vista parece muita coisa, mas, na realidade, apenas fragmentamos o assunto em partes menores para facilitar o aprendizado.

## Primeira pergunta: por que um programa precisa gerenciar memória?

Antes de falar sobre ARC ou qualquer outro conceito, vamos começar pela pergunta mais importante:

> **Por que um programa precisa gerenciar memória?**

Imagine um aplicativo de banco.

Ao abrir a tela inicial, o aplicativo cria diversos objetos:

- uma `HomeViewController`;
- listas de contas;
- botões;
- imagens;
- ícones;
- animações;
- diversos outros objetos necessários para exibir a interface.

Tudo isso ocupa memória RAM.

Como a memória RAM não é infinita, surge uma pergunta importante:

> O que deveria acontecer quando o usuário sai dessa tela e navega para outra?

## Cenário 1: nunca liberar memória

Imagine que nenhuma dessas informações seja removida da memória.

O usuário abre a Home.

Ela utiliza **30 MB**.

Depois abre outra tela.

Ela utiliza mais **40 MB**.

Depois acessa a tela de saldo.

Mais **25 MB**.

Depois abre o perfil.

Mais **50 MB**.

Se nenhuma dessas telas liberar os objetos que não são mais utilizados, toda essa memória continuará ocupada.

Teremos algo parecido com isto:

- Home → 30 MB
- Segunda tela → 40 MB
- Saldo → 25 MB
- Perfil → 50 MB

Mesmo que o usuário não esteja mais visualizando a Home, aqueles objetos continuam existindo.

Com o tempo, a memória vai sendo ocupada por objetos que já não têm mais utilidade.

Quando o limite de memória disponível for atingido, o iOS poderá encerrar o aplicativo, resultando em um **crash**.

> O número utilizado neste exemplo é apenas ilustrativo. O importante é entender que existe um limite de memória disponível e que, ao ultrapassá-lo, o sistema pode finalizar a aplicação.

## Cenário 2: liberar memória cedo demais

Agora imagine o extremo oposto.

O sistema remove um objeto antes da hora.

Por exemplo:

```swift
let user = User(name: "Julio")
```

Suponha que esse objeto ainda será utilizado para exibir uma mensagem como:

> Bem-vindo, Julio.

Se esse objeto for removido da memória antes desse momento, a aplicação tentará acessar algo que já não existe mais.

Isso pode causar:

- comportamentos indefinidos;
- falhas inesperadas;
- possíveis crashes, caso não haja o tratamento adequado.

Ou seja, remover objetos cedo demais também é um problema.

## O verdadeiro desafio

Perceba que os dois cenários são ruins.

Não liberar memória faz o aplicativo consumir RAM continuamente.

Liberar cedo demais faz o aplicativo perder informações que ainda seriam utilizadas.

No fim das contas, um programa precisa responder continuamente duas perguntas:

- Quando um objeto deve continuar existindo na memória?
- Quando ele pode ser destruído com segurança?

É exatamente isso que chamamos de **gerenciamento de memória**.

Em outras palavras, gerenciar memória significa manter os objetos vivos enquanto ainda são necessários e liberá-los assim que deixarem de ser utilizados, permitindo que novos objetos possam ocupar esse espaço na memória RAM.

## Conclusão

Nesta primeira parte da série entendemos apenas o problema que o gerenciamento de memória resolve.

Ainda não falamos sobre ARC, referências ou Memory Leaks. Antes disso, era importante compreender **por que esse gerenciamento existe** e qual problema ele resolve dentro de uma aplicação.

No próximo artigo, vamos estudar a segunda "caixinha" desse corpo de conhecimento:

> **O que é Heap?**