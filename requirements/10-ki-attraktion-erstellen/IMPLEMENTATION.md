# IMPLEMENTATION.md — Feature 10: Attraktion per KI erstellen (D2)

> **Für den KI-Agenten:** Schritt für Schritt abarbeiten, `[x]` abhaken, am Ende
> `BACKLOG.md` aktualisieren. **Diese Schritte passieren im n8n-Editor** — die PWA-Seite
> (`index.html`, `n8n.js`) ist bereits fertig und wird nicht verändert.

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

## Task 1: Vorlage duplizieren

- [ ] `Vorlage-PWA-AddAttraction` im n8n-Workspace duplizieren (eigene Kopie)
- [ ] **Prüfen:** Webhook, „Basic LLM Chain" (Groq-Modell `llama-3.1-8b-instant`),
      Code-Node „JSON umwandeln" sind vorhanden und bereits verbunden; nur der
      abschließende Data-Table-Node ist leer/nicht verbunden

## Task 2: Data-Table-Node verdrahten

- [ ] Im Code-Node → Data-Table-Node (`operation: insert`) verbinden, `dataTableId`
      auf die bestehende Tabelle `Attractions` setzen
- [ ] Spalten-Mapping: `Title: {{ $json.name }}`, `Description: {{ $json.description }}`,
      `Address/Latitude/Longitude/Telephone/Email/URL: {{ $json.<Feld> }}` (Werte aus dem
      geparsten LLM-JSON)
- [ ] Data-Table-Node → Respond to Webhook verbinden, Workflow aktivieren

## Task 3: End-to-End testen

- [ ] Über die PWA im Modal „Attraktion hinzufügen" Name + Kurzbeschreibung eingeben,
      abschicken
- [ ] **Prüfen (n8n-UI):** neue Zeile in `Attractions` mit allen Feldern befüllt
- [ ] **Prüfen:** Title/Description entsprechen exakt der Eingabe (nicht vom LLM verändert)
- [ ] **Prüfen (Browser):** Attraktion erscheint nach Neuladen der Liste (`getAttractions()`)

## Task 4: Fehlerfall prüfen

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
