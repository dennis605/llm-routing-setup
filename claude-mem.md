---
layout: default
title: claude-mem Hooks
---

# claude-mem: Doppelhook und Bereinigung

## Symptom

Beim Start einer Claude-Code-Session erschien der komplette `claude-mem status`-Block zweimal.

## Ursache

`claude-mem@thedotmack` war als Plugin aktiviert. Zusätzlich enthielt `~/.claude/settings.json` sechs manuell eingetragene Aufrufe von `worker-service.cjs`:

- `observation`
- `file-context`
- `start`
- `context`
- `summarize`
- `session-init`

Damit registrierten sowohl das Plugin als auch die alten manuellen Einstellungen dieselben Funktionspfade.

## Bereinigung

Nach einer Sicherung von `settings.json` wurden nur die sechs manuellen claude-mem-Hooks entfernt. Das Plugin selbst blieb aktiviert und registriert seine Hooks weiterhin eigenständig.

## Verifikation

Claude Code wurde vollständig beendet und neu mit Debug-Ausgabe gestartet. Der `SessionStart:startup`-Status erschien danach genau einmal. Zwei Werbe-/Hinweiszeilen innerhalb desselben Statusblocks sind keine zweite Hook-Ausführung.

## Dauerhafte Regel

claude-mem entweder als Plugin verwalten **oder** manuelle Hooks pflegen, nicht beides parallel. Bei einem Update zuerst prüfen, ob das Plugin seine Hooks selbst registriert, bevor Einträge nach `settings.json` kopiert werden.

