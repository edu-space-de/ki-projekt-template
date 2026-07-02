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

Zwei Data Tables existieren bereits zentral im edu-space-n8n-Workspace (Musterlösung):

| Tabelle | Spalten | Genutzt von |
|---|---|---|
| `Attractions` | `Title`, `Description`, `Latitude`, `Longitude`, `Address`, `Telephone`, `Email`, `URL` | `get-attractions` (liest), `add-attraction` (schreibt, s. u.) |
| `Comments` | `Name`, `Note` | `add-comment` (schreibt) |

Dazu existieren bereits **unverdrahtete Vorlage-Workflows** (`Vorlage-PWA-GetAttractions`,
`Vorlage-PWA-AddComment`) mit Webhook + leerem Data-Table-Node — das n8n-seitige Gegenstück
zu den `//------`-Markern im PWA-Code. Teams **duplizieren diese Vorlage und verdrahten
sie** (Data-Table-Node mit der bestehenden Tabelle verbinden), statt Tabelle und Workflow
neu zu bauen. Die Webhook-Schnittstelle (Pfad, Request-/Response-Format) ändert sich für
die PWA nicht.

Per MCP ansteuerbar/vorbereitbar: `search_data_tables`, `add_data_table_rows`
(zusätzliche Testzeilen), `search_workflows`/`get_workflow_details` (Vorlagen inspizieren).

---

## Abnahmekriterien

- [ ] Vorlage-Workflows für `get-attractions`/`add-comment` dupliziert (eigene Kopie pro Team)
- [ ] Beide Kopien mit den bestehenden Data Tables `Attractions`/`Comments` verdrahtet und aktiviert
- [ ] `get-attractions`-Kopie liefert ihre Antwort aus `Attractions` (nicht aus
      hart codierten Werten im Workflow)
- [ ] `add-comment`-Kopie schreibt jede eingehende Bewertung als neue Zeile in
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
- [x] Wird `Attractions` je Team einmalig befüllt, oder soll es einen Pflege-Workflow
      (Insert/Update) dafür geben? — **Geklärt:** ja, per KI-generiertem Pflege-Workflow,
      s. die optionalen D-Aufgaben (`add-attraction`, noch zu dokumentieren als eigenes
      Feature, s. `_meta/task.md`)
