---
layout: default
title: Architektur
---

# Architektur

## Datenfluss

```mermaid
sequenceDiagram
  participant U as Nutzer
  participant C as Claude Code
  participant R as CCR :3456
  participant O as OmniRoute :20129
  participant P as Provider

  U->>C: Prompt / Tool-Aufgabe
  C->>R: Anthropic-kompatibler Request + Modell-ID
  R->>R: Agent-Profil und Modellfreigabe prüfen
  R->>O: Request im erkannten Upstream-Protokoll
  O->>O: Provider, Konto, Alias, Combo oder Fallback wählen
  O->>P: Provider-spezifischer Request
  P-->>O: Modellantwort
  O-->>R: Normalisierte Antwort
  R-->>C: Claude-Code-kompatible Antwort
  C-->>U: Text / Tool-Aktion
```

## Verantwortungsgrenzen

### Claude Code

- startet Sessions und Agenten,
- zeigt den `/model`-Picker,
- liest die Gateway-Modellliste, wenn Discovery aktiviert ist,
- sendet Requests an den konfigurierten Anthropic-Basis-Endpunkt.

### CCR

- ist der einzige direkte Gateway-Endpunkt für Claude Code,
- exponiert eine kuratierte Liste von OmniRoute-Modellen,
- hält das Claude-Code-Agent-Profil und den Default fest,
- erkennt am OmniRoute-Upstream mehrere Protokolle, darunter OpenAI Chat Completions, OpenAI Responses, Anthropic Messages und Gemini Generate Content.

### OmniRoute

- verwaltet Provider-Verbindungen und deren Zugangsdaten,
- mappt Modell-IDs auf konkrete Providerpfade,
- kann Konten verteilen, Combos, Aliase, Retries und Fallbacks einsetzen,
- normalisiert Requests und Responses zwischen Protokollen.

## Warum zwei Router?

OmniRoute ist der allgemeine Multi-Provider-Router. CCR ist die Claude-Code-spezifische Präsentationsschicht. Diese Aufteilung vermeidet, dass Claude Code den gesamten heterogenen OmniRoute-Katalog selbst interpretieren muss.

Der zusätzliche Hop ist lokal und bringt einen klaren Vorteil: Die in Claude Code sichtbare Modellliste wird von CCR kontrolliert, während die bereits eingerichteten OmniRoute-Provider unverändert weiterverwendet werden.

