# Project Context

## Purpose
Balance Checker Service - HTTP API for checking LLM provider account balances across multiple providers (OpenAI, OpenRouter, Qwen, etc.) with a plugin-based architecture that enables zero-modification addition of new providers.

## Tech Stack
- **Language**: Python 3.11+
- **Web Framework**: FastAPI 0.104+
- **Async HTTP**: httpx 0.25+
- **ASGI Server**: Uvicorn 0.24+
- **Validation**: Pydantic 2.5+
- **Testing**: pytest, pytest-asyncio
- **Code Quality**: black (formatting), ruff (linting), mypy (type checking)

## Project Conventions

### Code Style
- **Type Hints**: Full type annotations on all functions and methods
- **Formatting**: Black with default line length (88)
- **Linting**: Ruff with strict rules
- **Naming**:
  - Classes: `PascalCase` (e.g., `OpenAIProvider`)
  - Functions/variables: `snake_case`
  - Constants: `UPPER_SNAKE_CASE`
  - Private members: leading underscore
- **Docstrings**: Google-style docstrings for all public methods
- **Async/Await**: All I/O operations must be async

### Architecture Patterns
- **Plugin Architecture**: Providers are auto-discovered through naming conventions
- **Abstract Base Classes**: Use Python ABC for strict interface enforcement
- **Registry Pattern**: Provider registry for auto-discovery and lookup
- **Dependency Injection**: Providers are instantiated dynamically
- **Separation of Concerns**: Clear boundaries between API, provider logic, and HTTP clients
- **Exception Hierarchy**: Structured exceptions with provider-specific context

### Testing Strategy
- **Unit Tests**: Test each provider implementation with mocked HTTP responses
- **Integration Tests**: Test API endpoints with test providers
- **Coverage**: Minimum 80% coverage
- **Async Tests**: Use pytest-asyncio for async code
- **Fixtures**: Shared fixtures in `conftest.py` for test app and HTTP mocks

### Git Workflow
- **Branching**: Feature branches from `main`
- **Commit Format**: Conventional commits (e.g., `feat:`, `fix:`, `docs:`)
- **PR Reviews**: Required for all changes
- **Issue Tracking**: Beads for task management

## Domain Context

### Provider API Keys
Different LLM providers use different API key formats:
- **OpenAI**: Starts with `sk-`
- **OpenRouter**: Starts with `sk-or-`
- **Qwen**: Format TBD
- API key format validation should happen before making HTTP requests

### Balance API Endpoints
Each provider has different API endpoints and response formats for balance checking. Provider implementations must:
1. Handle provider-specific authentication
2. Parse provider-specific response formats
3. Convert provider errors to standard exceptions
4. Return balance as float (USD)

### Rate Limiting
Providers may enforce rate limits. Service should:
- Propagate rate limit errors to client
- Not implement caching (always fetch fresh data)
- Use appropriate timeouts (10s default)

## Important Constraints

### Architecture Constraints
- **Zero-Modification Principle**: Adding new providers MUST NOT require modifying existing code
- **No Configuration Files**: Providers must be auto-discovered through code structure
- **No Hardcoded Provider Lists**: Registry must discover providers dynamically
- **Interface Contract**: All providers must implement `BalanceProvider` base class

### API Constraints
- **Authentication**: Simple X-API-Key header (no user accounts, no JWT)
- **Response Format**: Only return current balance amount and currency
- **No Caching**: Always fetch fresh data from providers
- **Async Only**: All I/O must use async/await

### Operational Constraints
- **Timeout**: 10 second timeout for provider API calls
- **Error Transparency**: Provider-specific errors must be propagated to clients
- **No Persistence**: No database, no session storage
- **Stateless**: Each request is independent

## External Dependencies

### Provider APIs (HTTP)
- **OpenAI API**: https://api.openai.com (account/balance endpoint)
- **OpenRouter API**: https://openrouter.ai/api (balance endpoint)
- **Qwen API**: TBD

### Python Packages
- `fastapi` - Web framework
- `httpx` - Async HTTP client
- `uvicorn` - ASGI server
- `pydantic` - Data validation
- `pytest` - Testing framework

### Development Tools
- `black` - Code formatter
- `ruff` - Linter
- `mypy` - Type checker
- `beads` - Issue tracking
