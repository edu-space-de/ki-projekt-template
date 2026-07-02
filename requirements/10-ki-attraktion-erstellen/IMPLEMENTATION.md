# IMPLEMENTATION.md — Feature 10: Attraktion per KI erstellen (D2)

> **Für den KI-Agenten:** Schritt für Schritt abarbeiten, `[x]` abhaken, am Ende
> `BACKLOG.md` aktualisieren. `index.html`/`app.js` sind bereits fertig verdrahtet;
> `n8n.js` braucht noch Task 1 (Marker D2 auflösen), der Rest passiert im n8n-Editor.

**Ziel:** Aus Name + Kurzbeschreibung eine vollständige Attraktion per KI generieren
und in `Attractions` speichern.
**Abhängigkeit:** 02-bewertung-senden abgeschlossen; 08-datenhaltung empfohlen
**Verantwortlich:** [Name]
**Branch:** `feature/10-ki-attraktion-erstellen`

---

## Technische Übersicht

**Ort:** n8n-Workspace (Vorlage `Vorlage-PWA-AddAttraction`), **nicht** `assets/js/`.
**Ablauf (Mermaid):** Konvention → [`docs/diagramme.md`](../../docs/diagramme.md) Abschnitt 5.

```mermaid
sequenceDiagram
    actor Betreiber
    participant PWA as N8nService (PWA, fertig)
    participant WH as Webhook add-attraction
    participant LLM as Groq LLM Chain
    participant Code as Code-Node (JSON-Parse)
    participant DT as Data Table Attractions
    Betreiber->>PWA: sendDataToN8N('attraction') {name, description}
    PWA->>WH: POST /webhook/add-attraction
    WH->>LLM: Prompt (name, description fix; Rest generieren)
    LLM-->>Code: Text-Antwort (JSON-String)
    Code->>Code: JSON.parse(aiText)
    Code->>DT: Insert row (Title, Description, Address, Lat/Long, ...)
    DT-->>WH: OK
    WH-->>PWA: Response
```

---

## Task 1: `n8n.js`-Marker D2 auflösen

- [ ] In `typeConfig`: Eintrag `'d2_attraction_platzhalter'` in `'attraction'` umbenennen,
      `ids` auf `['addAttractionName', 'addAttractionDescription']` setzen
- [ ] Im `switch`: `case 'd2_attraction_platzhalter':` in `case 'attraction':` umbenennen,
      Payload auf `{ name: val1.trim(), description: val2.trim() }` setzen
- [ ] **Prüfen (Browser, DevTools Network):** Modal „Attraktion hinzufügen" abschicken
      löst einen Request an `add-attraction` aus (noch ohne Backend-Verdrahtung ggf. Fehler
      — das ist erwartet, folgt in Task 2/3)

## Task 2: Vorlage duplizieren

- [ ] `Vorlage-PWA-AddAttraction` im n8n-Workspace duplizieren (eigene Kopie)
- [ ] **Prüfen:** Webhook, „Basic LLM Chain" (Groq-Modell `llama-3.1-8b-instant`),
      Code-Node „JSON umwandeln" sind vorhanden und bereits verbunden; nur der
      abschließende Data-Table-Node ist leer/nicht verbunden

## Task 3: Data-Table-Node verdrahten

- [ ] Im Code-Node → Data-Table-Node (`operation: insert`) verbinden, `dataTableId`
      auf die bestehende Tabelle `Attractions` setzen
- [ ] Spalten-Mapping: `Title: {{ $json.name }}`, `Description: {{ $json.description }}`,
      `Address/Latitude/Longitude/Telephone/Email/URL: {{ $json.<Feld> }}` (Werte aus dem
      geparsten LLM-JSON)
- [ ] Data-Table-Node → Respond to Webhook verbinden, Workflow aktivieren

## Task 4: End-to-End testen

- [ ] Über die PWA im Modal „Attraktion hinzufügen" Name + Kurzbeschreibung eingeben,
      abschicken
- [ ] **Prüfen (n8n-UI):** neue Zeile in `Attractions` mit allen Feldern befüllt
- [ ] **Prüfen:** Title/Description entsprechen exakt der Eingabe (nicht vom LLM verändert)
- [ ] **Prüfen (Browser):** Attraktion erscheint nach Neuladen der Liste (`getAttractions()`)

## Task 5: Fehlerfall prüfen

- [ ] Testen, was passiert, wenn das LLM keine valide JSON-Antwort liefert (z. B. sehr
      kurze/unklare Beschreibung eingeben)
- [ ] **Prüfen:** Code-Node gibt `{error, raw}` zurück, Workflow bricht nicht ab, PWA
      zeigt eine Fehlermeldung statt sich aufzuhängen
- [ ] **Commit:** `git commit -m "feat(ki-attraktion): Vorlage dupliziert + Data Table verdrahtet (D2)"`

---

## Abschluss

- [ ] Abnahmekriterien aus `FEATURE.md` geprüft
- [ ] `BACKLOG.md`: `10-ki-attraktion-erstellen` → `✅ fertig`
- [ ] Pull Request anlegen (`git push origin feature/10-ki-attraktion-erstellen`)
