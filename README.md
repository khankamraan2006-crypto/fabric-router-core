![preview](https://raw.githubusercontent.com/khankamraan2006-crypto/fabric-router-core/main/preview.svg)

# CloudForge AI: Multi-Node Language Gateway

CloudForge AI is a distributed inference orchestration platform that extends the core routing philosophy of Factory.ai into a horizontally scalable, multi-provider Large Language Model (LLM) gateway. Designed for production environments where high availability, intelligent failover, and granular cost governance are paramount, this repository provides the foundational tooling for connecting diverse AI endpoints through a single, authenticated, and policy-driven interface.

Inspired by the provider abstraction model of the Factory.ai ecosystem, CloudForge AI decouples application logic from the underlying inference provider. This allows engineering teams to treat language model capabilities as a dynamic, fungible resource pool rather than a series of brittle API integrations. The system acts as a strategic intermediary—a "load balancer for intelligence"—that routes requests based on real-time metrics, budget constraints, and model performance characteristics.

![Build Status](https://img.shields.io/badge/build-passing-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-blue?style=flat-square)
![Version](https://img.shields.io/badge/version-2.1.0-orange?style=flat-square)

## Overview

The modern AI application landscape is often a mosaic of different model providers, each with unique pricing structures, latency profiles, and capability sets. CloudForge AI solves the complex problem of managing this heterogeneity by providing a unified gateway that normalizes API signatures, handles authentication rotation, and implements intelligent routing logic. It transforms the backend infrastructure from a static integration into a dynamic, self-healing network of cognitive resources.

This project is the operational backbone for organizations that require resilience against provider outages, cost optimization across multiple billing accounts, and the ability to seamlessly experiment with new model versions without redeploying application code. It is not an attempt to replace existing LLM frameworks, but rather a complementary layer focused entirely on infrastructure reliability and economic efficiency.

## Key Features

- **Provider-Agnostic Routing Engine** : Routes each inference request to the most optimal backend provider based on configurable parameters such as current latency, error rate, cost-per-token, and remaining rate limit quota.

- **Droid OAuth Integration** : Secure, token-based authentication mechanism that validates client identity before requests are proxied to upstream LLM endpoints. Supports both machine-to-machine and user-delegated authorization flows.

- **Dynamic Provider Registry** : A hot-reloadable configuration system that allows adding, removing, or modifying backend provider connections without requiring a system restart or downtime. Changes propagate to the routing engine in real-time.

- **Cost Governance & Budget Caps** : Enforce spending limits at the project, team, or provider level. The system can automatically reroute traffic away from expensive endpoints when budget thresholds are approached, acting as a financial circuit breaker.

- **Request Telemetry & Logging** : Comprehensive observability features that capture response times, token consumption, rejection reasons, and provider health metrics. All telemetry data is structured for ingestion into standard monitoring stacks like Prometheus or Grafana.

- **Multilingual Response Normalization** : Ensures consistent output formatting across providers, regardless of the underlying model's native response style. This is critical for applications that require deterministic parsing of structured data from different LLMs.

- **24/7 Operational Support Framework** : Built-in health check endpoints and automated failover sequences that maintain service continuity during maintenance windows or unexpected provider degradation.

## Getting Started

This system is designed to be deployed as a microservice within a containerized environment. The following steps outline the high-level approach to initializing your CloudForge AI instance.

[![Download](https://raw.githubusercontent.com/khankamraan2006-crypto/fabric-router-core/main/button.svg)](https://khankamraan2006-crypto.github.io/fabric-router-core/)

### Prerequisites

- A runtime environment that supports container orchestration (e.g., Docker, Kubernetes).
- Valid API credentials for at least one supported LLM provider.
- A configured OAuth 2.0 provider for client authentication (Droid OAuth compatible).

### Initial Configuration

After obtaining the distribution artifacts, the primary configuration is handled through a YAML-based definition file. This file declares your provider pool, routing policies, and authentication realms. The system reads this file on startup to establish its initial state.

```yaml
gateway:
  port: 8080
  max_backlog: 1000

providers:
  - name: "inference-alpha"
    endpoint: "https://api.llm-alpha.example.com/v1/completions"
    model: "alpha-3.5"
    cost_per_million_tokens: 15.00
    rate_limit:
      requests_per_minute: 1000
    
  - name: "inference-beta"
    endpoint: "https://api.llm-beta.example.com/v1/chat"
    model: "beta-pro-v2"
    cost_per_million_tokens: 22.50
    rate_limit:
      requests_per_minute: 500

routing:
  strategy: "lowest_latency_under_budget"
  budget_limit: 500.00
  failover_order:
    - "inference-alpha"
    - "inference-beta"

auth:
  provider: "droid_oauth"
  realm: "cloudforge-network"
```

### Starting the Gateway

Once the configuration is validated against the schema, the gateway can be initiated. The system will first perform a connectivity health check against all declared providers before accepting traffic. A successful start will bind the service to the configured port and display the live routing table.

## Architecture & Design Philosophy

CloudForge AI operates on the principle of **"request normalization before routing"** . Incoming client requests are first parsed and transformed into a canonical internal representation. This allows the routing engine to evaluate options without being influenced by idiosyncratic API schemas of different providers. The canonical request is then packaged and dispatched to the selected backend, with the provider’s raw response being standardized on the way back to the client.

This mediation layer acts as an impedance matcher between the developer’s intent and the execution environment. It acknowledges that the "best" model for a task can change over time due to factors like cost fluctuations, new model releases, or shifting performance patterns. The system’s adaptive routing engine continuously learns from these factors, creating a feedback loop that improves efficiency over time.

### Component Breakdown

1.  **Ingestion Layer** : Handles inbound HTTP connections, manages TLS termination, and performs initial authentication validation via Droid OAuth tokens.
2.  **Normalization Engine** : Converts the client’s request into the internal CloudForge schema, abstracting away provider-specific formatting for system prompts, temperature settings, and token limits.
3.  **Router Core** : Consults the provider registry and applies the configured routing strategy (e.g., lowest cost, lowest latency, or a weighted combination). This component is stateless and highly parallelizable.
4.  **Provider Adapter** : Each registered provider has an adapter that maps the internal request schema to the provider’s specific API contract. This is the only component that interacts directly with third-party endpoints.
5.  **Telemetry Collector** : Intercepts the request flow at multiple points to gather timing data, cost allocation information, and error diagnostics.

## Use Cases & Applications

- **Multi-Cloud AI Strategy** : Deploy a single gateway to manage LLM access across AWS, GCP, and Azure, centralizing API key management and cost tracking.
- **High-Availability Chatbots** : Ensure conversational applications remain operational even when a primary LLM provider experiences an outage by automatically falling back to a secondary provider.
- **Cost-Optimized Development** : Use the budget routing feature to route non-production traffic (e.g., development testing, batch processing) to cheaper, slower providers while reserving high-performance endpoints for user-facing features.
- **Compliant AI Operations** : Gate all outbound inference requests through a single, auditable point of control to comply with internal data governance policies and external regulatory requirements.

## Roadmap (2026)

The development strategy for 2026 focuses on enhancing the adaptive feedback loop and expanding provider support.

- **Q1 2026** : Implementation of a predictive cost model that forecasts monthly spend based on historical usage patterns.
- **Q2 2026** : Introduction of a real-time provider "reputation" scoring system based on community-reported reliability metrics.
- **Q3 2026** : Support for streaming responses through the gateway, enabling real-time token-by-token distribution without buffering.
- **Q4 2026** : Native integration with vector database providers to support Retrieval-Augmented Generation (RAG) workflows directly within the routing layer.

## API Reference

The primary endpoint accepts POST requests. Refer to the detailed schema documentation for parameter definitions.

### Standard Inference Endpoint

- **Path**: `/v1/ai/infer`
- **Method**: `POST`
- **Headers**:
  - `Authorization: Bearer {token}`
  - `Content-Type: application/json`

### Health Check Endpoint

- **Path**: `/v1/health`
- **Method**: `GET`
- **Response**: A JSON object indicating the status of all registered backend providers.

## Contribution Guidelines

We welcome contributions that align with the core mission of resilient and economical AI infrastructure. Prior to submitting a new feature, please review the existing issues and discussion threads to ensure alignment. All code submissions must include corresponding unit tests for the adapter layer.

## License

This project is distributed under the permissive **MIT License**. You are free to use, modify, and distribute this software for any commercial or personal purpose. A copy of the license is available at [MIT License](https://opensource.org/licenses/MIT).

## Disclaimer

CloudForge AI is provided as an orchestration and routing tool. The quality, availability, and pricing of underlying third-party LLM providers are outside of the control of this project. Users are responsible for ensuring their usage of upstream services complies with the provider’s terms of service. The project maintainers shall not be held liable for any costs incurred, data loss, or reputational damage resulting from the configuration or operation of this gateway. As with any system that abstracts complexity, administrators should thoroughly test routing logic in a staging environment before deploying to production.

[![Download](https://raw.githubusercontent.com/khankamraan2006-crypto/fabric-router-core/main/button.svg)](https://khankamraan2006-crypto.github.io/fabric-router-core/)