# FEATURE.md — 10-ki-attraktion-erstellen

> **Hinweis:** Optional — LB3-Aufgabe **D2**, aus der Musterlösung `C_ML_inkl_D`.
> Datei: `assets/js/n8n.js` (`sendDataToN8N('attraction')`, bereits implementiert),
> `index.html` (Modal `modalAddAttraction`).

**Priorität:** Optional (Erweiterung)
**Status:** offen
**Abhängigkeit:** 02-bewertung-senden abgeschlossen (teilt `sendDataToN8N`-Dispatcher),
08-datenhaltung empfohlen (schreibt in dieselbe `Attractions`-Table)

---

## Ziel

Neue Attraktionen lassen sich mit minimalem manuellem Aufwand anlegen: Name +
Kurzbeschreibung genügen, ein KI-Modell recherchiert/generiert die restlichen Felder.

---

## Beschreibung

Button `btnAddAttraction` (Modal „Attraktion hinzufügen", Felder `addAttractionName`,
`addAttractionDescription`) ruft `sendDataToN8N('attraction')` auf — **bereits fertig
implementiert**, keine PWA-Codeänderung nötig. Der n8n-Workflow hinter `add-attraction`
gibt Name + Beschreibung an ein LLM (Groq, `llama-3.1-8b-instant` über einen
LangChain-„Basic LLM Chain"-Node), das angewiesen wird, Name/Beschreibung unverändert zu
übernehmen und Adresse, Latitude/Longitude, Telefon, E-Mail, URL zu recherchieren/
generieren. Ein Code-Node parst die LLM-Textantwort zu JSON; ein Data-Table-Node schreibt
das Ergebnis als neue Zeile in `Attractions` (dieselbe Tabelle wie `08-datenhaltung`).

**Backend-Übung (n8n-seitig):** Vorlage `Vorlage-PWA-AddAttraction` (Webhook + LLM-Chain
+ Code-Node vorhanden, Data-Table-Node **leer**) duplizieren + die Data-Table-Verbindung
auf `Attractions` setzen.

---

## Abnahmekriterien

- [ ] Vorlage-Workflow `Vorlage-PWA-AddAttraction` dupliziert, Data-Table-Node auf
      `Attractions` verdrahtet, aktiviert
- [ ] Formular mit nur Name + Kurzbeschreibung abschicken → neue Zeile erscheint in
      `Attractions` mit allen Feldern befüllt (auch den nicht manuell eingegebenen)
- [ ] Name/Beschreibung in der neuen Zeile entsprechen exakt der Eingabe (LLM darf diese
      beiden Felder nicht verändern)
- [ ] Neue Attraktion erscheint nach Neuladen in der normalen Liste (`get-attractions`)
- [ ] Fehlerfall (LLM liefert kein valides JSON): kein Absturz, Code-Node gibt
      `{error, raw}` zurück statt zu werfen

---

## Abgrenzung — was dieses Feature nicht leistet

- Keine Qualitätsgarantie für die KI-generierten Daten (können ungenau/veraltet sein —
  Groq-Modell ohne Websuche, reines Sprachmodell-Wissen)
- Kein Bearbeiten/Löschen bestehender Attraktionen (nur Anlegen)
- Keine eigene `stufen/`-Übungsstufe mit `//------`-Markern — PWA-Seite ist fertig
  vorgegeben, Übung liegt im n8n-Backend

---

## Offene Fragen

- [ ] Soll es eine Vorschau/Freigabe-Schritt geben, bevor die KI-generierten Daten
      gespeichert werden, oder direktes Speichern (wie aktuell)?
- [ ] Soll das Groq-Modell/Prompt für den Kurs fix vorgegeben sein, oder dürfen Teams es
      anpassen?
