---
name: api-controller
description: Implementa ou altera controllers da API no projeto DataFeed. Use ao criar ou editar controllers em DataFeed.Presentation.API, rotas versionadas ou documentação OpenAPI de endpoints.
---

# Controller — DataFeed Presentation API

## Contexto

- Projeto: `DataFeed.Presentation.API`.
- Controllers em `Controllers/Modules/<Module>/v1/`.
- Prefixo global de rota: `api` (via `RoutePrefixConvention`).

## Estrutura do controller

- Atributos:
  - `[ApiController]`
  - `[Route("v1/<recurso>")]`
  - `[Tags("<Module>")]`
  - `[Authorize]` quando a rota for protegida.
  - `[ApiExplorerSettings(GroupName = "v1")]` para OpenAPI.
- Herdar `Controller` ou `ControllerBase`.

## Endpoints

- Injetar apenas use cases via `[FromServices] I<Nome>UseCase useCase`.
- Injetar `Tools.Logging.Generics.ILogger` no controller para logs.
- Request:
  - `[FromQuery]` para GET.
  - `[FromBody]` para POST/PUT.
- Chamar `await useCase.ExecuteAsync(request, cancellationToken);`.
- Mapear resultado para `Ok()`, `Accepted()`, `Created()`, etc.
- Documentar com `[ProducesResponseType(...)]` e códigos de status adequados (200, 202, 400, 401, 500).
- Logs: no início de cada ação, log Executing com rota e parâmetros chave (ver skill `logging-metrics`).

## Regras de arquitetura

- Controller não injeta repositórios, DbContext ou serviços de infraestrutura; apenas use cases.
- Toda lógica de negócio fica nos use cases; controller só orquestra chamada e HTTP.
- Usar tipos de request/response da Application; não expor entidades de Domain diretamente quando houver DTO.

