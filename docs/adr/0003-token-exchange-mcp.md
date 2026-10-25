# ADR-0003: Token Exchange (RFC 8693) для вызовов MCP Server

- **Статус:** Proposed
- **Дата:** YYYY-MM-DD
- **Авторы:** Platform Team
- **Контекст blueprint:** [docs/blueprint.md](../blueprint.md)

## Контекст и проблема
Пользовательский JWT выписан для Backstage (`aud=backstage`). Прямой проброс его
в MCP Server нарушает least privilege. Нужен способ делегировать права пользователя
MCP Server без расширения привилегий.

## Рассмотренные варианты
1. **Прямой проброс JWT** — просто, но нарушает least privilege и аудит.
2. **Token Exchange (RFC 8693)** — оркестратор обменивает JWT на scoped-токен
   с `aud=mcp-server`, TTL 30–60 с, суженным набором scopes.

## Решение
Token Exchange (RFC 8693). Оркестратор — единственный компонент, который видит
оригинальный JWT. MCP Server получает scoped-токен с ограниченным TTL и аудиторией.

## Последствия
### Положительные
- Least privilege на уровне транспорта.
- Упрощенный аудит: в логах MCP видно делегирование.

### Отрицательные
- Дополнительный hop (token exchange) — latency.
- Требуется поддержка RFC 8693 в IdP.

## Критерий пересмотра
- IdP не поддерживает RFC 8693 — рассмотреть альтернативы (mTLS + scoped API keys).

## Ссылки
- [Blueprint](../blueprint.md)
- RFC 8693: OAuth 2.0 Token Exchange