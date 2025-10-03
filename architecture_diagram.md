# OpenAlgo Architecture Diagram

## High-Level System Architecture

```mermaid
graph TB
    %% Client Layer
    subgraph "Client Layer"
        WEB[Web Dashboard<br/>DaisyUI + Tailwind]
        API[REST API Clients<br/>Python/Node.js/Excel]
        WS[WebSocket Clients<br/>Real-time Data]
        STRAT[Python Strategies<br/>Automated Trading]
        TELE[Telegram Bot<br/>Mobile Interface]
    end

    %% OpenAlgo Platform
    subgraph "OpenAlgo Platform"
        %% Application Layer
        subgraph "Application Layer (Flask)"
            BLUEPRINTS[Blueprints<br/>- auth, orders, dashboard<br/>- analyzer, settings<br/>- strategy, telegram]
            RESTX[RESTx API<br/>- /api/v1/* endpoints<br/>- Swagger documentation]
            STRAT_MGR[Strategy Manager<br/>- Python hosting<br/>- Process isolation<br/>- Scheduling]
        end

        %% Business Logic Layer
        subgraph "Business Logic Layer"
            ORDER_MGR[Order Manager<br/>- Place/Modify/Cancel<br/>- Smart Orders<br/>- Basket Orders]
            POS_MGR[Position Manager<br/>- Track positions<br/>- P&L calculation<br/>- Risk management]
            STRAT_ENGINE[Strategy Engine<br/>- Strategy execution<br/>- Backtesting<br/>- Performance monitoring]
            DATA_MGR[Market Data Manager<br/>- Real-time quotes<br/>- Historical data<br/>- Symbol management]
        end

        %% Broker Integration Layer
        subgraph "Broker Integration Layer"
            BROKER_ADAPTERS[Broker Adapters<br/>20+ Indian Brokers<br/>- Zerodha, Angel One<br/>- Upstox, Groww, etc.]
            WS_ADAPTERS[WebSocket Adapters<br/>- Real-time streaming<br/>- Auto-reconnection<br/>- Data transformation]
            API_ADAPTERS[REST API Adapters<br/>- Order management<br/>- Account data<br/>- Market data]
        end

        %% Infrastructure Layer
        subgraph "Infrastructure Layer"
            DB_LAYER[Database Layer<br/>- SQLite/PostgreSQL<br/>- User management<br/>- Symbol mapping<br/>- Audit logs]
            WS_PROXY[WebSocket Proxy<br/>- Port 8765<br/>- Client authentication<br/>- Subscription management]
            ZMQ[ZeroMQ Message Bus<br/>- High-performance messaging<br/>- Publisher-Subscriber pattern<br/>- Real-time data distribution]
            SECURITY[Security & Auth<br/>- Argon2 password hashing<br/>- Fernet encryption<br/>- CSRF protection<br/>- Rate limiting]
        end
    end

    %% External Systems
    subgraph "External Systems"
        BROKERS[Broker APIs<br/>- REST APIs<br/>- WebSocket feeds<br/>- Authentication]
        MARKET_DATA[Market Data Feeds<br/>- Real-time prices<br/>- Historical data<br/>- Market depth]
        CLOUD[Cloud Services<br/>- AWS deployment<br/>- Docker containers<br/>- Nginx reverse proxy]
    end

    %% Sandbox Mode
    subgraph "Sandbox Mode (API Analyzer)"
        SANDBOX[Sandbox Engine<br/>- ₹1 Crore virtual funds<br/>- Real-time LTP execution<br/>- Margin management<br/>- Auto square-off]
        EXEC_ENGINE[Execution Engine<br/>- Order processing<br/>- Position tracking<br/>- P&L calculation]
        SQUAREOFF[Square-off Scheduler<br/>- MIS auto-close<br/>- Settlement handling<br/>- Risk management]
    end

    %% Connections
    WEB --> BLUEPRINTS
    API --> RESTX
    WS --> WS_PROXY
    STRAT --> STRAT_MGR
    TELE --> BLUEPRINTS

    BLUEPRINTS --> ORDER_MGR
    BLUEPRINTS --> POS_MGR
    BLUEPRINTS --> DATA_MGR
    RESTX --> ORDER_MGR
    RESTX --> POS_MGR
    RESTX --> DATA_MGR
    STRAT_MGR --> STRAT_ENGINE

    ORDER_MGR --> BROKER_ADAPTERS
    POS_MGR --> BROKER_ADAPTERS
    DATA_MGR --> WS_ADAPTERS
    DATA_MGR --> API_ADAPTERS

    BROKER_ADAPTERS --> BROKERS
    WS_ADAPTERS --> MARKET_DATA
    API_ADAPTERS --> BROKERS

    WS_PROXY --> ZMQ
    WS_ADAPTERS --> ZMQ
    ZMQ --> WS_PROXY

    ORDER_MGR --> DB_LAYER
    POS_MGR --> DB_LAYER
    STRAT_ENGINE --> DB_LAYER
    DATA_MGR --> DB_LAYER

    BLUEPRINTS --> SECURITY
    RESTX --> SECURITY

    %% Sandbox connections
    ORDER_MGR -.-> SANDBOX
    SANDBOX --> EXEC_ENGINE
    SANDBOX --> SQUAREOFF
    EXEC_ENGINE --> DB_LAYER
    SQUAREOFF --> DB_LAYER

    %% External connections
    BROKER_ADAPTERS --> BROKERS
    WS_ADAPTERS --> MARKET_DATA
    API_ADAPTERS --> BROKERS
    DB_LAYER --> CLOUD
```

## Detailed Component Architecture

```mermaid
graph TB
    %% Web Interface Layer
    subgraph "Web Interface Layer"
        DASHBOARD[Dashboard<br/>- Account overview<br/>- P&L tracking<br/>- Order management]
        ORDERS[Order Management<br/>- Place orders<br/>- Modify/Cancel<br/>- Order book]
        POSITIONS[Position Tracking<br/>- Open positions<br/>- Holdings<br/>- P&L analysis]
        ANALYZER[API Analyzer<br/>- Test strategies<br/>- Sandbox mode<br/>- Risk-free testing]
        SETTINGS[Settings<br/>- Broker configuration<br/>- API keys<br/>- User preferences]
    end

    %% API Layer
    subgraph "REST API Layer (/api/v1/)"
        ORDER_API[Order APIs<br/>- /placeorder<br/>- /placesmartorder<br/>- /modifyorder<br/>- /cancelorder]
        DATA_API[Data APIs<br/>- /quotes<br/>- /history<br/>- /depth<br/>- /search]
        ACCOUNT_API[Account APIs<br/>- /funds<br/>- /orderbook<br/>- /positionbook<br/>- /holdings]
        UTILITY_API[Utility APIs<br/>- /ping<br/>- /analyzer<br/>- /symbol<br/>- /expiry]
    end

    %% Service Layer
    subgraph "Service Layer"
        PLACE_ORDER[Place Order Service<br/>- Order validation<br/>- Broker routing<br/>- Error handling]
        SMART_ORDER[Smart Order Service<br/>- SL/Target logic<br/>- Position sizing<br/>- Risk management]
        FUNDS_SERVICE[Funds Service<br/>- Account balance<br/>- Margin calculation<br/>- Available funds]
        MARKET_DATA_SERVICE[Market Data Service<br/>- Real-time quotes<br/>- Historical data<br/>- Symbol search]
    end

    %% Broker Integration
    subgraph "Broker Integration (20+ Brokers)"
        ZERODHA[Zerodha<br/>- Kite API<br/>- WebSocket streaming<br/>- Order management]
        ANGEL[Angel One<br/>- SmartAPI<br/>- WebSocket feeds<br/>- Basket orders]
        UPSTOX[Upstox<br/>- REST API<br/>- Real-time data<br/>- Position tracking]
        GROWW[Groww<br/>- API integration<br/>- Market data<br/>- Order execution]
        OTHERS[Other Brokers<br/>- 5Paisa, IIFL<br/>- Kotak, Dhan<br/>- Fyers, etc.]
    end

    %% Database Layer
    subgraph "Database Layer"
        USER_DB[User Database<br/>- Authentication<br/>- API keys<br/>- Session management]
        SYMBOL_DB[Symbol Database<br/>- Master contracts<br/>- Token mapping<br/>- Exchange data]
        LOG_DB[Logging Database<br/>- API logs<br/>- Error tracking<br/>- Performance metrics]
        SANDBOX_DB[Sandbox Database<br/>- Virtual positions<br/>- Simulated P&L<br/>- Order history]
    end

    %% WebSocket Infrastructure
    subgraph "WebSocket Infrastructure"
        WS_SERVER[WebSocket Server<br/>- Port 8765<br/>- Client management<br/>- Authentication]
        ZMQ_BUS[ZeroMQ Message Bus<br/>- High-performance<br/>- Publisher-Subscriber<br/>- Real-time routing]
        BROKER_WS[Broker WebSockets<br/>- Market data feeds<br/>- Auto-reconnection<br/>- Data transformation]
    end

    %% Strategy Hosting
    subgraph "Strategy Hosting System"
        STRAT_UPLOAD[Strategy Upload<br/>- Python scripts<br/>- Dependencies<br/>- Configuration]
        STRAT_SCHEDULER[Strategy Scheduler<br/>- Cron-like scheduling<br/>- IST timezone<br/>- Process management]
        STRAT_MONITOR[Strategy Monitor<br/>- Real-time logs<br/>- Performance tracking<br/>- Error handling]
        STRAT_EXEC[Strategy Execution<br/>- Process isolation<br/>- Environment variables<br/>- API access]
    end

    %% Security & Monitoring
    subgraph "Security & Monitoring"
        AUTH[Authentication<br/>- Argon2 hashing<br/>- TOTP 2FA<br/>- Session management]
        ENCRYPTION[Encryption<br/>- Fernet tokens<br/>- API key security<br/>- Data protection]
        RATE_LIMIT[Rate Limiting<br/>- Per-user limits<br/>- API throttling<br/>- Abuse prevention]
        MONITORING[Monitoring<br/>- Latency tracking<br/>- Traffic analysis<br/>- Error reporting]
    end

    %% Connections
    DASHBOARD --> ORDER_API
    ORDERS --> ORDER_API
    POSITIONS --> ACCOUNT_API
    ANALYZER --> UTILITY_API
    SETTINGS --> USER_DB

    ORDER_API --> PLACE_ORDER
    ORDER_API --> SMART_ORDER
    DATA_API --> MARKET_DATA_SERVICE
    ACCOUNT_API --> FUNDS_SERVICE

    PLACE_ORDER --> ZERODHA
    SMART_ORDER --> ANGEL
    FUNDS_SERVICE --> UPSTOX
    MARKET_DATA_SERVICE --> GROWW

    ZERODHA --> USER_DB
    ANGEL --> SYMBOL_DB
    UPSTOX --> LOG_DB
    GROWW --> SANDBOX_DB

    WS_SERVER --> ZMQ_BUS
    ZMQ_BUS --> BROKER_WS
    BROKER_WS --> WS_SERVER

    STRAT_UPLOAD --> STRAT_SCHEDULER
    STRAT_SCHEDULER --> STRAT_EXEC
    STRAT_EXEC --> STRAT_MONITOR
    STRAT_MONITOR --> LOG_DB

    AUTH --> ENCRYPTION
    ENCRYPTION --> RATE_LIMIT
    RATE_LIMIT --> MONITORING
    MONITORING --> LOG_DB
```

## Data Flow Architecture

```mermaid
sequenceDiagram
    participant Client as Client Application
    participant WebUI as Web Dashboard
    participant API as REST API
    participant Service as Service Layer
    participant Broker as Broker API
    participant DB as Database
    participant WS as WebSocket Proxy
    participant ZMQ as ZeroMQ Bus

    %% Order Placement Flow
    Client->>API: POST /api/v1/placeorder
    API->>Service: place_order_service()
    Service->>Broker: Broker API call
    Broker-->>Service: Order response
    Service->>DB: Log order
    Service-->>API: Order result
    API-->>Client: JSON response

    %% Real-time Data Flow
    Client->>WS: WebSocket connection
    WS->>ZMQ: Subscribe to symbols
    ZMQ->>Broker: Market data feed
    Broker-->>ZMQ: Real-time data
    ZMQ-->>WS: Broadcast data
    WS-->>Client: Live updates

    %% Strategy Execution Flow
    WebUI->>Service: Upload strategy
    Service->>DB: Store strategy
    Service->>Service: Schedule execution
    Service->>Broker: Execute trades
    Broker-->>Service: Trade confirmation
    Service->>DB: Update positions
    Service->>WebUI: Strategy status
```

## Deployment Architecture

```mermaid
graph TB
    subgraph "Production Environment"
        LB[Load Balancer<br/>Nginx]
        APP1[OpenAlgo Instance 1<br/>Flask + SocketIO]
        APP2[OpenAlgo Instance 2<br/>Flask + SocketIO]
        WS_SERVER[WebSocket Server<br/>Port 8765]
        DB[(Database<br/>PostgreSQL/SQLite)]
        REDIS[(Redis Cache<br/>Session Storage)]
    end

    subgraph "Development Environment"
        DEV_APP[OpenAlgo Dev<br/>Local Flask]
        DEV_DB[(SQLite<br/>Development DB)]
        DEV_WS[WebSocket Proxy<br/>Integrated Mode]
    end

    subgraph "Docker Environment"
        DOCKER_COMPOSE[Docker Compose<br/>Multi-container]
        CONTAINER[OpenAlgo Container<br/>Flask + Dependencies]
        DB_CONTAINER[Database Container<br/>PostgreSQL]
        NGINX_CONTAINER[Nginx Container<br/>Reverse Proxy]
    end

    LB --> APP1
    LB --> APP2
    APP1 --> DB
    APP2 --> DB
    APP1 --> REDIS
    APP2 --> REDIS
    APP1 --> WS_SERVER
    APP2 --> WS_SERVER

    DEV_APP --> DEV_DB
    DEV_APP --> DEV_WS

    DOCKER_COMPOSE --> CONTAINER
    DOCKER_COMPOSE --> DB_CONTAINER
    DOCKER_COMPOSE --> NGINX_CONTAINER
    CONTAINER --> DB_CONTAINER
    NGINX_CONTAINER --> CONTAINER
```

## Key Features Architecture

```mermaid
graph LR
    subgraph "Core Features"
        UNIFIED_API[Unified API<br/>20+ Brokers]
        REAL_TIME[Real-time Data<br/>WebSocket + ZMQ]
        SMART_ORDERS[Smart Orders<br/>SL/Target/Trailing]
        BASKET_ORDERS[Basket Orders<br/>Multi-leg strategies]
    end

    subgraph "Advanced Features"
        STRATEGY_HOSTING[Strategy Hosting<br/>Python execution]
        SANDBOX_MODE[Sandbox Mode<br/>Risk-free testing]
        TELEGRAM_BOT[Telegram Bot<br/>Mobile trading]
        ANALYZER[API Analyzer<br/>Strategy testing]
    end

    subgraph "Security Features"
        ENCRYPTION[Data Encryption<br/>Fernet + Argon2]
        RATE_LIMITING[Rate Limiting<br/>Per-user controls]
        CSRF_PROTECTION[CSRF Protection<br/>Token validation]
        AUDIT_LOGGING[Audit Logging<br/>Complete tracking]
    end

    subgraph "Monitoring Features"
        LATENCY_MONITOR[Latency Monitor<br/>Performance tracking]
        TRAFFIC_MONITOR[Traffic Monitor<br/>API analytics]
        PNL_TRACKER[P&L Tracker<br/>Real-time charts]
        ERROR_TRACKING[Error Tracking<br/>Comprehensive logging]
    end

    UNIFIED_API --> STRATEGY_HOSTING
    REAL_TIME --> SANDBOX_MODE
    SMART_ORDERS --> TELEGRAM_BOT
    BASKET_ORDERS --> ANALYZER

    STRATEGY_HOSTING --> ENCRYPTION
    SANDBOX_MODE --> RATE_LIMITING
    TELEGRAM_BOT --> CSRF_PROTECTION
    ANALYZER --> AUDIT_LOGGING

    ENCRYPTION --> LATENCY_MONITOR
    RATE_LIMITING --> TRAFFIC_MONITOR
    CSRF_PROTECTION --> PNL_TRACKER
    AUDIT_LOGGING --> ERROR_TRACKING
```

This comprehensive architecture diagram shows:

1. **Multi-layered Architecture**: Clear separation between client, application, business logic, integration, and infrastructure layers
2. **Broker Agnostic Design**: Unified API layer supporting 20+ Indian brokers
3. **Real-time Infrastructure**: WebSocket proxy with ZeroMQ message bus for high-performance data distribution
4. **Strategy Hosting**: Complete Python strategy execution environment with process isolation
5. **Sandbox Mode**: Risk-free testing environment with virtual funds
6. **Security**: Comprehensive security measures including encryption, rate limiting, and audit logging
7. **Monitoring**: Advanced monitoring and analytics capabilities
8. **Scalability**: Support for multiple deployment scenarios (development, production, Docker)

The architecture is designed to be modular, extensible, and maintainable while providing a unified interface for algorithmic trading across multiple brokers.
