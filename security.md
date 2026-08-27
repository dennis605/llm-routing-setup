---
layout: default
title: Sicherheit
---

# Sicherheit und Redaction

## Was niemals in dieses Repository gehört

- API-Keys, OAuth-Tokens, Identity Tokens und Session-Cookies
- CCR-Service- oder Web-UI-Tokens
- OmniRoute Access Tokens und Provider-Zugangsdaten
- Inhalte lokaler `.env`-Dateien
- private Schlüssel, Zertifikate und SSH-Material
- vollständige lokale Datenbanken oder Backups
- unredigierte Debug-Logs, Transkripte oder Screenshots
- persönliche Organisations-, Federation- oder Konto-IDs, sofern sie für die Architektur nicht nötig sind

## Erlaubte Platzhalter

```text
API_KEY=<REDACTED>
AUTH_TOKEN=<REDACTED>
CCR_WEB_TOKEN=<REDACTED>
```

Keine realistisch aussehenden Beispieltokens verwenden. Auch abgelaufene Tokens werden nicht veröffentlicht.

## Redaction-Regeln vor jedem Commit

1. Nur handgeschriebene Dokumentationsdateien stagen; niemals ganze Konfigurationsverzeichnisse kopieren.
2. Nach Begriffen wie `token`, `secret`, `password`, `api_key`, `authorization`, `cookie`, `private key` und `identity` suchen.
3. Nach typischen Token-Formaten und langen zufälligen Zeichenketten suchen.
4. Diff vollständig lesen, einschließlich gelöschter oder umbenannter Dateien.
5. Screenshots separat visuell prüfen: Adresszeile, Query-Parameter, QR-Codes und Accountdaten redigieren.
6. Erst nach bestandenem Scan pushen.

Beispiel für einen lokalen Vorabscan:

```bash
git diff --cached --check
git grep -nEi '(api[_-]?key|token|secret|password|authorization|cookie|private[_ -]?key)'
```

Treffer sind nicht automatisch Secrets: Diese Sicherheitsseite enthält die Suchbegriffe selbst. Jeder Treffer muss deshalb manuell klassifiziert werden.

## Netzwerkgrenzen

CCR ist korrekt an `127.0.0.1` gebunden. OmniRoutes aktuelles Docker-Mapping veröffentlicht `20129` dagegen über `0.0.0.0`. Falls Zugriff aus dem LAN nicht beabsichtigt ist, sollte auf Loopback eingeschränkt und anschließend die Erreichbarkeit erneut geprüft werden.

## Falls doch ein Secret veröffentlicht wurde

1. Secret sofort beim ausstellenden Dienst widerrufen oder rotieren.
2. Erst danach Git-Historie bereinigen; das Löschen aus dem letzten Commit allein reicht nicht.
3. GitHub-Caches, Actions-Logs und veröffentlichte Pages-Artefakte berücksichtigen.
4. Betroffene Provider-Verbindungen testen und den Vorfall kurz dokumentieren, ohne das Secret zu wiederholen.

