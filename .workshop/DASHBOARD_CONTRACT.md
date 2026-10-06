# Dashboard Contract v1

Diese Datei definiert die stabile Schnittstelle zwischen einem Teilnehmer-Repository und dem Workshop-Dashboard. Das Dashboard liest ausschließlich Repository-Dateien und benötigt keine Chat-Historie.

- Aktuelles JSON-Schema: `v1` (`schemaVersion: 1`)
- Workshop Protocol: `1.1.0`

## Dateien

| Pfad | Format | Zweck |
| --- | --- | --- |
| `.workshop/project.json` | JSON | Projektidentität und Projektstatus |
| `.workshop/roadmap.json` | JSON | Autoritative Phasen, Tasks, Abhängigkeiten und Status |
| `.workshop/progress.json` | JSON | Aus der Roadmap berechnete Aggregate |
| `.workshop/activity.jsonl` | JSON Lines, UTF-8 | Chronologisches sachliches Ereignisprotokoll |

Die Pfade werden zusätzlich in `.workshop/config.json` angegeben. Relative Pfade beziehen sich auf das Repository-Root.

## Allgemeine Regeln

- Jede strukturierte Hauptdatei besitzt `schemaVersion` mit dem ganzzahligen Wert `1`.
- JSON-Dateien verwenden UTF-8 und enthalten genau ein JSON-Dokument.
- `activity.jsonl` enthält pro nicht leerer Zeile genau ein JSON-Objekt; die Datei darf leer sein.
- Zeitstempel sind entweder `null` oder ISO-8601-Datumszeiten in UTC, beispielsweise `2026-01-15T10:30:00Z`.
- Fehlende Information wird mit dem laut Schema zulässigen `null` oder einem leeren Array dargestellt, nicht durch erfundene Werte.

## Stabile IDs

`projectId` sowie die `id`-Werte von Phasen und Tasks sind stabile technische Identifikatoren. Eine Änderung des sichtbaren Namens oder Titels verändert die ID nicht. Beispielsweise behält ein Task mit ID `task-3-2` diese ID auch dann, wenn sein Titel von „Login erstellen“ zu „Anmeldung und Sessionverwaltung“ geändert wird.

IDs abgeschlossener, abgebrochener oder ersetzter Elemente dürfen nicht wiederverwendet werden. Phasen-IDs sind innerhalb einer Roadmap eindeutig; Task-IDs sind innerhalb der gesamten Roadmap eindeutig.

## `project.json`

- `projectId`: stabile technische ID des Projekts oder `null` vor der Initialisierung
- `name`: menschenlesbarer Projektname oder `null`
- `status`: `not_initialized`, `planned`, `active`, `paused`, `completed` oder `archived`
- `workshopType`: später festgelegter Workshop-Typ oder `null`
- `owner`: Teilnehmerbezeichnung, sofern freiwillig festgelegt, sonst `null`
- `description`: kurze Projektbeschreibung oder `null`
- `createdAt`, `updatedAt`: Zeitstempel oder `null`

## `roadmap.json`

- `roadmapVersion`: bei wesentlichen Umfangsänderungen monoton erhöhte, nicht negative Ganzzahl; `0` bedeutet noch nicht initialisiert
- `status`: `not_initialized`, `planned`, `active`, `completed` oder `archived`
- `phases`: geordnete Liste der Projektphasen

Phasenstatus: `planned`, `ready`, `in_progress`, `blocked`, `completed`, `cancelled`. Der Phasenstatus soll nach Möglichkeit aus den enthaltenen Tasks abgeleitet und nicht unabhängig willkürlich gesetzt werden.

Taskstatus: `planned`, `ready`, `in_progress`, `blocked`, `completed`, `cancelled`, `superseded`.

`dependsOn` enthält existierende Task-IDs. Ein Task darf nur `ready` sein, wenn alle referenzierten Tasks `completed` sind. Abhängigkeiten starten keinen Task automatisch. Ist eine Abhängigkeit `cancelled`, `superseded` oder `blocked`, ist eine alternative Abhängigkeit oder Roadmap-Anpassung zu prüfen. `definitionOfDone` enthält prüfbare Kriterien. `startedAt` und `completedAt` dokumentieren den tatsächlichen Ablauf.

Jeder Task enthält ein `verification`-Objekt:

```json
{
  "status": "not_run",
  "summary": null,
  "commands": [],
  "checkedAt": null
}
```

Zulässige Verifikationsstatus sind `not_run`, `passed`, `failed` und `partial`. `commands` enthält nur tatsächlich ausgeführte Befehle oder Checks; manuelle Prüfungen werden sachlich in `summary` beschrieben. Ein `completed` Task benötigt `verification.status: passed`, einen Verifikationszeitpunkt und einen Abschlusszeitpunkt. Nicht ausgeführte Prüfungen dürfen nicht als bestanden erscheinen.

Standardmäßig darf höchstens ein Task `in_progress` sein. Mehrere aktive Tasks sind nur gültig, wenn eine Spezialisierungsdatei oder die Roadmap parallele Arbeit ausdrücklich erlaubt.

`order` bestimmt die Anzeige innerhalb der jeweiligen Ebene. `weight` ist das nicht negative relative Gewicht für die Fortschrittsberechnung. Task-IDs sind innerhalb der gesamten Roadmap eindeutig; Phasen-IDs sind ebenfalls eindeutig.

`roadmapVersion` wird beispielsweise bei einer neuen Phase, mehreren neuen Tasks, einem wesentlich geänderten MVP oder ersetzten Tasks erhöht. Reine Statusübergänge wie `ready` → `in_progress` → `completed` erfordern nicht zwingend eine neue Version. Wesentliche Änderungen werden als `roadmap_updated` protokolliert. Abgeschlossene Tasks bleiben in der Historie erhalten.

## `progress.json`

`roadmap.json` ist die fachliche Quelle der Wahrheit. `progress.json` ist nur ein daraus neu berechenbarer Cache/Aggregatzustand für Dashboard, schnelle Anzeige und Agentenorientierung. Bei Widerspruch gewinnt immer `roadmap.json`; `progress.json` muss daraus neu berechnet werden.

- `totalWeight`: Summe der Gewichte aller zum aktiven Projektumfang gehörenden Tasks
- `completedWeight`: Summe der Gewichte aller `completed` Tasks im aktiven Projektumfang
- `overall`: bei `totalWeight > 0` der Wert `completedWeight / totalWeight * 100`, auf zwei Dezimalstellen gerundet; sonst `0`
- `totalTasks`: Anzahl aller Tasks im aktiven Projektumfang
- `completedTasks`: Anzahl der `completed` Tasks im aktiven Projektumfang
- `blockedTasks`: Anzahl der `blocked` Tasks im aktiven Projektumfang
- `currentPhaseId`: ID der aktiven Phase oder `null`
- `currentTaskId`: ID des aktiven Tasks oder `null`
- `updatedAt`: Zeitpunkt der letzten Neuberechnung oder `null`, solange keine Roadmap existiert

Zum aktiven Projektumfang zählen Tasks mit Status `planned`, `ready`, `in_progress`, `blocked` oder `completed`. `cancelled` Tasks zählen weder zum Nenner noch zum abgeschlossenen Gewicht. `superseded` Tasks zählen nicht, wenn eine Nachfolgeaufgabe sie ersetzt. Das Phasengewicht wird nicht zusätzlich mit Task-Gewichten multipliziert; die Berechnung erfolgt ausschließlich aus Task-Gewichten.

```text
completedWeight = Summe der Gewichte aller completed Tasks im aktiven Projektumfang
totalWeight     = Summe der Gewichte aller Tasks im aktiven Projektumfang
overall         = totalWeight > 0 ? completedWeight / totalWeight * 100 : 0
```

`planned`, `ready`, `in_progress` und `blocked` zählen vollständig zu `totalWeight`, aber mit `0` zu `completedWeight`. Fortschritt wird niemals von einer KI geschätzt. Ein Task trägt entweder `0` oder sein vollständiges Gewicht zum abgeschlossenen Fortschritt bei; anteilige Angaben wie „60 % fertig“ sind unzulässig. Das Dashboard darf Aggregate neu berechnen und Abweichungen in `progress.json` als Konsistenzfehler behandeln.

## `activity.jsonl`

Jeder Eintrag enthält mindestens:

- `timestamp`: ISO-8601-Datumszeit in UTC
- `type`: stabiler Ereignistyp
- `summary`: kurze sachliche Zusammenfassung

Unterstützte sinnvolle Ereignistypen sind mindestens `project_initialized`, `roadmap_created`, `roadmap_updated`, `task_ready`, `task_started`, `task_blocked`, `task_unblocked`, `task_completed`, `task_cancelled`, `task_superseded`, `verification_passed`, `verification_failed` und `decision_recorded`.

Taskbezogene Ereignisse sollen zusätzlich `taskId` enthalten. Vollständige Nutzerprompts, Geheimnisse, personenbezogene Daten, Chain-of-Thought und interne Denkprozesse werden nicht protokolliert.

## Synchronisierter Dashboard-Stand

Lokale Änderungen im Arbeitsverzeichnis sind nicht automatisch im Dashboard sichtbar. Maßgeblich ist der zuletzt synchronisierte Repository-Stand des konfigurierten Branches beziehungsweise der Git-Stand, den das Dashboard tatsächlich liest. Agenten und Dashboard dürfen einen lokal berechneten Fortschritt nicht als bereits sichtbaren Dashboard-Stand ausgeben.

Nach einem Task-Abschluss ist zwischen lokalem Projektstand und synchronisiertem Stand zu unterscheiden. Wenn der Workshop einen Push durch den Teilnehmer vorsieht, soll der Agent auf die notwendige Synchronisierung hinweisen, aber keinen automatischen Push verlangen, falls das Tool oder Repository anders konfiguriert ist.

## Versionierung

Rückwärtskompatible optionale Ergänzungen dürfen innerhalb von `schemaVersion: 1` erfolgen, sofern alte Leser unbekannte Felder ignorieren können und die Ergänzung dokumentiert ist. Jede inkompatible Änderung an JSON-Struktur, Feldnamen, Typen, Pflichtfeldern oder Bedeutungen benötigt eine neue `schemaVersion` und entsprechend versionierte Schemas. Ein Verbraucher darf eine unbekannte `schemaVersion` nicht stillschweigend als kompatibel behandeln. Die Protokollversion ist davon getrennt und steht in `.workshop/config.json`.

