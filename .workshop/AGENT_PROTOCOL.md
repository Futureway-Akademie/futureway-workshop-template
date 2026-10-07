# Verbindliches Agentenprotokoll

Diese Datei ist die autoritative Prozessquelle für jeden Coding-Agenten. Tool-spezifische Adapter dürfen diesen Ablauf weder duplizieren noch verändern. Maschinenlesbarer Projektzustand wird nur unter `.workshop/` gepflegt.

# Guided Interaction Contract

Bestimmte Workshop-Zustände besitzen nachfolgend exakt definierte Nutzerfragen. Jeder Coding-Agent muss diese Formulierungen unverändert verwenden. Bei diesen Fragen darf er:

- keinen zusätzlichen Einführungstext schreiben
- keine Beispiele ergänzen
- keine Bulletpoints ergänzen
- keine Empfehlungen ergänzen
- keine technische Analyse ausgeben
- keine nächste Frage vorwegnehmen
- die Formulierung weder ausschmücken noch erklären oder umformulieren

Die definierte Frage bildet grundsätzlich die gesamte sichtbare Antwort. Wenn ein Zustand ausdrücklich eine Zusammenfassung, Planung oder Task-Liste vor der Frage verlangt, ist ausschließlich dieser dort genannte Inhalt zusätzlich zulässig; die definierte Frage steht dann unverändert als letzte Zeile. Wo ausdrücklich festgelegt ist, dass die Frage die gesamte sichtbare Antwort bildet, darf davor und danach nichts ausgegeben werden.

Interne Datei-Prüfung und Analyse darf vor der sichtbaren Antwort stattfinden.

## A. Beim ersten Öffnen

Lies in dieser Reihenfolge:

1. `.workshop/config.json`
2. `.workshop/project.json`
3. `.workshop/roadmap.json`
4. `.workshop/progress.json`
5. `.workshop/CURRENT_STATE.md`
6. alle Dateien unter `.workshop/specialization/`
7. die für die aktuelle Arbeit relevante Dokumentation

Wenn `project.status` den Wert `not_initialized` hat und noch keine Projektidee vom Nutzer vorliegt, darf keine Anwendung programmiert, technisch eingerichtet oder selbstständig erdacht werden. Nach der stillen Prüfung der genannten Dateien lautet die gesamte sichtbare Antwort EXAKT:

Was möchtest du entwickeln?

Keine weitere Zeile ist zulässig.

Ist das Projekt bereits initialisiert, melde dem Nutzer nach dem Lesen kurz das erkannte Projekt, die aktuelle Phase, die aktuelle Aufgabe, die zuletzt abgeschlossene Aufgabe und den empfohlenen nächsten Schritt zurück. Halte diese Bestätigung knapp; sie zeigt, dass der zentrale Zustand korrekt übernommen wurde.

## B. Nach der Projektidee

Analysiere die Idee, aber schreibe noch keinen Anwendungscode und ändere noch keine Projektzustandsdatei. Erarbeite einen verständlichen Vorschlag für:

- Projektbrief
- MVP
- technische Struktur
- Phasen und Tasks
- Abhängigkeiten
- projektweite und taskbezogene Definition of Done
- Verifikation
- Gewichte

Leite dafür insbesondere Projektname, Problem oder Idee, Zielgruppe, Zielplattform, Kernfunktionen, Nicht-Ziele und technische Rahmenbedingungen ab. Zerlege das Vorhaben in sinnvolle Entwicklungsphasen und jede Phase in überschaubare, prüfbare Tasks. Teile pauschale Großaufgaben wie „Backend bauen“ weiter auf.

Zeige dem Nutzer diese Planung verständlich, ohne sie bereits im Repository zu aktivieren. Die letzte Zeile der Antwort lautet EXAKT:

Soll ich diese Projektstruktur so vorbereiten?

Danach warte auf die Antwort des Nutzers. Vor seiner Zustimmung dürfen weder die Projektstruktur gespeichert noch eine Implementierung begonnen werden.

## C. Nach Bestätigung der Projektstruktur

Wenn der Nutzer zustimmt, aktualisiere gemeinsam und konsistent:

- `.workshop/PROJECT_BRIEF.md`
- `.workshop/project.json`
- `.workshop/roadmap.json`
- `.workshop/progress.json`
- `.workshop/CURRENT_STATE.md`
- `.workshop/activity.jsonl`
- relevante Dokumentation

Setze `project.status` und `roadmap.status` auf `active`, `roadmapVersion` auf mindestens `1` und erfasse die Initialisierung sachlich im Activity Log. Berechne anschließend die Abhängigkeiten und setze jeden startbaren Task auf `ready`.

Zeige die startbaren Tasks kompakt an. Leite diese Auswahl ausschließlich aus `roadmap.json` ab. Zeige `planned` Tasks mit offenen Abhängigkeiten sowie `blocked`, `completed`, `cancelled` und `superseded` Tasks nicht als direkt startbar an. Die letzte Zeile der Antwort lautet EXAKT:

Womit möchtest du starten?

Beginne noch keine Implementierung.

## D. Nach der Task-Auswahl

Setze den ausgewählten Task noch nicht auf `completed` und beginne noch nicht mit der Implementierung. Du darfst den ausgewählten Task kurz nennen. Danach lautet die Frage EXAKT:

Wie möchtest du diese Teilaufgabe umsetzen?

Füge keine Beispiele oder vorweggenommenen Vorschläge an, sofern der Nutzer nicht ausdrücklich darum bittet. Warte danach auf seine Umsetzungsbeschreibung.

## E. Während der Implementierung

Erst nachdem der Nutzer beschrieben hat, wie die ausgewählte Teilaufgabe umgesetzt werden soll, setze den Task bei tatsächlichem Arbeitsbeginn auf `in_progress`, pflege `startedAt` und aktualisiere den zentralen Zustand. Verwende ISO-8601-Zeitstempel in UTC.

Bearbeite nur die ausgewählte Aufgabe. Kleine, notwendige Abhängigkeiten dürfen ergänzt werden. Ziehe keine großen zukünftigen Phasen ungefragt vor. Beachte bestehende Projektregeln und halte wesentliche neue Entscheidungen im Repository fest.

Prüfe die Definition of Done, berücksichtige das Quality Gate und führe relevante Tests und Checks aus.

Standardmäßig darf im gesamten Projekt höchstens ein Task `in_progress` sein. Eine Ausnahme ist nur zulässig, wenn die Roadmap oder eine Spezialisierungsdatei parallele Arbeit ausdrücklich erlaubt. Starte niemals eigenständig mehrere große Tasks gleichzeitig.

## F. Nach Abschluss oder Blockierung

Bevor ein Task den Status `completed` erhält:

1. Stelle sicher, dass die zugehörige Implementierung tatsächlich im Repository vorhanden ist.
2. Prüfe alle Punkte der `definitionOfDone`.
3. Berücksichtige das Quality Gate.
4. Führe relevante Tests und Checks aus.
5. Dokumentiere das Ergebnis im `verification`-Objekt des Tasks.

Melde nicht ausgeführte Tests niemals als erfolgreich. Aktualisiere nach einem erfolgreichen Abschluss konsistent:

- `.workshop/roadmap.json`
- `.workshop/progress.json`
- `.workshop/activity.jsonl`
- `.workshop/CURRENT_STATE.md`
- relevante Dokumentation

Setze `completedAt` erst beim belegten Abschluss. Ein Task darf niemals allein durch eine Änderung an `roadmap.json` auf `completed` gesetzt werden. Umgekehrt müssen nach einer erfolgreich umgesetzten und geprüften Aufgabe mindestens `roadmap.json`, `progress.json`, `CURRENT_STATE.md` und `activity.jsonl` sowie bei Bedarf relevante Dokumentation aktualisiert werden.

Berechne den Fortschritt aus der Roadmap nach dem Dashboard-Vertrag; schätze ihn nicht. Fasse danach kurz und sachlich zusammen, was tatsächlich umgesetzt und geprüft wurde, und zeige die aktuell möglichen nächsten Tasks. Die letzte Zeile lautet EXAKT:

Womit möchtest du weitermachen?

Beginne keinen dieser Tasks automatisch.

Kann die Aufgabe nicht abgeschlossen werden, setze sie auf `blocked` und dokumentiere den Grund sachlich. Beginne keine andere größere Aufgabe automatisch. Zeige mögliche `ready` Tasks und beende die Antwort mit der exakt gleichen Frage:

Womit möchtest du weitermachen?

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

