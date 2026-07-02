# FEATURE.md — 07-feedback-mail

> **Hinweis:** Optional — LB3-Aufgaben **D1** (E-Mail) + **D4** (Push), aus der
> Musterlösung `C_ML_inkl_D`. Datei: `assets/js/app.js` (Button-Handler) +
> `assets/js/n8n.js` (`sendDataToN8N`, `showServiceWorkerNotification`).

**Priorität:** Optional (Erweiterung)
**Status:** offen
**Abhängigkeit:** 02-bewertung-senden abgeschlossen (teilt Formular + Dispatcher)

---

## Ziel

Zusätzlich zum Speichern in der Data Table (`02-bewertung-senden`) löst eine Bewertung
eine E-Mail-Benachrichtigung an den Betreiber aus (D1) und zeigt der/dem Nutzenden eine
Push-Bestätigung auf dem Gerät (D4).

---

## Beschreibung

`sendDataToN8N(type)` unterstützt bereits den Typ `'email'` (Payload: `name`, `comment`,
`timestamp`, `source`, `userAgent`) neben `'comment'`. Der Button-Handler zu
`btnSendComment` in `app.js` ruft aktuell nur `sendDataToN8N('comment')` auf; der
`'email'`-Aufruf steht bereits daneben, aber **auskommentiert**. Backend: n8n-Workflow
hinter `app-review-email` (Endpunkt aus `CONFIG.endpoints.emailNotification`) formatiert
die Daten und versendet eine HTML-E-Mail. Nach erfolgreichem `'email'`-Versand ruft
`sendDataToN8N` automatisch `showServiceWorkerNotification()` auf (D4) — eine
Service-Worker-Push-Bestätigung auf dem Gerät der/des Nutzenden.

**Backend-Übung (n8n-seitig):** Wie bei `08-datenhaltung` existiert eine unverdrahtete
Vorlage (`Vorlage-PWA-SendFeedbackMail`) im n8n-Workspace — duplizieren + verdrahten
(Webhook → Set/Edit-Fields → Send-Email-Node), statt neu zu bauen.

---

## Abnahmekriterien

- [ ] `btnSendComment`-Handler ruft zusätzlich zu `sendDataToN8N('comment')` auch
      `sendDataToN8N('email')` auf (auskommentierte Zeile aktivieren/ergänzen)
- [ ] Vorlage-Workflow `Vorlage-PWA-SendFeedbackMail` dupliziert + verdrahtet + aktiviert
- [ ] Nach Formular-Absenden trifft eine E-Mail beim Betreiber ein (Name, Kommentar,
      Zeitpunkt lesbar formatiert)
- [ ] Nach erfolgreichem E-Mail-Versand erscheint eine Push-Benachrichtigung auf dem Gerät
      (sofern Berechtigung erteilt)
- [ ] Ohne Notification-Berechtigung: kein Absturz, nur Konsolen-Warnung

---

## Abgrenzung — was dieses Feature nicht leistet

- Keine Push-Benachrichtigung unabhängig vom E-Mail-Versand (im Code fest an
  `type === 'email'` gekoppelt)
- Kein Double-Opt-in/Unsubscribe für die Benachrichtigungen
- Keine eigene `stufen/`-Übungsstufe mit `//------`-Markern für D1/D4 — die PWA-Seite
  ist in der Musterlösung bereits fertig, die Übung liegt im n8n-seitigen Verdrahten
  (s. `_meta/task.md`, offener Punkt „stufen/D fehlt")

---

## Offene Fragen

- [ ] Soll `'email'` den Data-Table-Save (`'comment'`) ersetzen oder wie in der
      Musterlösung zusätzlich dazu laufen?
- [ ] Soll es eine eigene `stufen/D`-Stufe mit Markern für Studierende geben, oder bleibt
      dieses Feature ausschließlich eine n8n-seitige Übung mit vorgegebenem PWA-Code?
