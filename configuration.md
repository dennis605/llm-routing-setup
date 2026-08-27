---
layout: default
title: Konfiguration
---

# Konfiguration

## Ports und Bindings

| Komponente | Binding | Hinweis |
|---|---|---|
| CCR Gateway | `127.0.0.1:3456` | Ziel von Claude Code |
| CCR interner Core | `127.0.0.1:3457` | interne CCR-Komponente; kein normaler Benutzer-Endpunkt |
| CCR Web UI | `127.0.0.1:3458` | nur lokal öffnen; URL kann ein Web-Token enthalten |
| OmniRoute Host-Port | `0.0.0.0:20129` | Docker veröffentlicht Host-Port 20129 auf Container-Port 20128 |

> OmniRoute ist derzeit auf allen Host-Interfaces gebunden. Wenn kein LAN-Zugriff benötigt wird, sollte das Docker-Port-Mapping auf `127.0.0.1:20129:20128` eingeschränkt werden.

## Relevante Claude-Code-Variablen

Die Werte stehen lokal in `~/.claude/settings.json` unter `env`. In einer öffentlichen Dokumentation werden keine Tokens oder Identitätsdateien wiedergegeben.

| Variable | Aktuelle Rolle |
|---|---|
| `ANTHROPIC_BASE_URL` | Primärer Claude-Code-Basis-Endpunkt; zeigt auf `http://127.0.0.1:3456` |
| `ANTHROPIC_API_BASE_URL` | Kompatibilitäts-/Integrationspfad; zeigt ebenfalls auf CCR `:3456` |
| `CLAUDE_AGENT_API_BASE_URL` | Agent-bezogener Einstieg; zeigt ebenfalls auf CCR `:3456` |
| `CLAUDE_CODE_ENABLE_GATEWAY_MODEL_DISCOVERY` | Wert `1` aktiviert die Modellabfrage beim Gateway für `/model` |
| `ANTHROPIC_MODEL` | Default für neue Claude-Code-Sessions; derzeit CCR-Anzeigename für MiniMax M3 |
| `CCR_CLAUDE_CODE_MODEL` | von CCR verwalteter Default für das Claude-Code-Profil |
| `CODEXL_CLAUDE_CODE_MODEL` | zusätzlicher Wrapper-/Integrationsdefault |
| `CLAUDE_CODE_SUBAGENT_MODEL` | expliziter Default für Subagenten; derzeit ein DeepSeek-V4-Pro-Pfad |
| `API_TIMEOUT_MS` | langer Client-Timeout für umfangreiche Agent-Aufgaben |

Weitere lokal vorhandene Variablen für Federation, Organisation oder Identity Token Files sind installationsspezifisch und werden hier absichtlich nicht mit Werten dokumentiert.

## Gateway Model Discovery

Mit `CLAUDE_CODE_ENABLE_GATEWAY_MODEL_DISCOVERY=1` fragt Claude Code den Gateway-Katalog ab. Da Claude Code nun auf CCR zeigt, stammt diese Liste aus CCR und nicht direkt aus OmniRoute.

Der aktive CCR-Provider heißt `OmniRoute`, verwendet `http://127.0.0.1:20129` als Upstream, hat Auto-Fetch aktiviert und exponiert aktuell neun ausgewählte Modelle. Änderungen an dieser Auswahl erfolgen in CCR, Provider-Zugänge bleiben in OmniRoute.

## OmniRoute in Docker

Das lokale Compose-Prinzip lautet:

```yaml
services:
  omniroute:
    image: diegosouzapw/omniroute:latest
    restart: unless-stopped
    env_file:
      - .env
    environment:
      ENABLE_CC_COMPATIBLE_PROVIDER: "true"
    ports:
      - "0.0.0.0:20129:20128"
    volumes:
      - omniroute-data:/app/data
```

`ENABLE_CC_COMPATIBLE_PROVIDER=true` schaltet in OmniRoute einen speziellen Provider-Typ für externe Claude-Code-kompatible Relays frei. Der Schalter war **nicht** der entscheidende Mechanismus für die funktionierende Kette. Entscheidend waren CCR als vorgeschaltetes Gateway, das Claude-Code-Profil, die Discovery-Variable und CCRs eigener exponierter Modellkatalog.

