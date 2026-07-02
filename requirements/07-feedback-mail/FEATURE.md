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

`sendDataToN8N(type)` unterstützt bereits den Typ `'comment'`; der Platzhalter
`d1_email_platzhalter` in `typeConfig`/`switch` markiert die Stelle, an der ein Eintrag
für `'email'` (Payload: `name`, `comment`, `timestamp`, `source`, `userAgent`) ergänzt
werden muss (Marker **D1**). Der Button-Handler zu `btnSendComment` in `app.js` ruft
aktuell nur `sendDataToN8N('comment')` auf; der `'email'`-Aufruf steht bereits daneben,
aber **auskommentiert**. Backend: n8n-Workflow hinter `app-review-email` (Endpunkt aus
`CONFIG.endpoints.emailNotification`, bereits in `CONFIG` hinterlegt) formatiert die
Daten und versendet eine HTML-E-Mail. Nach erfolgreichem `'email'`-Versand soll
`sendDataToN8N` `showServiceWorkerNotification()` aufrufen (Marker **D4**, aktuell ein
`console.log`-Stub) — eine Service-Worker-Push-Bestätigung auf dem Gerät der/des
Nutzenden.

**Backend-Übung (n8n-seitig):** Wie bei `08-datenhaltung` existiert eine unverdrahtete
Vorlage (`Vorlage-PWA-SendFeedbackMail`) im n8n-Workspace — duplizieren + verdrahten
(Webhook → Set/Edit-Fields → Send-Email-Node), statt neu zu bauen.

**Alternativer Ansatz (laut LB3-Folie 109):** Statt eines eigenständigen Workflows kann
auch der bestehende `add-comment`-Workflow (aus `02-bewertung-senden`) erweitert werden,
sodass er die Mail direkt versendet, sobald die neue Zeile in `Comments` angelegt wurde —
dann ist kein zweiter Request (`sendDataToN8N('email')`) von der PWA-Seite nötig.

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

---

## Offene Fragen

- [ ] Soll `'email'` den Data-Table-Save (`'comment'`) ersetzen oder wie in der
      Musterlösung zusätzlich dazu laufen?
- [ ] Alternativer Ansatz (Mail direkt bei Data-Table-Insert, s. o.) statt eigenem
      Workflow umsetzen — oder beide Varianten als Wahlmöglichkeit dokumentieren?
