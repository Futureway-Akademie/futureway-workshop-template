# Futureway Workshop Repository Template

Dieses Repository ist das technologie- und KI-anbieterunabhängige Master-Template für einen geführten Futureway-Workshop. Die konkrete Projektidee wird erst nach dem Klonen in einem daraus erzeugten Teilnehmer-Repository definiert.

Coding-Agenten müssen vor jeder Änderung die Workshop-Dateien lesen. Projektplanung, Fortschritt und wichtige Entscheidungen werden zentral im Repository dokumentiert, damit jederzeit zwischen kompatiblen Coding-Agenten gewechselt werden kann. Eine vorherige Chat-Historie ist nicht erforderlich.

## Für Coding-Agenten

- Codex: Lies zuerst `AGENTS.md`.
- Claude Code: Lies zuerst `CLAUDE.md`.
- Andere Coding-Agenten: Lies zuerst `.agents/generic/INSTRUCTIONS.md`.

Danach gelten für alle Agenten dieselben autoritativen Dateien unter `.workshop/`.

## Verwendung

Dieses Repository wird als Master-Template gepflegt. Erst in einem abgeleiteten Teilnehmer-Repository werden Projektidee, Workshop-Typ, Technologie, Starter-Code und projektspezifische Rahmenbedingungen festgelegt.

## Workshop Repository Standard v1.1

Der Standard trennt den zentralen Projektzustand unter `.workshop/` von den schlanken, tool-spezifischen Adaptern. Roadmap, Task-Verifikation und abgeleiteter Fortschritt bleiben dadurch auch bei einem Agentenwechsel nachvollziehbar. Das Dashboard arbeitet mit dem synchronisierten Repository-Stand; lokale, noch nicht synchronisierte Änderungen sind dort nicht automatisch sichtbar.

Dieses Master-Template wird später in konkrete Workshop-Repositories abgeleitet. Workshop-Typ, Technologien, Starter-Code, Setup, technische Constraints, Quality Gate und erlaubte Tools oder Libraries werden dort primär unter `.workshop/specialization/` ergänzt. Der zentrale Workflow in `.workshop/AGENT_PROTOCOL.md` bleibt davon unabhängig und darf durch die Spezialisierung nicht überschrieben werden.

## Erwarteter Erststart

Wenn ein frisch erzeugtes Teilnehmerrepository noch nicht initialisiert ist und der Nutzer beispielsweise `starte` eingibt, lautet die erwartete Agent-Antwort: `Was möchtest du entwickeln?`

Danach wartet der Agent auf die Projektidee.
