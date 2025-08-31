---
title: "Série Git part I - Estrutura do Git: Working Directory, Staging Area e Repository"
date: 2025-08-30
categories: [Git, Versionamento]
tags: [Git, Versionamento]
---

# Série Git - Estrutura do Git: Working Directory, Staging Area e Repository

Neste post, iniciamos uma série sobre **Git**, abordando:

- O que é o Git e para que serve
- Como funciona de forma distribuída
- Estrutura básica: Working Directory, Staging Area e Repository
- Como o Git armazena commits e snapshots

> Esta é uma introdução conceitual. Exemplos de comandos são ilustrativos, sem detalhar cada opção.

---

## Sumário

1. [O que é o Git](#o-que-e-o-git)
2. [Sistemas Centralizados x Distribuídos](#sistemas-centralizados-x-distribuidos)
3. [Estrutura do Git](#estrutura-do-git)
   - [Working Directory](#working-directory)
   - [Staging Area](#staging-area)
   - [Repository](#repository)
4. [Snapshots e Hashes](#snapshots-e-hashes)

---

## O que é o Git

O Git é um **sistema de controle de versionamento distribuído**. Ele permite que cada desenvolvedor tenha uma cópia completa do projeto, incluindo histórico, permitindo trabalhar offline e sincronizar alterações posteriormente.

---

## Sistemas Centralizados x Distribuídos

- **Centralizado:** Um servidor único mantém todos os arquivos e histórico. Se o servidor ficar indisponível, ninguém consegue trabalhar.
- **Distribuído (Git):** Cada desenvolvedor possui uma cópia completa do projeto e histórico. É possível trabalhar offline e sincronizar alterações com o servidor depois.

---

## Estrutura do Git

O Git possui três áreas principais:

### Working Directory

É a pasta onde você edita os arquivos. Modificações aparecem aqui primeiro.

```bash
# Verificar status dos arquivos
git status
```

Arquivos modificados aparecem destacados, indicando que foram alterados mas ainda não preparados para commit.

### Staging Area

Também chamada de **index**, funciona como uma **sala de espera**. Você escolhe quais arquivos irão para o próximo commit.

```bash
# Adicionar arquivos para a área de preparação
git add file.txt
```

### Repository

O **repositório** é o banco de dados local do Git, onde os commits são gravados. Contém histórico, branches e todos os snapshots do projeto.

```bash
# Criar um commit com mensagem
git commit -m "Mensagem do commit"
```

Fluxo resumido: **Working Directory → Staging Area → Repository**

---

## Snapshots e Hashes

O Git não salva apenas diferenças entre arquivos, mas **snapshots** completos do projeto:

- Cada commit é uma foto do estado do projeto.
- Arquivos não alterados reapontam para hashes existentes, economizando espaço.
- Apenas arquivos modificados recebem novos hashes.

Exemplo simplificado:

| Commit | Arquivo       | Hash  |
|--------|---------------|-------|
| 1      | hello.txt     | H1    |
| 1      | main.c        | H2    |
| 2      | hello.txt     | H3    |
| 2      | main.c        | H2    |  ← main.c não mudou, reaponta para H2

Isso torna o Git **eficiente, rápido e confiável** para versionamento.

---

Este post foi uma introdução à série de Git e controle de versionamento. Nos próximos vídeos e posts, abordaremos comandos avançados, branches, merges e workflows mais detalhados.

---

*Publicado por Julio Cesar - Café do Jovem Stark*
