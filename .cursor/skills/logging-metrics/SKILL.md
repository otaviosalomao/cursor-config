---
name: logging-metrics
description: Aplica logging estruturado e métricas (Solutions.Analytics.Tools.Logging) em novo código. Use ao criar ou alterar use cases, controllers, handlers, collectors ou qualquer fluxo que faça I/O.
---

# Logging e Métricas — Solutions.Analytics.Tools.Logging

## Regra: logging via Decorator

Toda nova implementação que precise de log deve **usar o padrão Decorator** sempre que houver decorator disponível:

- **Use cases** — decorators (`LoggingUseCaseWithRequestAndResultDecorator`, `LoggingUseCaseWithOnlyRequestDecorator`).
- **Event handlers** — decorator `LoggingApplicationEventHandlerDecorator`.
- **Collectors** (`ICorporateActionCollector`) — decorator `LoggingCorporateActionCollectorDecorator`.

A classe "real" fica sem logging; o decorator registra Executing, Success, Error e **ExecutionTimeMetric**. Manter apenas `_logs.Add(ApplicationLog...)` quando o contrato exigir.

Para novos tipos de componente que precisem de log (e tenham interface), criar um decorator que implemente a mesma interface, envolver a implementação real e adicionar log + ExecutionTimeMetric; registrar o decorator no DI usando Scrutor (`Decorate`).

Exceção: controllers, middlewares e pontos de entrada HTTP, onde o logging é feito diretamente na classe.

## Quando aplicar

- Novo use case / handler / collector: não adicionar `ILogger`/`LogFormatter`/`ExecutionTimeMetric` dentro da classe; deixar para o decorator.
- Novo controller/endpoint: log Executing por ação (rota, parâmetros chave) na própria classe.
- Novo adapter ou fluxo com I/O: preferir decorator; se não houver, log Error em falhas e `ExecutionTimeMetric` ao redor da operação.

## Biblioteca e referência

- Pacote: `Solutions.Analytics.Tools.Logging`.
- Configuração em `Program.cs`: `ConfigureLogging(new LoggingOptions(applicationId, loggerUrl))`.

## Logger estruturado (LogFormatter)

- Injetar `ILogger` (namespace `Tools.Logging.Generics`).
- Usar `LogFormatter` para mensagens com contexto:
  - `new LogFormatter("ContextoOperacao", "Descrição curta").AddValue("Chave", valor).Format(DateTime.UtcNow, LogStatus.Executing|Success|Warning|Error, mensagemOpcional)`.
  - `this._logger.Info(log)` ou `this._logger.Error(log)` conforme o status.

## Métrica de tempo (ExecutionTimeMetric) para I/O

- Em operações de I/O (import, reprocess, coleta HTTP, consulta a repositório), envolver o bloco em `ExecutionTimeMetric`.
- Parâmetros de contexto: `(string, object)[]` com Operacao, Source, Page, etc.

Exemplo:

```csharp
var logParams = new (string, object)[]
{
    ("Operacao", "ImportRawCorporateActions"),
    ("Source", request.CorporateActionSource.ToString()),
    ("StartDate", request.StartDate.ToString()),
    ("EndDate", request.EndDate.ToString())
};
var timeMetric = new ExecutionTimeMetric(this._logger, logParams);

using (timeMetric.Start(DateTime.UtcNow, "ImportRawCorporateActions", "Import corporate actions"))
{
    // toda a lógica de I/O
}
```

