---
name: coding-style
description: Aplica convenções de estilo em novas implementações ou ajustes. Use ao escrever ou alterar código C# para garantir declaração explícita de tipos, ordem dos membros da classe e uso de this.NomePropriedade em construtores.
---

# Estilo de Código — DataFeed

## 1. Declaração explícita de tipos

Em **novas implementações ou ajustes**, toda variável local deve ser declarada com **tipo explícito**. Não utilizar `var`.

### Exemplos

```csharp
// ❌ Evitar
var teste = 0;
var request = new GetManyRequest();
var formatter = new LogFormatter("Contexto", "Mensagem").AddValue("Chave", valor);

// ✅ Preferir
int teste = 0;
GetManyRequest request = new GetManyRequest();
ILogFormatter formatter = new LogFormatter("Contexto", "Mensagem").AddValue("Chave", valor);
```

### Quando aplicar

- Código novo (use cases, controllers, handlers, adapters, etc.).
- Refatorações e correções em código existente.
- Qualquer alteração que introduza ou modifique declarações de variáveis locais.

### Exceções

- Tipos anônimos continuam usando `var` quando forem realmente anônimos (ex.: `var x = new { A = 1, B = "x" };`).
- Em expressões LINQ onde o tipo é anônimo ou complexo e a legibilidade piora com o tipo explícito, avaliar caso a caso; na dúvida, preferir tipo explícito quando o compilador permitir.

## 2. Ordem dos membros da classe

Em **novas classes**, implementar os membros na seguinte sequência (de cima para baixo no arquivo):

1. Constantes e campos estáticos readonly.
2. Campos privados.
3. Construtor(es).
4. Propriedades públicas.
5. Métodos públicos.
6. Métodos privados.

## 3. Uso de this.NomePropriedade em construtores

- Em construtores, sempre atribuir campos/propriedades com `this.NomePropriedade` (ex.: `this._service = service;`).
- Manter consistência em todo o código C# do projeto.

## Escopo

Convenções **específicas deste projeto**. Para regras de design de código mais gerais, ver a rule `.cursor/rules/code-design.mdc`.

