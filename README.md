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

### Kalendertermin erstellen

Eingaben des Blueprints:

- **Kalender**: Ziel-Kalender (muss das Anlegen von Terminen unterstützen).
- **Standarddauer**: Dauer in Minuten, wenn das LLM kein Ende angibt (Standard 60).

Felder für das LLM: `summary` (Pflicht), `start_date_time` / `end_date_time` (Termin mit Uhrzeit) oder `start_date` / `end_date` (ganztägig, Ende exklusiv), `description`, `location`.

Das Script liefert dem LLM eine Antwort (`success: true/false`, bei Fehlern mit `error`), sodass es dem Nutzer Rückmeldung geben kann.
