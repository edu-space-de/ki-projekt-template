# FEATURE.md — 08-datenhaltung

> **Hinweis:** Mindestanforderung — für alle Teams verbindlich. Betrifft die
> **n8n-Workflows** hinter `get-attractions`/`add-comment`, nicht `assets/js/`.
> Architektur-Begründung: `ARCHITECTURE.md` → ADR-03.

**Priorität:** Mindestanforderung
**Status:** offen
**Abhängigkeit:** 01-attraktionen-laden, 02-bewertung-senden (beide Webhooks müssen stehen)

---

## Ziel

Die n8n-Workflows hinter `get-attractions` und `add-comment` bekommen eine persistente
Ablage über **n8n Data Tables** (natives n8n-Feature), statt Attraktionen als Demodaten
im Workflow zu halten und Bewertungen gar nicht zu speichern.

---

## Beschreibung

Zwei Data Tables im n8n-Workspace:

| Tabelle | Spalten | Genutzt von |
|---|---|---|
| `Attractions` | `AttractionID`, `Title`, `Description`, `Latitude`, `Longitude`, `Address`, `Telephone`, `Email`, `URL` | `get-attractions` (liest) |
| `Comments` | `Name`, `Note`, `CreatedAt` | `add-comment` (schreibt) |

Der `get-attractions`-Workflow liest die `Attractions`-Table statt die in `n8n.js`
hinterlegten Demodaten zu spiegeln; der `add-comment`-Workflow fügt jede eingehende
Bewertung (`payload.name`/`payload.note`) als neue Zeile in `Comments` ein. Die
Webhook-Schnittstelle (Pfad, Request-/Response-Format) ändert sich für die PWA nicht.

Per MCP ansteuerbar/vorbereitbar: `create_data_table`, `add_data_table_column`,
`add_data_table_rows`, `search_data_tables`.

---

## Abnahmekriterien

- [ ] Data Table `Attractions` existiert mit den oben genannten Spalten, befüllt mit
      mindestens den bisherigen Demo-Attraktionen aus `n8n.js`
- [ ] Data Table `Comments` existiert mit den oben genannten Spalten
- [ ] `get-attractions`-Workflow liefert seine Antwort aus `Attractions` (nicht aus
      hart codierten Werten im Workflow)
- [ ] `add-comment`-Workflow schreibt jede eingehende Bewertung als neue Zeile in
      `Comments`
- [ ] Ein über die PWA abgeschickter Kommentar ist nach einem Neustart/Neu-Deploy des
      n8n-Workflows weiterhin in `Comments` vorhanden
- [ ] `assets/js/n8n.js` (`CONFIG.endpoints`, Request-/Response-Format) bleibt unverändert

---

## Abgrenzung — was dieses Feature nicht leistet

- Keine Grid-UI/Admin-Ansicht außerhalb von n8n (→ optional `09-nocodb`)
- Keine Nutzer-Authentifizierung auf den Data Tables
- Kein Löschen/Bearbeiten von Bewertungen durch Endnutzer:innen (nur Anlegen)

---

## Offene Fragen

- [ ] Sollen doppelte/leere Bewertungen serverseitig (im Workflow) zusätzlich validiert
      werden, oder reicht die clientseitige Validierung in `n8n.js`?
- [ ] Wird `Attractions` je Team einmalig befüllt, oder soll es einen Pflege-Workflow
      (Insert/Update) dafür geben?
