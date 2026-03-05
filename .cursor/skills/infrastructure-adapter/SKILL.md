---
name: infrastructure-adapter
description: Implementa ou altera repositórios, provedores, mappers e configuração de infraestrutura no projeto DataFeed. Use ao criar ou editar código em DataFeed.Infrastructure (repositórios, MongoDB, AWS, HTTP, filas).
---

# Infrastructure — DataFeed.Infrastructure

## Contexto

- Projeto: `DataFeed.Infrastructure` (referencia Application e Domain).
- Implementa interfaces de repositórios (Domain) e abstrações de Application (eventos, factories, adaptadores).
- Registro de serviços em `DependencyInjection.cs` (e extensões de módulo, ex.: `AddCorporateActionModule`).

## Repositórios

- Nome com sufixo `Repository` (ex.: `RawCorporateActionRepository`).
- Implementar interface do Domain (ex.: `IRawCorporateActionRepository`).
- Injetar configuração de banco (ex.: MongoDB) e clientes prontos (ex.: `IMongoClient`), sem instanciar conexões "na unha".
- Métodos assíncronos com `CancellationToken` repassado para o driver.

## Registro (Scrutor)

- Repositórios:
  - `AddClasses(c => c.Where(t => t.Name.EndsWith("Repository") && !t.IsAbstract)).AsMatchingInterface().WithScopedLifetime();`
- Validators:
  - `AddClasses(c => c.AssignableTo(typeof(IValidator<>))).AsImplementedInterfaces().WithScopedLifetime();`
- Módulos:
  - Métodos de extensão (ex.: `AddCorporateActionModule`) que fazem scan por interfaces (`AssignableTo<ICorporateActionCollector>()`, etc.).

## Banco de dados

- MongoDB: usar `MongoConfiguration` e tipos de configuração próprios; Domain não conhece detalhes de persistência.
- Persistência: mapear entidades de Domain para coleções/documentos na Infrastructure.

## AWS, filas e HTTP

- SQS, Secrets Manager: via SDK e configuração (`GetAWSOptions()`); encapsular em abstrações (ex.: `IQueue`, `ISecretProvider`).
- HTTP clients: `AddHttpClient` com políticas de resiliência; interfaces em Application/Domain; implementação concreta em Infrastructure.

## Logs e métricas

- Injetar `Tools.Logging.Generics.ILogger` em adaptadores que podem falhar (HTTP, filas, secrets, etc.).
- Em falhas: usar `LogFormatter` + `LogStatus.Error` com contexto (Source, Detail, etc.).
- Em operações de I/O relevantes: envolver o bloco em `ExecutionTimeMetric` (ver skill `logging-metrics`).

## Regras de arquitetura

- Infrastructure não referencia Presentation.
- Não colocar regra de negócio em repositórios; apenas acesso a dados e mapeamento.
- Novos repositórios/factories devem ser descobertos via Scrutor ou registrados explicitamente no módulo correspondente.

