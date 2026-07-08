# AUDIT — PWA-Abnahme-Audit (verbindliche Vorgabe)

Diese Datei ist Teil des Projekts und **Prüfungsbestandteil**. Ein Feature „läuft bei mir"
ist kein Nachweis. Nachweis ist ein **aktuelles, nachvollziehbares Audit**, das belegt, dass
eure PWA **zuverlässig, updatefähig/wartbar, sicher und performant** ist — genau die vier
Eigenschaften, die auch im Berufsalltag über „produktiv gehen oder nicht" entscheiden.

> **Grundsatz:** Vor der LB3-Abgabe (bzw. jeder Demo/Übergabe) muss zu eurer PWA ein
> **bestandenes bzw. dokumentiert bewertetes Audit** vorliegen. Kein „fertig" ohne Audit.
> Offene Punkte sind erlaubt — aber **begründet** und im Register geführt, nicht verschwiegen.

Diese Datei ergänzt und verweist auf [`SECURITY.md`](SECURITY.md) (Secret-Handhabung, ebenfalls
Prüfungsbestandteil). `SECURITY.md` ist die **Sicherheits-Baseline**; dieses Audit erzwingt ihre
Anwendung und prüft sie zusammen mit Betriebsreife, Wartbarkeit und Performance.

> **Herkunft:** Die Systematik (vier Dimensionen, Ampel, Runbook-Schema, Report-Tabelle,
> Register) stammt aus dem realen Betriebs-Audit-Prozess von CONTIC (WordPress-, Odoo-,
> Nextcloud-Audits). Ihr lernt hier also kein Schul-Konstrukt, sondern ein Verfahren, das
> auf echten Produktivsystemen angewendet wird.

---

## 1. Auslöser — wann ist ein Audit Pflicht

| Auslöser | Frist | Umfang | Ergebnis-Artefakt |
|---|---|---|---|
| **LB3-Abgabe / Bewertung** | **vor** Abgabe | Voll-Audit | Audit-Report im Repo + Register-Eintrag |
| **Demo / Präsentation** | vor der Demo | Voll- oder Delta-Audit | Report (Delta zum letzten Voll-Audit) |
| **Feature-Merge in `main`** | nach dem Merge | Delta-Audit der betroffenen Dimension | Register-Aktualisierung |
| **Secret-Verdacht** (Key im Code/Bundle/Git gesehen) | **sofort** | Sicherheits-Voll-Check + Rotation | Report + `SECURITY.md`-Verfahren |
| **Deploy auf den Kursserver / Übergabe** | vor Produktivnahme | Voll-Audit | Report als Übergabe-Deliverable |

**Voll-Audit** = alle vier Dimensionen (Abschnitt 2). **Delta-Audit** = betroffene Dimension(en)
+ Regressionscheck der zuletzt offenen 🔴/🟡-Punkte.

---

## 2. Vier Prüfdimensionen (verbindliche Mindestabdeckung)

Jedes Audit deckt **alle vier** Dimensionen ab. Bewertung je Prüfpunkt per Ampel:
**🟢 konform · 🟡 Abweichung mit Restrisiko/geplant · 🔴 Handlungsbedarf.**

### 2.1 Zuverlässigkeit / Betriebsreife
App-Shell lädt fehlerfrei (keine `Error`/`Uncaught` in der DevTools-Konsole), **Service Worker
registriert und `activated`**, **Offline-Fallback** funktioniert (DevTools → *Network* → *Offline*
→ Reload lädt aus dem Cache), Statusanzeige online/offline schaltet korrekt, **Manifest valide**
(installierbar, „Zum Homescreen"), robuste **Fehlerbehandlung** bei fehlgeschlagenen n8n-Requests
(kein weißer Bildschirm / harter Absturz, verständliche Meldung), **Ressourcenfreigabe**
(Kamera-Tracks nach Foto gestoppt, QR-Scan-Loop beim Seitenwechsel beendet — kein Kamera-Indikator
im Hintergrund), Daten überleben Reload wo erwartet (localStorage/n8n).

### 2.2 Updatefähigkeit / Wartbarkeit
**`CACHE_NAME` versioniert** und `activate` löscht alte Caches (sonst sehen Nutzer nach einem Deploy
alte Assets), Konfiguration **zentral** (n8n-Endpoints/Keys in *einer* Config-Stelle, nicht über
Dateien verstreut), **kein Duplikat-Code** (Wiederholungen benannt/zusammengeführt), Abhängigkeiten
**gepinnt** (jsQR, Bulma & Co. mit fester Version, nicht „latest"), **keine toten Marker/Platzhalter**
mehr im Code (`ToDo`-Stubs, `*_platzhalter`-Zweige ersetzt), Doku aktuell
(`BACKLOG.md`-Status stimmt mit dem Code überein), Git-Historie sauber und nachvollziehbar
(sprechende Commits nach Schema).

### 2.3 Sicherheit  → Baseline: [`SECURITY.md`](SECURITY.md)
**Keine Secrets im Frontend/Bundle** — Google-Maps-Key, n8n-Base-URL/Webhook-Token, NocoDB-Token
sind im Browser für jeden lesbar; sensible Schlüssel gehören **hinter n8n** (Credential-Store), nicht
in die PWA. `.env` in `.gitignore`, `.env.example` nur mit Platzhaltern, **keine Secrets in der
Git-Historie** (mit `gitleaks`/`git-secrets` prüfen). **Input-Validierung/Escaping** bei allen
Formularen (Kommentar/Bewertung/„Attraktion hinzufügen") → kein ungefiltertes HTML ins DOM (XSS).
**Datentrennung** bei geteilter NocoDB-Base (Team A sieht nur Team-A-Daten). **HTTPS-Pflicht**
(Geolocation/Kamera/Service Worker laufen nur über sichere Herkunft). **Least Privilege** bei
n8n/NocoDB-Zugriff (nur nötige Rechte). **Keine echten personenbezogenen Testdaten**; localStorage
enthält nichts Sensibles. **LLM-Chat:** kein Erfinden von Fakten, keine Prompt-Injection über
Nutzereingaben, keine Weitergabe interner Prompts/Keys.

### 2.4 Performance
**Lighthouse** ausgeführt (DevTools → *Lighthouse*, Kategorien PWA/Performance/Best Practices/
Accessibility) — Ergebnis dokumentiert, grobe Ausreißer adressiert. Cache-Strategie sinnvoll
(Kern-Assets vorab, nicht „alles"), **Bilder komprimiert/passend dimensioniert**, Kamera/QR-Loop
CPU-schonend (`requestAnimationFrame`, sauber gestoppt), **keine unnötigen/gedoppelten n8n-Requests**
pro Interaktion, sichtbar keine 4xx/5xx unter Normalbedienung im *Network*-Tab.

---

## 3. Durchführung (Runbook-Schema)

**Ziel → Scope → Voraussetzungen → Read-only-Erhebung → Ist/Soll-Bewertung → Empfehlung (wartbar) →
Umsetzung → Verifikation → Rollback → Dokumentation.**

- **Erst read-only.** Zuerst *nur erheben und bewerten* (DevTools, Lighthouse, Repo lesen, `curl`/
  Netzwerk-Tab). Änderungen gesammelt in einem Empfehlungsblock, Umsetzung erst danach — nicht
  „nebenbei" beim Prüfen mitpatchen.
- **Scope-Kennzeichnung** durchgängig, damit jede Aussage überprüfbar ist:
  `[Browser]` (DevTools/Lighthouse) · `[Code]` (Repo/Datei) · `[n8n]` (Workflow/Backend) ·
  `[NocoDB]` (Daten) · `[Git]` (Historie/Secrets).
- **Sanitize-Pflicht:** jede Ausgabe, die ein Secret enthalten könnte (Config, `.env`, Logs,
  `curl -v`), **vor** dem Einfügen in Report/Chat maskieren — siehe `SECURITY.md`.
- **Wartbarkeit vor Quick-Fix:** eine Empfehlung, die beim nächsten Deploy/Umbau wieder kaputtgeht,
  ist keine Lösung. Zentral konfigurieren statt an mehreren Stellen hart kodieren.
- **Rollback-Folgen** jeder Empfehlung mitdenken (Branch/Commit-Stand, DB-/NocoDB-Rückweg).

---

## 4. Report-Format (Standard)

**Dateiname:** `audit-<team-oder-projekt>-YYYY-MM-DD.md`, abgelegt im Repo (z. B. `docs/audit/`).

**Pflichtinhalt:**
1. **Kopf:** Projekt/Team, geprüfter Stand (Commit-Hash/Branch), Deploy-URL falls vorhanden,
   Prüfmodus (Voll-/Delta-Audit), Scope-Legende.
2. **Transparenz:** etwaige Vorfälle während der Prüfung (z. B. versehentlich ein Key im Chat gelandet).
3. **Report-Tabelle** je Prüfpunkt (Spalten unten) — mit erkennbarer Zuordnung zu den vier
   Dimensionen aus Abschnitt 2.
4. **Zusatzbefunde** außerhalb der Punkte.
5. **Zusammenfassung:** Top-🔴 (sofort), Top-🟡 (geplant), Gesamtbewertung Betriebsreife.

**Report-Tabelle (Spaltenschema):**

| # | Prüfpunkt | Ebene | Ist | Soll | Ampel | Empfehlung (wartbar) |
|---|-----------|-------|-----|------|-------|----------------------|
| … | … | `[Browser]`/`[Code]`/… | beobachteter Zustand | Zielzustand | 🟢/🟡/🔴 | konkrete, wartungsfeste Maßnahme |

---

## 5. Prüfpunkt-Katalog (Startpunkt für die Report-Tabelle)

Mindestumfang. Ergänzt Punkte, die zu euren gewählten Features passen; streicht, was ihr nicht gebaut
habt (mit kurzer Begründung im Report). Die „Prüfen"-Schritte in den jeweiligen
`requirements/NN-*/IMPLEMENTATION.md` sind die feature-genaue Ergänzung dazu.

### Zuverlässigkeit / Betriebsreife
1. App lädt ohne Konsolenfehler — `[Browser]`
2. Service Worker `activated`, Kern-Assets im *Cache Storage* — `[Browser]`
3. Offline-Reload lädt aus dem Cache; Online/Offline-Status schaltet — `[Browser]`
4. `manifest.json` valide, App installierbar — `[Browser]`
5. n8n-Request schlägt fehl → verständliche Meldung statt Absturz — `[Browser]`/`[Code]`
6. Kamera-Tracks/QR-Loop nach Nutzung gestoppt (kein Hintergrund-Zugriff) — `[Browser]`

### Updatefähigkeit / Wartbarkeit
7. `CACHE_NAME` versioniert; `activate` räumt alte Caches — `[Code]`
8. n8n-Endpoints/Config zentral an einer Stelle — `[Code]`
9. Kein Duplikat-Code; Abhängigkeiten mit fester Version — `[Code]`
10. Keine `ToDo`-/Platzhalter-Zweige mehr aktiv — `[Code]`
11. `BACKLOG.md` = tatsächlicher Stand; Commits nach Schema — `[Git]`

### Sicherheit (Baseline `SECURITY.md`)
12. Kein Secret im Frontend/Bundle (Maps-Key, n8n-Token, NocoDB-Token) — `[Code]`/`[Browser]`
13. `.env` ge-ignored, `.env.example` nur Platzhalter — `[Code]`
14. Keine Secrets in der Git-Historie (`gitleaks`) — `[Git]`
15. Formular-Eingaben escaped/validiert (kein XSS im DOM) — `[Code]`/`[Browser]`
16. NocoDB-Datentrennung bei geteilter Base — `[NocoDB]`
17. HTTPS erzwungen; sichere Herkunft für Geo/Kamera/SW — `[Browser]`
18. n8n/NocoDB-Zugriff least privilege — `[n8n]`/`[NocoDB]`
19. Keine echten personenbezogenen Testdaten — `[NocoDB]`/`[Code]`
20. LLM-Chat: kein Erfinden, keine Prompt-Injection, kein Key-Leak — `[Browser]`/`[n8n]`

### Performance
21. Lighthouse ausgeführt, Ergebnis dokumentiert — `[Browser]`
22. Cache-Strategie sinnvoll; Bilder komprimiert — `[Code]`
23. Kamera/QR-Loop CPU-schonend; keine gedoppelten n8n-Requests — `[Browser]`
24. Keine 4xx/5xx unter Normalbedienung im *Network*-Tab — `[Browser]`

---

## 6. Audit-Register (im Repo fortschreiben)

| Feature/Bereich | Letztes Audit | Geprüfter Stand (Commit) | Ampel gesamt | Offene 🔴 / 🟡 | Nächstes fällig |
|---|---|---|---|---|---|
| 00-foundation (Shell/SW/Offline) | | | ⬜ | | |
| 01-attraktionen-laden | | | ⬜ | | |
| 02-bewertung-senden | | | ⬜ | | |
| … (weitere Features) | | | ⬜ | | |
| **Gesamt-PWA (LB3-Abgabe)** | | | ⬜ | | vor Abgabe |

**Legende Ampel gesamt:** 🟢 alle Dimensionen konform · 🟡 Restpunkte geplant/begründet ·
🔴 offener Handlungsbedarf · ⬜ noch nicht auditiert.

---

## 7. Kurz-Rezept „Ich mache jetzt das Abgabe-Audit"

1. Aktuellen `main`-Stand ziehen, Commit-Hash notieren, App lokal (bzw. auf Deploy-URL) öffnen.
2. **Read-only erheben:** DevTools-Konsole, *Application* (SW/Cache/Manifest), *Network* (inkl.
   Offline), *Lighthouse*; Repo auf Secrets/Config/Marker durchsehen; `gitleaks detect`.
3. Je Prüfpunkt aus Abschnitt 5 eine Zeile in die Report-Tabelle (Ist/Soll/Ampel/Empfehlung).
4. 🔴 zuerst beheben (v. a. Sicherheit), 🟡 begründen und im Register führen.
5. Report unter `docs/audit/audit-<...>-<datum>.md` committen, **Register** aktualisieren.

---

*Kein Feature gilt als „fertig", solange sein Audit-Eintrag nicht steht. Das Abgabe-Audit ist
Teil der Bewertung — es zeigt, dass ihr nicht nur gebaut, sondern auch verantwortet habt.*
