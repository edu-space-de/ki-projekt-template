# FEATURE.md — 10-ki-attraktion-erstellen

> **Hinweis:** Optional — LB3-Aufgabe **D2**, aus der Musterlösung `C_ML_inkl_D`.
> `index.html`/`app.js` (Modal `modalAddAttraction`, Button-Handler) sind in `stufen/C`
> bereits fertig verdrahtet; in `assets/js/n8n.js` steht noch der Platzhalter
> `d2_attraction_platzhalter` (Marker **D2**) — der ist Teil dieser Aufgabe.

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
`addAttractionDescription`) ruft bereits `sendDataToN8N('attraction')` auf — das ist
**vorgegeben**. Offen ist der `n8n.js`-Marker **D2**: der Platzhalter-Eintrag
`d2_attraction_platzhalter` in `typeConfig`/`switch` muss in `'attraction'` umbenannt
und mit den echten `ids` (`addAttractionName`/`addAttractionDescription`) sowie dem
Payload `{ name, description }` befüllt werden — erst danach lösen die Buttons echte
Requests aus. Der n8n-Workflow hinter `add-attraction`
gibt Name + Beschreibung an ein LLM (Groq, `llama-3.1-8b-instant` über einen
LangChain-„Basic LLM Chain"-Node), das angewiesen wird, Name/Beschreibung unverändert zu
übernehmen und Adresse, Latitude/Longitude, Telefon, E-Mail, URL zu recherchieren/
generieren. Ein Code-Node parst die LLM-Textantwort zu JSON; ein Data-Table-Node schreibt
das Ergebnis als neue Zeile in `Attractions` (dieselbe Tabelle wie `08-datenhaltung`).

**Backend-Übung (n8n-seitig):** Vorlage `Vorlage-PWA-AddAttraction` (Webhook + LLM-Chain
+ Code-Node vorhanden, Data-Table-Node **leer**) duplizieren + die Data-Table-Verbindung
auf `Attractions` setzen.

**Bonus-Erweiterung (laut LB3-Folie 110):** Aktuell übernimmt das LLM Name/Beschreibung
unverändert und generiert nur die restlichen Felder. Als Erweiterung darf das LLM auch
die **Kurzbeschreibung selbst ergänzen/ausformulieren**, statt nur die knappe
Nutzereingabe zu übernehmen — dazu die „Data Preservation"-Vorgabe im Prompt für
`description` lockern.

---

## Abnahmekriterien

- [ ] `n8n.js`-Marker D2 aufgelöst: `typeConfig`/`switch`-Eintrag heißt `'attraction'`
      (nicht mehr `d2_attraction_platzhalter`), `ids` korrekt, Payload `{ name, description }`
- [ ] Vorlage-Workflow `Vorlage-PWA-AddAttraction` dupliziert, Data-Table-Node auf
      `Attractions` verdrahtet, aktiviert
- [ ] Formular mit nur Name + Kurzbeschreibung abschicken → neue Zeile erscheint in
      `Attractions` mit allen Feldern befüllt (auch den nicht manuell eingegebenen)
- [ ] Name/Beschreibung in der neuen Zeile entsprechen exakt der Eingabe (LLM darf diese
      beiden Felder nicht verändern)
- [ ] Neue Attraktion erscheint nach Neuladen in der normalen Liste (`get-attractions`)
- [ ] Fehlerfall (LLM liefert kein valides JSON): kein Absturz, Code-Node gibt
      `{error, raw}` zurück statt zu werfen
- [ ] *(Bonus)* LLM ergänzt/verbessert auch die Kurzbeschreibung, statt sie nur
      unverändert zu übernehmen

---

## Abgrenzung — was dieses Feature nicht leistet

- Keine Qualitätsgarantie für die KI-generierten Daten (können ungenau/veraltet sein —
  Groq-Modell ohne Websuche, reines Sprachmodell-Wissen)
- Kein Bearbeiten/Löschen bestehender Attraktionen (nur Anlegen)

---

## Offene Fragen

- [ ] Soll es eine Vorschau/Freigabe-Schritt geben, bevor die KI-generierten Daten
      gespeichert werden, oder direktes Speichern (wie aktuell)?
- [ ] Soll das Groq-Modell/Prompt für den Kurs fix vorgegeben sein, oder dürfen Teams es
      anpassen?
