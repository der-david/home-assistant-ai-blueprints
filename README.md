# Home Assistant AI Blueprints

Sammlung von Script-Blueprints, die als **Tools für ein LLM** (Assist / Sprachassistent) gedacht sind.

## Verwendung

1. Blueprint-Datei aus `blueprints/script/` nach `<config>/blueprints/script/` kopieren (oder per URL importieren).
2. **Einstellungen > Automatisierungen & Szenen > Scripte > Script erstellen > Blueprint verwenden**.
3. Das Script unter **Einstellungen > Sprachassistenten > Entitäten freigeben** für Assist freigeben.
4. Im Konversations-Agenten muss die Steuerung durch Assist aktiviert sein (LLM-API „Assist").

Die `fields` des Scripts sind die Parameter, die das LLM sieht. Ihre `description` und die Script-`description` sind die Anweisungen an das LLM. Die Texte sind bewusst englisch, weil das erfahrungsgemäß am zuverlässigsten funktioniert. Das Script kann im UI umbeschrieben werden.

## Blueprints

| Blueprint | Beschreibung |
| --- | --- |
| [`calendar_create_event.yaml`](blueprints/script/calendar_create_event.yaml) | Legt einen Termin (mit Uhrzeit oder ganztägig) in einem Kalender an (`calendar.create_event`). |
| [`calendar_search_events.yaml`](blueprints/script/calendar_search_events.yaml) | Sucht Termine per Stichworten über einen großen Zeitraum (`calendar.get_events`), gibt nur die besten Treffer zurück. |

### Kalendertermin erstellen

Eingaben des Blueprints:

- **Kalender**: Ziel-Kalender (muss das Anlegen von Terminen unterstützen).
- **Standarddauer**: Dauer in Minuten, wenn das LLM kein Ende angibt (Standard 60).

Felder für das LLM: `summary` (Pflicht), `start_date_time` / `end_date_time` (Termin mit Uhrzeit) oder `start_date` / `end_date` (ganztägig, Ende exklusiv), `description`, `location`.

Das Script liefert dem LLM eine Antwort (`success: true/false`, bei Fehlern mit `error`), sodass es dem Nutzer Rückmeldung geben kann.

### Kalendertermine suchen

Das LLM übergibt mehrere Suchbegriffe (`search_terms`: Synonyme, Wortteile, Schreibweisen) und optional einen Zeitraum (`start_date`, `end_date`). Das Script holt die Termine per `calendar.get_events` und filtert sie, bevor etwas an das LLM zurückgeht:

- Teilstring-Vergleich ohne Groß-/Kleinschreibung und Umlaute (`Müller` = `mueller`) auf Titel, Beschreibung und Ort.
- Ein Termin ist ein Treffer, wenn **ein** Begriff passt. Mehr passende Begriffe ranken höher, Titeltreffer zählen doppelt, danach kommen anstehende vor vergangenen Terminen.
- Wiederkehrende Termine (gleicher Titel, gleiche Uhrzeit, gleicher Ort) werden optional zu einem Eintrag mit `further_occurrences` und `last_occurrence` zusammengefasst.
- Die Antwort enthält den tatsächlich durchsuchten Zeitraum, `total_matches` und bei Bedarf einen Hinweis (`note`), z. B. wenn nichts gefunden wurde oder Treffer abgeschnitten wurden. So kann das LLM mit anderen Begriffen erneut suchen.

Eingaben des Blueprints:

- **Kalender**: ein oder mehrere Kalender.
- **Standard-Suchzeitraum**: Tage zurück (Standard 30) und voraus (Standard 365), wenn das LLM keinen Zeitraum angibt.
- **Maximale Trefferzahl** (Standard 10) und **maximale Länge der Beschreibung** (Standard 120 Zeichen, 0 = weglassen) begrenzen die Payload.
- **Wiederkehrende Termine zusammenfassen** (Standard an).
