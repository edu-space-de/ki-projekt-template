# FEATURE.md — 11-ki-chat-assistent

> **Hinweis:** Optional — LB3-Aufgabe **D3**, aus der Musterlösung `C_ML_inkl_D`.
> `index.html`/`app.js` (Modal `modalChat`, „Frag den AI-ttraktionsbot", Button-Handler)
> sind in `stufen/C` bereits fertig verdrahtet; in `assets/js/n8n.js` steht noch der
> Platzhalter `d3_chat_platzhalter` (Marker **D3**) — der ist Teil dieser Aufgabe.

**Priorität:** Optional (Erweiterung)
**Status:** offen
**Abhängigkeit:** 01-attraktionen-laden abgeschlossen (Attraktionsdaten müssen existieren)

---

## Ziel

Ein einfacher RAG-Chatbot (Retrieval-Augmented Generation) beantwortet freie Fragen zu
den vorhandenen Attraktionen — als Demonstration, dass sich ein Chat-Assistent auch in
einem kleinen Projekt sinnvoll einsetzen lässt, nicht nur in großen Systemen.

---

## Beschreibung

Button `btnChat` (Modal „Frag den AI-ttraktionsbot", Feld `chatInput`, Ausgabe
`chatOutput`) ruft bereits `sendDataToN8N('chat')` auf — das ist **vorgegeben**. Offen ist
der `n8n.js`-Marker **D3**: der Platzhalter-Eintrag `d3_chat_platzhalter` in
`typeConfig`/`switch` muss in `'chat'` umbenannt und mit `ids: ['chatInput']` sowie
Payload `{ userInput }` befüllt werden; zusätzlich braucht der Erfolgsfall eine
Sonderbehandlung (Antwort in `#chatOutput` anzeigen statt Eingabefelder zu überschreiben —
ebenfalls unter Marker D3 vermerkt). Der n8n-Workflow hinter `chat-attraction` liest
**alle** Zeilen aus `Attractions` (Retrieval), fasst sie über einen Aggregate-Node zu
einem einzigen JSON-Kontext zusammen und übergibt sie zusammen mit der Nutzerfrage an ein
LLM (Groq, `llama-3.1-8b-instant`, Prompt „Urlaubs-Assistent"), das die passende
Attraktion sucht und in Fließtext antwortet (Generation). Die Antwort kommt als
`{ answer: "..." }` zurück.

**Backend-Übung (n8n-seitig):** Anders als bei `08`/`07`/`10` gab es (Stand 2026-07-02)
zunächst **keine** unverdrahtete `Vorlage-PWA-ChatAttraction` — nur die fertige
Referenz. Inzwischen angelegt (s. `IMPLEMENTATION.md` Task 2): Webhook + Data-Table
„Get row(s)" + Aggregate + „Basic LLM Chain" vorhanden und verbunden, **Prompt-Text der
LLM-Chain-Node ist geleert** (Platzhalter) — das ist die eigentliche Übungsaufgabe.

---

## Abnahmekriterien

- [ ] `n8n.js`-Marker D3 aufgelöst: `typeConfig`/`switch`-Eintrag heißt `'chat'` (nicht
      mehr `d3_chat_platzhalter`), `ids: ['chatInput']`, Payload `{ userInput }`;
      Erfolgsfall zeigt `result.answer` in `#chatOutput` statt Eingabefelder zu überschreiben
- [ ] Vorlage-Workflow `Vorlage-PWA-ChatAttraction` dupliziert, Prompt-Text der LLM-Chain
      ergänzt, aktiviert
- [ ] Chat-Modal öffnen, Frage eingeben (z. B. „Wo kann ich schwimmen?") → Antwort
      erscheint in `chatOutput`
- [ ] Antwort bezieht sich nachweislich auf eine reale Zeile aus `Attractions`
      (Name/Beschreibung im Antworttext wiedererkennbar)
- [ ] Frage ohne passende Attraktion → Assistent sagt das ehrlich, erfindet keine
      Attraktion
- [ ] Mehrfache Nachfragen im selben Chat-Fenster funktionieren (Inputfeld wird nach
      jeder Antwort geleert)

---

## Abgrenzung — was dieses Feature nicht leistet

- Kein Gedächtnis über mehrere Fragen hinweg (jede Anfrage lädt alle Attraktionen neu,
  kein Chat-Verlauf als Kontext)
- Keine Vektorsuche/Embeddings — bei wenigen Attraktionen reicht „alle laden und dem LLM
  übergeben"; bei sehr vielen Zeilen wäre eine echte RAG-Pipeline (Embeddings + Vektor-DB)
  nötig

---

## Offene Fragen

- [ ] Ab welcher Anzahl Attraktionen wird „alle laden" für den LLM-Kontext zu groß/teuer,
      und wäre dann eine echte Vektorsuche ein sinnvoller Anschluss-Punkt für Fortgeschrittene?
