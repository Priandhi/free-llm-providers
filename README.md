# 🆓 Free LLM Providers

> **Curated list of free LLM API providers + proxy configuration guides.**
> DeepSeek, Claude, Gemini, GPT, MiMo, and more — all at zero cost.

[![GitHub stars](https://img.shields.io/github/stars/Priandhi/free-llm-providers?style=social)](https://github.com/Priandhi/free-llm-providers/stargazers)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## 📋 Table of Contents

- [Quick Overview](#quick-overview)
- [Provider Details](#provider-details)
  - [1. OpenModel.ai](#1-openmodelai)
  - [2. OpenCode Zen](#2-opencode-zen)
  - [3. SambaNova](#3-sambanova)
  - [4. Pollinations](#4-pollinations)
  - [5. DeepSeek (Official)](#5-deepseek-official)
  - [6. TokenRouter](#6-tokenrouter)
  - [7. Halo Proxy → b.ai](#7-halo-proxy--bai)
  - [8. OpenRouter](#8-openrouter)
  - [9. Cloudflare Workers AI](#9-cloudflare-workers-ai)
  - [10. Google Gemini API](#10-google-gemini-api)
- [9Router Configuration](#9router-configuration)
- [Proxy & Bypass Techniques](#proxy--bypass-techniques)
- [Contribute](#contribute)

---

## Quick Overview

| Provider | Best For | Auth Required | Rate Limit | Models |
|----------|----------|---------------|------------|--------|
| **OpenModel.ai** | Anthropic/Claude models | API key | Moderate | deepseek-v4-flash, claude-opus-4.x |
| **OpenCode Zen** | Claude + GPT models | Free tier (no CC) | 50/day free | deepseek-v4-flash-free, mimo-v2.5-free |
| **SambaNova** | DeepSeek, Llama | Free signup (no CC) | 30 req/min | deepseek-r1, llama-4, qwen-3 |
| **Pollinations** | General purpose | None | Unmetered | Various open models |
| **DeepSeek** | DeepSeek models | API key (paid) | Per plan | deepseek-v4-pro, deepseek-v4-flash |
| **TokenRouter** | Router with DeepSeek | API key | Per balance | deepseek-v4-pro/flash |
| **b.ai (Halo proxy)** | Claude Code via CC headers | API key (free tier) | Shared pool | minimax-m3 |
| **OpenRouter** | Multi-model hub | API key (free credits) | Per model | 200+ models |
| **Cloudflare Workers AI** | Open models | Cloudflare account | 10k/day free | 50+ models |
| **Google Gemini** | Gemini models | API key (free tier) | 60 req/min | gemini-1.5-flash, gemini-2.0-flash |

---

## Provider Details

### 1. OpenModel.ai

**Endpoint:** `https://api.openmodel.ai/v1`  
**Format:** Anthropic-compatible (`/v1/messages`)  
**Auth:** `x-api-key` header  
**Key prefix:** `om-`

**Free models:**
- `deepseek-v4-flash` — best general purpose
- `claude-sonnet-4.5` — Claude-level reasoning
- `deepseek-v4-pro` — heavier reasoning

**9Router Config:**
```yaml
# providerNodes entry
type: anthropic-compatible
name: OpenModel
data:
  prefix: om
  baseUrl: https://api.openmodel.ai/v1
  apiType: chat
```

**Usage with cURL:**
```bash
curl https://api.openmodel.ai/v1/messages \
  -H "x-api-key: om-..." \
  -H "anthropic-version: 2023-06-01" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "deepseek-v4-flash",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

---

### 2. OpenCode Zen

**Endpoint:** `https://opencode.ai/zen/v1`  
**Format:** OpenAI-compatible (`/v1/chat/completions`)  
**Auth:** API key (free registration)  
**Key prefix:** `oc-`

**Free models:**
| Model | Status | Notes |
|-------|--------|-------|
| `deepseek-v4-flash-free` | ✅ Working | Best free option |
| `mimo-v2.5-free` | ✅ Working | MiMo's latest |
| `nemotron-3-ultra-free` | ✅ Working | NVIDIA model |
| `north-mini-code-free` | ✅ Working | Coding focused |
| `minimax-m3-free` | ❌ Expired | Promotion ended |
| `qwen3.6-plus-free` | ❌ Expired | Promotion ended |

> Warning: Datacenter IPs get rate-limited (429). Use Cloudflare Workers relay to bypass.

**9Router Config:**
```yaml
type: openai-compatible
name: OpenCode-Zen
data:
  prefix: oc
  baseUrl: https://opencode.ai/zen/v1
  apiType: chat
  models:
    - deepseek-v4-flash-free
    - mimo-v2.5-free
    - nemotron-3-ultra-free
    - north-mini-code-free
```

---

### 3. SambaNova

**Endpoint:** `https://api.sambanova.ai/v1`  
**Format:** OpenAI-compatible  
**Auth:** API key (free signup, no credit card)  
**Key prefix:** Not needed

**Free models:**
- `deepseek-r1-0709` — DeepSeek reasoning
- `llama4-maverick-17b-128e-instruct` — Meta Llama 4
- `qwen3-235b-a22b` — Alibaba Qwen 3
- `Qwen2.5-72B-Instruct-8bit` — Qwen 2.5

**Sign up:** https://cloud.sambanova.ai/ (no credit card required)

**9Router Config:**
```yaml
type: openai-compatible
name: SambaNova
data:
  prefix: sn
  baseUrl: https://api.sambanova.ai/v1
  apiType: chat
```

---

### 4. Pollinations

**Endpoint:** `https://text.pollinations.ai/openai`  
**Format:** OpenAI-compatible  
**Auth:** None (fully free, no key needed)

**Models:**
- Various open-source models (Mistral, Llama variants)

**No rate limiting** — but models change frequently. Check their GitHub for current availability.

---

### 5. DeepSeek (Official)

**Endpoint:** `https://api.deepseek.com/v1`  
**Format:** OpenAI-compatible  
**Auth:** API key (paid, need to top up)  
**Key format:** `sk-` + 32 hex chars = 35 chars

**Models:**
| Model | Price (Input/Output) |
|-------|----------------------|
| `deepseek-v4-flash` | $0.07 / $0.28 per 1M tokens |
| `deepseek-v4-pro` | $0.27 / $1.10 per 1M tokens |

> DeepSeek is NOT free, but it's the cheapest high-quality provider. Top up at https://platform.deepseek.ai/

---

### 6. TokenRouter

**Endpoint:** `https://api.tokenrouter.com/v1`  
**Format:** OpenAI-compatible  
**Auth:** API key

**Models:**
- `deepseek/deepseek-v4-pro` — Pro reasoning
- `deepseek/deepseek-v4-flash` — Fast general

**Note:** Namespaced model IDs. Requires prepending `deepseek/`. Pay-per-use pricing.

---

### 7. Halo Proxy → b.ai

**Endpoint:** Local `http://127.0.0.1:3457/v1`  
**Format:** OpenAI-compatible  
**Auth:** API key (free tier)

**How it works:** b.ai (api.b.ai) is a OneAPI-based provider that accepts Claude Code CLI headers. The Halo proxy injects these headers — essentially free Claude Code access.

**Model:**
- `minimax-m3` — best general purpose

**Setup:**
```bash
git clone https://github.com/nicepkg/halo-proxy.git
# Configure to forward to api.b.ai with CC header injection
# Run on localhost:3457
```

---

### 8. OpenRouter

**Endpoint:** `https://openrouter.ai/api/v1`  
**Format:** OpenAI-compatible  
**Auth:** API key (free credits on signup)

**200+ models** from all major providers. Pay-as-you-go with small free credit on registration.

---

### 9. Cloudflare Workers AI

**Endpoint:** `https://api.cloudflare.com/client/v4/accounts/{account_id}/ai/v1`  
**Format:** OpenAI-compatible (v2 API)  
**Auth:** Cloudflare API Token

**Free tier:** 10,000 requests per day  
**Requires:** Cloudflare account with R2/Workers enabled

---

### 10. Google Gemini API

**Endpoint:** `https://generativelanguage.googleapis.com/v1beta`  
**Format:** Google-specific (OpenAI-compatible via translation layer)  
**Auth:** API key (free tier, 60 requests/min)

**Free models:**
- `gemini-1.5-flash` — 60 req/min
- `gemini-1.5-pro` — 2 req/min
- `gemini-2.0-flash` — Newer, faster

> Pro tip: Use LiteLLM as a translation layer to access Gemini via OpenAI-compatible API.

---

## 9Router Configuration

### What is 9Router?

[9Router](https://github.com/9router/9router) is a lightweight, self-hosted LLM API proxy/router. It:
- Routes requests to multiple providers
- Manages API keys and failover
- Provides OpenAI-compatible unified endpoint
- Supports load balancing and OAuth

### Provider Strategy Setup

```javascript
// In 9Router settings.providerStrategies
{
  "opencode": {
    "fallbackStrategy": "round-robin",
    "stickyRoundRobinLimit": 1
  },
  "openmodel": {
    "fallbackStrategy": "round-robin",
    "stickyRoundRobinLimit": 1
  }
}
```

### Database Architecture

9Router stores provider configs in SQLite at `~/.9router/db/data.sqlite`. Three tables must be in sync:

1. **`providerNodes`** — Provider definition (type, baseUrl, prefix)
2. **`providerConnections`** — API keys and connection state
3. **`settings.providerStrategies`** — Routing strategy per provider

### Quick Prefix Reference

| Prefix | Provider | Notes |
|--------|----------|-------|
| `om/` | OpenModel | Anthropic-compatible |
| `oc/` | OpenCode Zen | Free tier |
| `sn/` | SambaNova | Free signup |
| `tr/` | TokenRouter | Namespaced models |
| `ol/` | Halo Proxy | Localhost:3457 |

---

## Proxy & Bypass Techniques

When datacenter IPs get blocked (common with free providers), here are proven bypass strategies:

### 1. Cloudflare Workers Relay

```javascript
// Deploy as a Cloudflare Worker
export default {
  async fetch(request) {
    const url = new URL(request.url);
    const target = url.searchParams.get('target');
    if (!target) return new Response('Missing target', { status: 400 });
    
    const headers = new Headers(request.headers);
    headers.set('X-Forwarded-For', '');
    
    const resp = await fetch(target, {
      method: request.method,
      headers: headers,
      body: request.body
    });
    
    return resp;
  }
}
```

Deploy with: `npx wrangler deploy`

Each Cloudflare Worker gets rotating egress IPs (anycast network) — effectively unlimited.

### 2. WARP SOCKS5 Proxy

```bash
# Install Cloudflare WARP
warp-cli register
warp-cli set-mode proxy
warp-cli connect

# Use SOCKS5 proxy at 127.0.0.1:1080
curl -x socks5://127.0.0.1:1080 https://api.provider.com/v1/models
```

### 3. Rate Limit Rotation

Configure 9Router proxy pool with multiple Cloudflare Worker relays for round-robin IP rotation:

```yaml
# proxy pool config
name: OpenCode-Zen-Relay
type: cloudflare-worker
proxies:
  - url: https://relay-1.workers.dev
  - url: https://relay-2.workers.dev
  - url: https://relay-3.workers.dev
rotation: round-robin
```

---

## Contribute

Found a new free provider, working model, or bypass technique? PRs welcome!

1. Fork the repo
2. Add your provider to the table and details section
3. Submit a pull request

---

## ⚠️ Disclaimer

- Free tiers may change or disappear without notice
- Always check the provider's ToS before production use
- Rate limits and availability are subject to change
- This guide is for educational purposes
