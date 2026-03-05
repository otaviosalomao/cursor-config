---
name: use-case
description: Implementa ou altera casos de uso (use cases) no projeto DataFeed. Use ao criar ou editar use cases em DataFeed.Application, requests, validators ou respostas paginadas (PagedResult).
---

# Use Case — DataFeed Application

## Contexto

- Projeto: `DataFeed.Application` (referência a `DataFeed.Domain` e `DataFeed.Shared`).
- Use cases ficam em `Modules/<Module>/UseCases/<Feature>/<Action>/`.
- Interfaces em Application; implementações `internal sealed`.

## Interfaces de use case

Escolher uma conforme o fluxo:

- `IUseCaseWithRequestAndResult<TRequest, TResult>` — request + retorno (ex.: listagem paginada).
- `IUseCaseWithOnlyRequest<TRequest>` — request sem retorno (ex.: comando que retorna 202 Accepted).
- `IUseCaseWithOnlyResult<TResult>` — sem request, só retorno.

Exemplo de interface:

```csharp
public interface IGetManyRawCorporateActionsUseCase
    : IUseCaseWithRequestAndResult<GetManyRawCorporateActionsRequest, PagedResult<IEnumerable<JsonElement>>> { }
```

## Estrutura de arquivos por ação

- `GetMany<Entity>Request.cs` — propriedades de filtro/paginação.
- `GetMany<Entity>RequestValidator.cs` — FluentValidation.
- `GetMany<Entity>UseCase.cs` — interface + implementação no mesmo arquivo (classe `internal sealed`).

## Implementação do use case

1. Injetar no construtor: repositórios (interfaces do Domain), `IValidator<TRequest>`, e `ILogger` (Tools.Logging.Generics) para logs/métricas quando aplicável.
2. No início de `ExecuteAsync`: `await ValidationHelper.ValidateAndThrowAsync(request, _requestValidator, cancellationToken);`
3. Chamar repositórios/services; montar `PagedResult` ou response quando for listagem.
4. Retornar resultado; não capturar exceções para "esconder" erros.
5. Para fluxos com I/O, usar logging/métricas conforme a skill `logging-metrics`.

## Paginação

- Repositório retorna `(IEnumerable<T> items, int totalCount)`.
- Calcular `TotalPages`, `HasNext`, `HasPrevious` e instanciar `Pagination` e `PagedResult<T>`.

## Regras de arquitetura

- Application não referencia Infrastructure ou Presentation.
- Não instanciar repositórios diretamente; sempre recebidos por construtor.
- Passar sempre `CancellationToken` para chamadas assíncronas.

