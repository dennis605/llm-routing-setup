---
layout: default
title: Troubleshooting
---

# Troubleshooting

## Schnellprüfung

1. Lauscht OmniRoute auf `20129`?
2. Lauschen CCR Gateway und UI auf `3456` und `3458`?
3. Zeigen alle drei Claude-Code-Base-URL-Variablen auf `127.0.0.1:3456`?
4. Ist `CLAUDE_CODE_ENABLE_GATEWAY_MODEL_DISCOVERY=1` gesetzt?
5. Ist in CCR der Provider `OmniRoute` erreichbar und mindestens ein Modell ausgewählt?
6. Wurde Claude Code nach einer Änderung vollständig neu gestartet?

## `/model` zeigt nur Claude-artige Modelle

Wahrscheinliche Ursache: Claude Code fragt noch direkt OmniRoute ab oder verwendet einen alten Cache. Prüfen, ob `ANTHROPIC_BASE_URL` tatsächlich `http://127.0.0.1:3456` ist. Danach Claude Code beenden und neu starten. Falls nötig den Gateway-Modellcache sichern und neu erzeugen lassen.

Ein starker Hinweis auf einen alten Direktpfad ist ein Gateway-Cache, dessen `baseUrl` weiterhin `http://127.0.0.1:20129` enthält.

## CCR meldet „No available models“

CCR hat noch keinen Provider mit mindestens einem exponierten Modell. In der Web UI OmniRoute als Custom Provider hinzufügen, Verbindung prüfen und wenigstens ein Modell auswählen. Erst danach das Gateway starten beziehungsweise das Agent-Profil öffnen.

## CCR sieht OmniRoute nicht

- Upstream muss `http://127.0.0.1:20129` sein.
- OmniRoute muss auf dem Mac mini laufen, nicht auf einem anderen Host-Namespace.
- Prüfen, ob OmniRoute die erwarteten Protokoll-Endpunkte anbietet.
- API-Zugang nur lokal prüfen; Token niemals in Logs, Screenshots oder Tickets kopieren.

## Modell erscheint doppelt

Gleicher Anzeigename bedeutet nicht gleiche Route. `qct/deepseek-v4-pro` und `qwen-cloud-token-plan/deepseek-v4-pro` sind unterschiedliche vollständige IDs. In CCR die nicht benötigte Variante abwählen oder die volle ID in der Dokumentation und bei Tests verwenden.

## Gewähltes Modell wird nicht verwendet

Prüfen, welche Ebene den Default setzt: `/model`, `ANTHROPIC_MODEL`, `CCR_CLAUDE_CODE_MODEL`, Wrapper-Default oder `CLAUDE_CODE_SUBAGENT_MODEL`. Subagenten können absichtlich ein anderes Modell als die Hauptsession verwenden.

## Sehr lange Requests brechen ab

Client- und Gateway-Timeout gemeinsam betrachten. Claude Code verwendet lokal einen langen `API_TIMEOUT_MS`; CCR besitzt zusätzlich einen eigenen API-Timeout. Ein Upstream-Provider oder OmniRoute kann trotzdem früher abbrechen.

## CCR Web UI lässt sich nicht öffnen

Die UI läuft auf `127.0.0.1:3458`, nicht auf dem Gateway-Port. Der vollständige Start-Link kann ein kurzlebiges oder sensibles Web-Token enthalten. Diesen Link weder veröffentlichen noch in Screenshots unredigiert zeigen.

## claude-mem meldet sich doppelt

Nach manuellen `worker-service.cjs`-Hooks suchen. Wenn das Plugin aktiv ist, alte manuelle Duplikate nach einem Backup entfernen. Details: [claude-mem Hooks](claude-mem.html).

