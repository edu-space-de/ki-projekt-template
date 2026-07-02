# FEATURE.md — 09-nocodb

> **Hinweis:** Bonus/optional — kein Muss, aber ausdrücklich empfohlen. Baut auf
> `08-datenhaltung` auf. Architektur-Begründung: `ARCHITECTURE.md` → ADR-03.

**Priorität:** Bonus (optional)
**Status:** offen
**Abhängigkeit:** 08-datenhaltung

---

## Ziel

NocoDB als zusätzliche bzw. alternative Datenhaltung zu den n8n Data Tables aus
`08-datenhaltung` — mit eigener Grid-UI und eigener REST-/MCP-Schnittstelle, damit sowohl
Menschen als auch KI-Agenten strukturiert auf die Projektdaten zugreifen können.

---

## Beschreibung

**Kein Multi-User-Login in NocoDB.** Stattdessen: ein einziges **Personal-API-Token**
vom Owner-Account (NocoDB-GUI → Account → API-Tokens), als n8n-Credential vom Typ
**„NocoDB API Token"** (`nocoDbApiToken`) hinterlegt — **nicht** als „NocoDB"
(Login-Credential). Die Workflows hinter `get-attractions`/`add-comment` nutzen wahlweise
statt/zusätzlich zur Data-Table-Node aus `08` eine `n8n-nodes-base.nocoDb`-Node.

Falls **mehrere Teams dieselbe NocoDB-Base** nutzen: Datentrennung **nicht** über
NocoDB-Berechtigungen, sondern über eine Tabellenspalte (z. B. `team_id`) + `where`-Filter
im Workflow (NocoDB-Formel-Syntax, z. B. `(team_id,eq,{{ $json.teamId }})`).

Per MCP ansteuerbar: `queryRecords`, `createRecords`, `updateRecords`, `aggregate`,
`getTableSchema`, `getTablesList`.

---

## Abnahmekriterien

- [ ] NocoDB-Base mit Tabellen `Attractions` und `Comments` (gleiches Schema wie in
      `08-datenhaltung`) angelegt
- [ ] Personal-API-Token vom Owner-Account erzeugt, **nicht** in Chat/Dokumenten/
      Klartext-Dateien abgelegt
- [ ] n8n-Credential vom Typ „NocoDB API Token" (nicht Login) angelegt und in den
      Workflows hinter `get-attractions`/`add-comment` genutzt
- [ ] (nur bei geteilter Base) `team_id`-Spalte + `where`-Filter verhindert
      Cross-Access zwischen Teams — mit zwei Test-`team_id`s verifiziert
- [ ] Ein Query über die NocoDB-REST-API bzw. den NocoDB-MCP-Server liefert dieselben
      Daten wie die PWA

---

## Abgrenzung — was dieses Feature nicht leistet

- Kein Account pro Studierendem/Team in NocoDB (bewusst: Free-Tier erlaubt nur
  Owner-Login über die Web-UI)
- Keine DB-seitige Zugriffskontrolle — Datentrennung ist Workflow-Logik, kein
  Safety-Net auf Datenbankebene (s. Risiko unten)

---

## Offene Fragen

- [ ] Reicht eine gemeinsame NocoDB-Base für alle Teams (mit `team_id`-Trennung), oder
      bekommt jedes Team eine eigene Base?
- [ ] Zusätzliche Absicherung nötig (z. B. Webhook + serverseitige Validierung der
      `teamId`, statt dass der Client sie frei mitgibt)?

**Risiko:** Bei fehlerhaftem `where`-Filter in einem Team-Workflow ist Cross-Access
zwischen Teams technisch möglich — es gibt keinen erzwingenden Schutz auf DB-Ebene.
