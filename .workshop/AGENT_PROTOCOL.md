# Verbindliches Agentenprotokoll

Diese Datei ist die autoritative Prozessquelle für jeden Coding-Agenten. Tool-spezifische Adapter dürfen diesen Ablauf weder duplizieren noch verändern. Maschinenlesbarer Projektzustand wird nur unter `.workshop/` gepflegt.

## A. Beim ersten Öffnen

Lies in dieser Reihenfolge:

1. `.workshop/config.json`
2. `.workshop/project.json`
3. `.workshop/roadmap.json`
4. `.workshop/progress.json`
5. `.workshop/CURRENT_STATE.md`
6. die relevanten Dateien unter `.workshop/specialization/`
7. die für die aktuelle Arbeit relevante Dokumentation

Wenn `project.status` den Wert `not_initialized` hat, darf noch keine Anwendung programmiert oder technisch eingerichtet werden. Frage zuerst sinngemäß genau:

> Was möchtest du entwickeln?

Der Teilnehmer darf seine Idee frei beschreiben. Bestimme die Idee nicht ungefragt selbst.

Ist das Projekt bereits initialisiert, melde dem Nutzer nach dem Lesen kurz das erkannte Projekt, die aktuelle Phase, die aktuelle Aufgabe, die zuletzt abgeschlossene Aufgabe und den empfohlenen nächsten Schritt zurück. Halte diese Bestätigung knapp; sie zeigt, dass der zentrale Zustand korrekt übernommen wurde.

## B. Nach der Projektidee

Analysiere die Idee, aber beginne noch nicht mit der Implementierung. Leite zunächst ab:

- Projektname
- Problem oder Idee
- Zielgruppe
- Zielplattform
- Kernfunktionen
- Nicht-Ziele
- MVP
- technische Rahmenbedingungen
- projektweite Definition of Done

Bereite daraus den Projektbrief und einen Roadmap-Vorschlag mit Phasen, Tasks, Abhängigkeiten, Task-Definition-of-Done, Verifikation und Gewichten vor. Erstelle oder aktualisiere dabei gemeinsam und konsistent:

- `.workshop/PROJECT_BRIEF.md`
- `.workshop/project.json`
- `.workshop/roadmap.json`
- `.workshop/progress.json`
- `.workshop/CURRENT_STATE.md`

Zerlege das Vorhaben in sinnvolle Entwicklungsphasen und jede Phase in überschaubare Tasks. Ein Task soll normalerweise in einer Arbeitssitzung sinnvoll abschließbar und prüfbar sein. Teile pauschale Großaufgaben wie „Backend bauen“ weiter auf. Dokumentiere Task-Abhängigkeiten in `dependsOn` und konkrete Abnahmekriterien in `definitionOfDone`.

Zeige dem Nutzer danach eine kurze, verständliche Zusammenfassung und frage genau:

> Soll ich diese Projektstruktur so vorbereiten?

Vor der Zustimmung darf ein gespeicherter Entwurf höchstens den Status `planned` besitzen und gilt nicht als aktive Roadmap. Erst nach der Zustimmung darfst du `project.status` und `roadmap.status` auf `active` setzen, `roadmapVersion` auf mindestens `1` setzen, die Roadmap als aktuelle Version speichern und die Initialisierung sachlich im Activity Log erfassen. Vor dieser Zustimmung darf keine Projektimplementierung beginnen.

## C. Nach der Planung

Frage:

> Womit möchtest du starten?

Zeige dabei den empfohlenen nächsten Task und weitere Tasks mit Status `ready`. Leite die Auswahl ausschließlich aus `roadmap.json` ab. Zeige `planned` Tasks mit offenen Abhängigkeiten sowie `blocked`, `completed`, `cancelled` und `superseded` Tasks nicht als direkt startbar an.

## D. Nach der Task-Auswahl

Implementiere noch nicht automatisch. Bitte den Teilnehmer zunächst um seine Vorstellungen zur ausgewählten Teilaufgabe, zum Beispiel:

> Beschreibe jetzt, wie du diese Teilaufgabe umsetzen möchtest. Du kannst Funktionen, Design, Verhalten oder besondere Anforderungen nennen.

Erst nach dieser Eingabe darf die Implementierung beginnen.

## E. Während der Implementierung

Bearbeite nur die ausgewählte Aufgabe. Kleine, notwendige Abhängigkeiten dürfen ergänzt werden. Ziehe keine großen zukünftigen Phasen ungefragt vor. Beachte bestehende Projektregeln und halte wesentliche neue Entscheidungen im Repository fest.

Setze den Task beim tatsächlichen Arbeitsbeginn auf `in_progress`, pflege `startedAt` und aktualisiere den zentralen Zustand. Verwende ISO-8601-Zeitstempel in UTC.

Standardmäßig darf im gesamten Projekt höchstens ein Task `in_progress` sein. Eine Ausnahme ist nur zulässig, wenn die Roadmap oder eine Spezialisierungsdatei parallele Arbeit ausdrücklich erlaubt. Starte niemals eigenständig mehrere große Tasks gleichzeitig.

## F. Nach Abschluss

Bevor ein Task den Status `completed` erhält:

1. Stelle sicher, dass die zugehörige Implementierung tatsächlich im Repository vorhanden ist.
2. Prüfe alle Punkte der `definitionOfDone`.
3. Führe relevante Tests und Checks aus.
4. Dokumentiere das Ergebnis im `verification`-Objekt des Tasks.

Melde nicht ausgeführte Tests niemals als erfolgreich. Aktualisiere danach konsistent:

- `.workshop/roadmap.json`
- `.workshop/progress.json`
- `.workshop/activity.jsonl`
- `.workshop/CURRENT_STATE.md`
- relevante Dokumentation

Setze `completedAt` erst beim belegten Abschluss. Ein Task darf niemals allein durch eine Änderung an `roadmap.json` auf `completed` gesetzt werden. Umgekehrt müssen nach einer erfolgreich umgesetzten und geprüften Aufgabe mindestens `roadmap.json`, `progress.json`, `CURRENT_STATE.md` und `activity.jsonl` sowie bei Bedarf relevante Dokumentation aktualisiert werden.

Berechne Fortschritt aus der Roadmap nach dem Dashboard-Vertrag; schätze ihn nicht. Zeige anschließend die aus der aktualisierten Roadmap abgeleiteten, möglichen nächsten Tasks.

## G. Agentenwechsel

Der Teilnehmer darf jederzeit den Coding-Agenten wechseln. Der neue Agent besitzt keine Chat-Historie. Deshalb darf keine wichtige Projektinformation ausschließlich im Chat verbleiben. Aktualisiere nach wesentlichen Entscheidungen und Aufgaben die zentralen Repository-Dateien. Erstelle niemals getrennte Roadmaps, Fortschrittsstände oder Projektdokumentationen für einzelne Agenten.

Beim Öffnen eines initialisierten Projekts liest ein neuer Agent mindestens `project.json`, `roadmap.json`, `progress.json`, `CURRENT_STATE.md` und die relevanten Spezialisierungsdateien, bevor er arbeitet. Anschließend folgt die kurze Zustandsbestätigung aus Abschnitt A.

## H. Task-Lifecycle

Zulässige Taskstatus sind `planned`, `ready`, `in_progress`, `blocked`, `completed`, `cancelled` und `superseded`.

Erlaubte Standardübergänge:

- `planned` → `ready`
- `ready` → `in_progress`
- `in_progress` → `completed`
- `in_progress` → `blocked`
- `blocked` → `ready`
- `blocked` → `in_progress`
- `planned`, `ready` oder `blocked` → `cancelled`
- `planned`, `ready` oder `blocked` → `superseded`

Andere Übergänge erfordern eine ausdrücklich dokumentierte Roadmap-Korrektur und dürfen die Historie nicht verfälschen. Ein `completed` Task darf nicht stillschweigend zurückgesetzt, gelöscht oder nachträglich zu `superseded` geändert werden. Wird seine Arbeit später ersetzt, bleibt der abgeschlossene Task historisch erhalten; eine neue geplante Nachfolgeaufgabe dokumentiert die Ersetzung. IDs abgeschlossener, abgebrochener oder ersetzter Tasks werden niemals wiederverwendet.

## I. Abhängigkeiten

Ein Task darf nur `ready` sein, wenn jede ID in `dependsOn` auf einen vorhandenen Task mit Status `completed` verweist. Abhängige Tasks starten nie automatisch. Ist eine Abhängigkeit `cancelled`, `superseded` oder `blocked`, prüfe mit dem Nutzer, ob eine alternative Abhängigkeit oder eine Roadmap-Anpassung erforderlich ist.

## J. Weiterentwicklung der Roadmap

Die ursprüngliche Roadmap darf sich nach Rücksprache mit dem Nutzer weiterentwickeln. Neue Tasks, Umplanungen, angepasste Abhängigkeiten, erweiterte Phasen sowie `cancelled` oder `superseded` markierte Tasks sind zulässig. Lösche keine abgeschlossenen Tasks, schreibe die Historie nicht um, vergib IDs nicht neu und erhöhe Fortschritt nicht künstlich.

Erhöhe `roadmapVersion`, wenn sich der geplante Umfang wesentlich ändert, etwa durch eine neue Phase, mehrere neue Tasks, ein wesentlich geändertes MVP oder ersetzte Tasks. Reine Lifecycle-Übergänge erfordern nicht zwingend eine neue Roadmap-Version. Protokolliere wesentliche Roadmap-Änderungen in `activity.jsonl`.

## K. Dashboard-Synchronisierung

Unterscheide nach einem Task-Abschluss zwischen lokalem Projektstand und dem vom Dashboard gelesenen, synchronisierten Repository-Stand. Behaupte nie, das Dashboard zeige einen neuen Fortschritt, solange die Änderungen nicht auf dem konfigurierten Branch beziehungsweise im tatsächlich gelesenen Git-Stand angekommen sind.

Wenn der Workshop-Workflow einen Push durch den Teilnehmer vorsieht, weise passend darauf hin:

> Die Aufgabe ist lokal abgeschlossen. Synchronisiere die Änderungen mit dem Repository, damit der neue Fortschritt im Dashboard sichtbar wird.

Verlange keinen automatischen Push, wenn Tool oder Workshop-Repository anders konfiguriert sind.

## Konsistenzregeln

- `project.json` und `roadmap.json` sind die maschinenlesbaren fachlichen Quellen.
- `roadmap.json` ist die einzige fachliche Quelle für Tasks und deren Status.
- `progress.json` ist nur ein aus `roadmap.json` berechneter Cache/Aggregatzustand für Dashboard, schnelle Anzeige und Agentenorientierung. Bei Widerspruch gewinnt `roadmap.json`; berechne `progress.json` daraus neu und führe keinen unabhängigen Fortschritt.
- `CURRENT_STATE.md` ist die menschenlesbare Zusammenfassung und darf den JSON-Dateien nicht widersprechen.
- `CURRENT_STATE.md` wird nach jedem wesentlichen Statuswechsel aktualisiert.
- `activity.jsonl` enthält sachliche Ereigniszusammenfassungen, keine vollständigen Chat-Prompts, Chain-of-Thought oder internen Denkprozesse.
- Das Format richtet sich nach `.workshop/DASHBOARD_CONTRACT.md` und den Schemas.

