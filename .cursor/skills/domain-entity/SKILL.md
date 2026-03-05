---
name: domain-entity
description: Implementa ou altera entidades, value objects, enums e interfaces de repositório no Domain do projeto DataFeed.
---

# Domain — DataFeed.Domain

## Contexto

- Projeto: `DataFeed.Domain` (sem dependências de Application, Infrastructure ou Presentation).
- Estrutura: `Modules/<Module>/Entities/`, `Repositories/`, `Enums/`.

## Entidades

- Propriedades com getters apropriados; setters privados ou `init` quando for imutável.
- Construtor protegido ou privado quando houver herança (ex.: entidade base abstrata).
- Comportamento de negócio em métodos (ex.: `MarkAsProcessed()`, `LinkToAsset(Guid)`); evitar entidades anêmicas.
- Identificador: `Guid Id`; datas em UTC quando relevante (`CreatedAt`, etc.).
- Sem atributos de persistência (MongoDB, EF, etc.) no Domain; mapeamento fica em Infrastructure.

## Interfaces de repositório

- Nome: `I<Aggregate>Repository` (ex.: `IRawCorporateActionRepository`).
- Métodos assíncronos com `CancellationToken`.
- Listagens paginadas: retornar `(IEnumerable<T> items, int totalCount)` ou equivalente.

## Enums

- Namespace do módulo; nomes descritivos (ex.: `CorporateActionSource`, `RawCorporateActionStatus`).

## Regras de arquitetura

- Domain não referencia outros projetos da solução (exceto Shared, se houver tipos compartilhados).
- Nenhuma implementação concreta de repositório no Domain; apenas interfaces.
- Entidades representam regras de negócio; DTOs e contratos de API ficam em Application.

