# 🏆 Mini Loyalty Rewards Hub

> Lightweight but enterprise-grade loyalty, rewards & Cash+Miles engine built with
> **Spring Boot 3.2**, **GraphQL**, **Redis**, **Resilience4j**, and **Siebel CRM** integration.
>
> Designed to reflect real-world fintech/banking patterns used at companies like
> Ally Financial, Fiserv, and Chase.

---

## 📌 What This Project Demonstrates

This is a **senior-level portfolio project** showcasing production-grade Java backend
architecture across every layer — from domain modeling to infrastructure resilience.

| Skill Area | What's Demonstrated |
|---|---|
| **DDD** | Rich domain model with invariants, factory methods, encapsulated business logic |
| **GraphQL** | Schema-first API, custom scalars, Mutations, Subscriptions, N+1 prevention |
| **Redis** | Cache-aside pattern, per-cache TTLs, Pub/Sub for real-time WebSocket scaling |
| **Circuit Breaker** | Resilience4j CB + Retry on Siebel CRM, graceful fallbacks |
| **Cash+Miles Engine** | Hybrid pricing, one-time quote tokens, replay attack prevention |
| **WebSockets** | Real-time balance updates via GraphQL Subscriptions + Redis Pub/Sub |
| **Observability** | Micrometer, Prometheus, Zipkin distributed tracing, structured logging |
| **Testing** | Domain unit tests (zero Spring), GraphQL integration tests, HttpGraphQlTester |

---

## 🏗️ Architecture
```
┌─────────────────────────────────────────────────────────┐
│                   GraphQL API Layer                      │
│         Query / Mutation / Subscription Resolvers        │
└──────────────────────────┬──────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────┐
│               Application Service Layer                  │
│   LoyaltyApplicationService · CashMilesCalculator        │
│   RewardQueryService                                     │
└──────────────────────────┬──────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────┐
│                   Domain Layer (Pure Java)                │
│   Member · LoyaltyAccount · Reward · Transaction         │
│   MemberStatus · DomainValueObjects                      │
└──────────────────────────┬──────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────┐
│               Infrastructure Layer                       │
│   Redis Cache · Siebel CRM · WebSocket Pub/Sub           │
│   JPA Repositories · Exception Handlers                  │
└─────────────────────────────────────────────────────────┘
```

---

## 📂 Project Structure
```
mini-loyalty-rewards-hub/
├── build.gradle
├── settings.gradle
├── docker-compose.yml
├── src/
│   ├── main/
│   │   ├── resources/
│   │   │   ├── application.yml
│   │   │   └── graphql/
│   │   │       └── schema.graphqls
│   │   └── java/com/loyalty/
│   │       ├── LoyaltyPlatformApplication.java
│   │       │
│   │       ├── domain/
│   │       │   ├── model/
│   │       │   │   ├── Member.java
│   │       │   │   ├── MemberStatus.java
│   │       │   │   ├── MemberPreferences.java
│   │       │   │   ├── LoyaltyAccount.java
│   │       │   │   ├── Reward.java
│   │       │   │   ├── Transaction.java
│   │       │   │   ├── CurrencyType.java
│   │       │   │   ├── RewardCategory.java
│   │       │   │   ├── TransactionType.java
│   │       │   │   └── DomainValueObjects.java
│   │       │   └── repository/
│   │       │       ├── MemberRepository.java
│   │       │       ├── LoyaltyAccountRepository.java
│   │       │       ├── RewardRepository.java
│   │       │       └── TransactionRepository.java
│   │       │
│   │       ├── application/
│   │       │   └── service/
│   │       │       ├── LoyaltyApplicationService.java
│   │       │       ├── CashMilesCalculator.java
│   │       │       └── RewardQueryService.java
│   │       │
│   │       └── infrastructure/
│   │           ├── cache/
│   │           │   └── CacheEvictionService.java
│   │           ├── config/
│   │           │   ├── RedisConfig.java
│   │           │   ├── ReactiveRedisConfig.java
│   │           │   ├── GraphQLScalarConfig.java
│   │           │   ├── SiebelWebClientConfig.java
│   │           │   └── DataSeeder.java
│   │           ├── crm/siebel/
│   │           │   ├── SiebelCrmAdapter.java
│   │           │   ├── SiebelMemberDto.java
│   │           │   ├── SiebelTierUpdateRequest.java
│   │           │   ├── SiebelCaseCreateRequest.java
│   │           │   └── SiebelUpdateResponse.java
│   │           ├── graphql/
│   │           │   ├── resolver/
│   │           │   │   ├── LoyaltyQueryResolver.java
│   │           │   │   ├── LoyaltyMutationResolver.java
│   │           │   │   └── LoyaltySubscriptionResolver.java
│   │           │   └── subscription/
│   │           │       └── SubscriptionPublisher.java
│   │           └── exception/
│   │               └── GlobalGraphQLExceptionHandler.java
│   │
│   └── test/java/com/loyalty/
│       ├── LoyaltyAccountTest.java
│       ├── MemberStatusTest.java
│       └── LoyaltyGraphQLTest.java
```

---

## 🚀 Quick Start

### Prerequisites
- Java 21+
- Docker Desktop

### 1. Clone the repo
```bash
git clone https://github.com/YOUR_USERNAME/mini-loyalty-rewards-hub.git
cd mini-loyalty-rewards-hub
```

### 2. Start infrastructure
```bash
docker-compose up -d
```
This starts Redis, Redis Insight, Siebel mock (WireMock), and Zipkin.

### 3. Run the application
```bash
./gradlew bootRun
```

### 4. Open GraphiQL playground
```
http://localhost:8080/graphiql
```

### 5. View Redis keys (Redis Insight)
```
http://localhost:5540
```

### 6. H2 Database console (dev only)
```
http://localhost:8080/h2-console
JDBC URL: jdbc:h2:mem:testdb
```

---

## 📊 Sample Queries

### Get member profile
```graphql
query {
  member(memberId: "M-TEST-001") {
    memberId
    firstName
    status
    loyaltyAccount {
      cashBalance
      milesBalance
      lifetimeMiles
    }
  }
}
```

### Get Cash+Miles pricing quote
```graphql
query {
  cashMilesQuote(
    memberId: "M-TEST-001"
    rewardId: "FLIGHT-NYC-LAX"
    cashPercent: 60
  ) {
    cashComponent
    milesComponent
    savingsVsFullCash
    quoteToken
    expiresAt
  }
}
```

### Redeem a reward
```graphql
mutation {
  redeemReward(input: {
    memberId:     "M-TEST-001"
    rewardId:     "DINING-NYC-50"
    quoteToken:   "<token-from-quote>"
    currencyType: HYBRID
    cashAmount:   25.00
    milesAmount:  2500
  }) {
    success
    confirmationNumber
    newCashBalance
    newMilesBalance
    siebelCaseId
  }
}
```

### Tier status projection
```graphql
query {
  statusProjection(memberId: "M-TEST-001") {
    currentStatus
    nextStatus
    milesNeeded
    percentComplete
    projectedStatusByYearEnd
  }
}
```

### Real-time balance subscription
```graphql
subscription {
  balanceUpdated(memberId: "M-TEST-001") {
    milesBalance
    cashBalance
    lastActivityDate
  }
}
```

---

## 🧠 Key Design Decisions

### 1. Domain-Driven Design
The domain layer has **zero Spring dependencies**. `Member`, `LoyaltyAccount`, and
`Reward` enforce their own invariants:
- `LoyaltyAccount.debitMiles()` throws `InsufficientBalanceException` — never goes negative
- `lifetimeMiles` is **never decremented** — it's a lifetime metric, not a balance
- `Member.promote()` enforces one-way tier progression — no silent demotions

### 2. Quote Token System
Cash+Miles pricing uses **single-use Redis tokens** (2-min TTL):
1. `cashMilesQuote` query → generates token, stores in Redis
2. `redeemReward` mutation → validates + deletes token (atomic)
3. Second use → `QUOTE_EXPIRED` error

This prevents double-redemption, stale pricing, and replay attacks.

### 3. Siebel CRM Defense
Siebel is slow and unreliable. The adapter is fully defended:
- **Circuit Breaker**: opens after 5/10 failures, waits 30s before retry
- **Retry**: 3 attempts with exponential backoff (1s → 2s → 4s)
- **Fallback**: returns `SIEBEL_UNAVAILABLE_QUEUED` — platform keeps running
- **Anti-Corruption Layer**: Siebel's ugly field names (`fstName`, `LOC_LOYALTY_TIER_CD`)
  never escape the `crm/siebel` package

### 4. Redis Cache Strategy
All cache names are constants in `RedisConfig`. Every TTL has a business reason:

| Cache | TTL | Why |
|---|---|---|
| `loyalty::member::profile` | 5 min | Balance changes frequently |
| `loyalty::reward::catalog` | 60 min | Catalog rarely updates |
| `loyalty::quote::cash-miles` | 2 min | Quotes expire fast |
| `loyalty::status::projection` | 10 min | Tier calculations are expensive |

### 5. Real-Time WebSocket Scaling
GraphQL Subscriptions use **Redis Pub/Sub** so balance updates work across all pods:
- Any pod that processes a mutation publishes to `loyalty:balance:{memberId}`
- All pods subscribed to that channel receive the signal
- Each pod emits the fresh balance to its local WebSocket clients

---

## 🧪 Running Tests
```bash
# Domain unit tests (zero Spring, < 10ms each)
./gradlew test --tests "com.loyalty.LoyaltyAccountTest"
./gradlew test --tests "com.loyalty.MemberStatusTest"

# GraphQL integration tests
./gradlew test --tests "com.loyalty.LoyaltyGraphQLTest"

# All tests
./gradlew test
```

---

## ⚙️ Environment Variables

| Variable | Default | Description |
|---|---|---|
| `REDIS_HOST` | `localhost` | Redis host |
| `REDIS_PORT` | `6379` | Redis port |
| `REDIS_PASSWORD` | *(empty)* | Redis auth |
| `SIEBEL_BASE_URL` | `http://localhost:8099/siebel/mock` | Siebel REST URL |
| `SIEBEL_USER` | `siebelAdmin` | Siebel username |
| `SIEBEL_PASS` | `changeme` | Siebel password |

---

## 📈 Observability Endpoints

| Endpoint | Description |
|---|---|
| `GET /actuator/health` | App health check |
| `GET /actuator/prometheus` | Prometheus metrics scrape |
| `GET /actuator/circuitbreakers` | Siebel circuit breaker status |
| `http://localhost:9411` | Zipkin distributed traces |
| `http://localhost:5540` | Redis Insight (cache inspector) |

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Language | Java 21 |
| Framework | Spring Boot 3.2.5 |
| API | Spring for GraphQL (schema-first) |
| Cache | Redis 7.2 + Spring Cache + Lettuce |
| Real-time | GraphQL Subscriptions + Redis Pub/Sub |
| Resilience | Resilience4j (Circuit Breaker + Retry) |
| HTTP Client | Spring WebFlux WebClient |
| Database | Spring Data JPA + H2 (dev) / PostgreSQL (prod) |
| Build | Gradle 8 |
| Observability | Micrometer + Prometheus + Zipkin |

---

## 👤 Author

**Vishnu** — Java Backend Developer


[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?style=flat&logo=linkedin)](https://linkedin.com/in/YOUR_PROFILE)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?style=flat&logo=github)](https://github.com/YOUR_USERNAME)

---

## 📄 License

MIT License — feel free to use this as a reference or starting point.
