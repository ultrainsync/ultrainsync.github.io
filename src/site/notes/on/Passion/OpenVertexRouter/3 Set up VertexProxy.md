---
{"dg-publish":true,"dg-path":"OpenVertexRouter/3 Set up VertexProxy.md","permalink":"/open-vertex-router/3-set-up-vertex-proxy/","created":"2026-07-08T01:57:13","updated":"2026-07-08T11:50:36","dg-note-properties":{"ID":"scn_b25175cb","Class":"Scene","Act":1,"cssclasses":["wide-page"],"operonId":"a32aywk","operonProjectStage":"Default.Complete","priority":"Press","datetimeCreated":"2026-07-08T01:57:13","timestamp":"2026-07-08T11:50:36","Status":"Complete","description":"have another Vertex Proxy running on AiM1 (MacOS) - OpenAI Chat Completions","resource":["https://github.com/NousResearch/hermes-agent/issues/13484#issue-4302284932"],"dateCompleted":"2026-07-08"}}
---

# Feature: native Google Cloud Vertex AI provider support

## Context

Hermes currently has no working path for Google Cloud Vertex AI. The entry in `HERMES_OVERLAYS` for `google-vertex` exists, but there's no auth machinery: Vertex uses short-lived OAuth access tokens derived from a service-account JSON, not a static API key. As a result, setting `fallback_model.provider: google-vertex` silently fails with "provider not configured" on every cron tick. Happy to share log excerpts.

This came up in a solo-founder setup where the goal was to use GCP credits to cover primary Gemini plus fallback inference (burning credits instead of paying cash), but the provider plumbing blocks that path today.

## What I built in the meantime (happy to upstream)

I wrote a standalone proxy that sits on `127.0.0.1:8787` , handles GCP service-account auth plus 50-minute token refresh, and exposes Anthropic Messages API, Gemini generateContent API, and OpenAI Chat Completions endpoints backed by Vertex AI.

Repo: [https://github.com/prasadus92/vertex-proxy](https://github.com/prasadus92/vertex-proxy)

The Hermes integration is zero-code. It works through the existing `custom_providers` mechanism:

```
custom_providers :
  - name : vertex-gemini base_url : http://127.0.0.1:8787/gemini transport : openai_chat - name : vertex-anthropic base_url : http://127.0.0.1:8787/anthropic transport : anthropic_messages
fallback_model : provider : vertex-gemini model : gemini-2.5-pro
```

So for users like me, the external shim is already a complete solution. The open question is whether Hermes wants a **native** Vertex path that avoids the localhost hop.

# Vertex Proxy Setup Handoff for AiM1 Aigents
resources:
- [gemini-3.5-flash](https://docs.cloud.google.com/gemini-enterprise-agent-platform/models/gemini/3-5-flash)
- [Gemini Embedding 2](https://docs.cloud.google.com/gemini-enterprise-agent-platform/models/gemini/embedding-2)
- [Endpoints](https://docs.cloud.google.com/gemini-enterprise-agent-platform/resources/locations)
## 🎯 Goal
Get the Python-based `vertex-proxy` running on AiM1 to seamlessly intercept OpenAI API calls and translate them into Google Vertex AI requests using full-credit Service Account keys.

## 🚀 Step-by-Step Setup for AiM1

### 1. Clone the Proxy
Since the `.tmp` folder is ignored by Syncthing, the proxy code won't sync automatically. You must clone it on AiM1:
```bash
git clone https://github.com/Adsvise/VertexProxy.git
cd VertexProxy
```

### 2. Configure Environment
A `.env.example` template was added to the repo. Copy it or directly set these environment variables:
```bash
# Point this to your Syncthed PnC key location
export VERTEX_PROXY_CREDENTIALS_PATH="/path/to/your/ServiceAcct(Aigent)for-VertexProxy.json"

# Bind to all interfaces for Tailscale access
export VERTEX_PROXY_HOST="0.0.0.0"

# Fix 404 Model Not Found errors by setting regions to global
export VERTEX_PROXY_ANTHROPIC_REGION="global"
export VERTEX_PROXY_GEMINI_REGION="global"
export VERTEX_PROXY_MAAS_REGION="global"
```

### 3. Install and Run
Set up the virtual environment and launch:
```bash
python -m venv .venv
.venv/bin/pip install -e .
.venv/bin/vertex-proxy
```

## 🔌 Client Configuration (for Aigents)
Once running, configure your AI extensions (Claude Code, Hermes, Kilo, Cline, etc.):
*   **Provider:** OpenAI Compatible
*   **Base URL:** `http://127.0.0.1:8787/v1` (or `http://100.106.247.99:8787/v1` over Tailscale)
*   **Model:** `gemini-3.5-flash`
*   **API Key:** `sk-anything` (ignored by proxy)

You are now running `gemini-3.5-flash` natively translated from OpenAI calls, securely hitting the Google Cloud project!

---
## ⚠️ Important Details & Fixes Applied (July 2026)

### Native Background Service (`launchd`)
To ensure the proxy runs 24/7 without manual intervention, a `launchd` plist was deployed to `~/Library/LaunchAgents/ai.hermes.vertex-proxy.plist` with all the necessary `global` environment variables. The proxy now automatically starts on boot.

### Migration to Adsvise/VertexProxy (July 2026)
The Vertex Proxy was successfully migrated to the official **`Adsvise/VertexProxy`** repository:
1. **Repository Swapped:** Replaced the legacy repository folder `/Users/aigent/vertex-proxy` with the new repository cloned from `https://github.com/Adsvise/VertexProxy.git`.
2. **Environment Active:** Configured `/Users/aigent/vertex-proxy/.env` using the `.env.example` template, setting `VERTEX_PROXY_CREDENTIALS_PATH` to point to `/Volumes/ROG Flow SSD/KB/Private/PnC/ServiceAcct(Aigent)for-VertexProxy.json`.
3. **Environment Built:** Re-created the python virtual environment (`.venv`) and installed dependencies via `uv pip install -e .`.
4. **Daemon Rebooted:** Successfully restarted the launchd background daemon `ai.hermes.vertex-proxy.plist` and confirmed clean startup listening on `http://0.0.0.0:8787`.

