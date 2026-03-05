---
name: testing-xunit
description: Escreve ou altera testes unitários e de integração no projeto DataFeed. Use ao criar ou editar testes com xUnit e FluentAssertions.
---

# Testes — DataFeed (xUnit + FluentAssertions)

## Stack

- xUnit — testes e descoberta.
- FluentAssertions — assertions (ex.: `result.Should().NotBeNull()`, `list.Should().HaveCount(5)`).

## Nomenclatura

Padrão: `Method_Situation_ExpectedResult`

Exemplos:

- `ExecuteAsync_Should_ReturnPagedResult_When_DataExists`
- `ExecuteAsync_Should_ThrowValidationException_When_StartDateAfterEndDate`
- `GetManyAsync_Should_ReturnEmpty_When_NoData`

## Mocks e boas práticas

- Seguir as regras de testes definidas em `.cursor/rules/testing.mdc` (mocks obrigatórios para persistência, HTTP, I/O; dados reais só para objetos em memória; um comportamento por teste).
- No projeto: mockar repositórios (ex.: `Mock<IRawCorporateActionRepository>`), clientes HTTP e I/O; usar FluentAssertions para assertions.

## Estrutura (AAA)

- **Arrange:** montar mocks, dados de entrada, instanciar SUT.
- **Act:** chamar o método sob teste (ex.: `await useCase.ExecuteAsync(request)`).
- **Assert:** FluentAssertions sobre o resultado ou exceção (ex.: `result.Should().NotBeNull()`, `list.Should().HaveCount(5)`).

## Exceções

- Validar exceção: `await act.Should().ThrowAsync<ValidationException>();` (FluentAssertions).
- Testar mensagem ou propriedades quando relevante para o comportamento.

