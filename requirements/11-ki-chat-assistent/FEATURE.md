# FEATURE.md — 11-ki-chat-assistent

> **Hinweis:** Optional — LB3-Aufgabe **D3**, aus der Musterlösung `C_ML_inkl_D`.
> Datei: `assets/js/n8n.js` (`sendDataToN8N('chat')`, bereits implementiert),
> `index.html` (Modal `modalChat`, „Frag den AI-ttraktionsbot").

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
`chatOutput`) ruft `sendDataToN8N('chat')` mit Payload `{ userInput }` auf — **bereits
fertig implementiert**, keine PWA-Codeänderung nötig. Der n8n-Workflow hinter
`chat-attraction` liest **alle** Zeilen aus `Attractions` (Retrieval), fasst sie über
einen Aggregate-Node zu einem einzigen JSON-Kontext zusammen und übergibt sie zusammen
mit der Nutzerfrage an ein LLM (Groq, `llama-3.1-8b-instant`, Prompt „Urlaubs-Assistent"),
das die passende Attraktion sucht und in Fließtext antwortet (Generation). Die Antwort
kommt als `{ answer: "..." }` zurück und wird im `chatOutput`-Div dargestellt.

**Backend-Übung (n8n-seitig):** Anders als bei `08`/`10` gibt es (Stand 2026-07-02) noch
**keine** unverdrahtete `Vorlage-PWA-ChatAttraction` — der Workflow ist bislang nur in
seiner fertigen Form vorhanden. Falls eine Übungsvariante gewünscht ist, muss sie analog
zu den anderen Vorlagen zuerst erstellt werden (s. Offene Fragen).

---

## Abnahmekriterien

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
- Keine eigene `stufen/`-Übungsstufe mit `//------`-Markern — PWA-Seite ist fertig
  vorgegeben; n8n-seitige Übungsvorlage existiert noch nicht (s. Offene Fragen)

---

## Offene Fragen

- [ ] Soll eine `Vorlage-PWA-ChatAttraction` (unverdrahtet, analog zu den anderen vier
      Vorlage-Workflows) für Studierende erstellt werden, oder bleibt dieser Workflow als
      reine Demo/Referenz ohne eigene Übung?
- [ ] Ab welcher Anzahl Attraktionen wird „alle laden" für den LLM-Kontext zu groß/teuer,
      und wäre dann eine echte Vektorsuche ein sinnvoller Anschluss-Punkt für Fortgeschrittene?
