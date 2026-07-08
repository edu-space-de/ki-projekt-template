# Audit-Report (Beispiel) — „Touristik Guide" PWA

> **Musterbeispiel** für ein Abnahme-Audit nach [`../../AUDIT.md`](../../AUDIT.md). Zeigt Aufbau,
> Detailtiefe und Ampel-Logik. Ersetzt eure Zeilen durch euren echten Befund. Erfundene
> Beispielwerte — kein realer Prüfstand.

## Kopf
- **Projekt/Team:** Touristik Guide — Team Musterstadt (Referenz-PWA `C_ML_inkl_D`)
- **Geprüfter Stand:** `main` @ `a1b2c3d`
- **Deploy-URL:** https://touristik-guide.example.edu-space.de
- **Prüfmodus:** Voll-Audit (alle vier Dimensionen) vor LB3-Abgabe
- **Scope-Legende:** `[Browser]` DevTools/Lighthouse · `[Code]` Repo · `[n8n]` Backend · `[NocoDB]` Daten · `[Git]` Historie

## Transparenz
Während der Prüfung keine Vorfälle. Beim `gitleaks`-Lauf ist kein echter Key im Chat gelandet.

## Report-Tabelle

| # | Prüfpunkt | Ebene | Ist | Soll | Ampel | Empfehlung (wartbar) |
|---|-----------|-------|-----|------|-------|----------------------|
| 1 | App lädt ohne Konsolenfehler | `[Browser]` | Konsole sauber, App-Shell rendert | keine Fehler | 🟢 | — |
| 2 | Service Worker `activated`, Kern-Assets im Cache | `[Browser]` | SW aktiv, `touristik-cache-v3` enthält Shell + Bulma + jsQR | aktiv | 🟢 | — |
| 3 | Offline-Reload + Online/Offline-Status | `[Browser]` | Offline-Reload lädt aus Cache; Statusanzeige schaltet | funktioniert | 🟢 | — |
| 4 | `manifest.json` valide, installierbar | `[Browser]` | „Zum Homescreen" erscheint, Icons/Name gesetzt | installierbar | 🟢 | — |
| 5 | n8n-Fehler → Meldung statt Absturz | `[Browser]`/`[Code]` | Bei 500 vom Webhook bleibt UI leer, keine Meldung | verständliche Meldung | 🟡 | `catch`-Zweig in `n8n.js` → sichtbaren Hinweis „Laden fehlgeschlagen" setzen |
| 6 | Kamera/QR-Loop gestoppt | `[Browser]` | nach Foto Tracks gestoppt; QR-Loop bei Seitenwechsel beendet | gestoppt | 🟢 | — |
| 7 | `CACHE_NAME` versioniert; `activate` räumt | `[Code]` | `...-v3`; `activate` löscht Alt-Caches | versioniert | 🟢 | — |
| 8 | n8n-Endpoints zentral | `[Code]` | alle URLs in `CONFIG` in `n8n.js` | zentral | 🟢 | — |
| 9 | Kein Duplikat-Code; Deps gepinnt | `[Code]` | jsQR `@1.4.0`, Bulma `@1.0.2` fest | gepinnt | 🟢 | — |
| 10 | Keine Platzhalter-Zweige aktiv | `[Code]` | `d1/d2/d3_platzhalter` durch `'email'/'attraction'/'chat'` ersetzt | ersetzt | 🟢 | — |
| 11 | `BACKLOG.md` = Stand; Commits nach Schema | `[Git]` | Backlog aktuell, `feat(...)`/`fix(...)` konsistent | stimmt | 🟢 | — |
| 12 | Kein Secret im Frontend/Bundle | `[Code]`/`[Browser]` | **Google-Maps-Key im Klartext in `index.html`** | kein Secret im Client | 🔴 | Key hinter n8n-Proxy **oder** als eingeschränkter HTTP-Referrer-Key (Domain-Restriktion) — nie ungebremst im Bundle |
| 13 | `.env` ge-ignored, `.env.example` Platzhalter | `[Code]` | `.env` in `.gitignore`, Example nur `<...>` | konform | 🟢 | — |
| 14 | Keine Secrets in der Git-Historie | `[Git]` | `gitleaks detect` → 0 Treffer | 0 Treffer | 🟢 | — |
| 15 | Formular-Eingaben escaped | `[Code]`/`[Browser]` | Kommentar via `textContent`, kein `innerHTML` | escaped | 🟢 | — |
| 16 | NocoDB-Datentrennung (geteilte Base) | `[NocoDB]` | n8n filtert auf Team-Feld; Team B sieht nur B | getrennt | 🟢 | — |
| 17 | HTTPS erzwungen; sichere Herkunft | `[Browser]` | HTTP→HTTPS 301; Geo/Kamera/SW laufen | erzwungen | 🟢 | — |
| 18 | n8n/NocoDB least privilege | `[n8n]`/`[NocoDB]` | NocoDB-Token nur im n8n-Credential-Store, nicht in PWA | least privilege | 🟢 | — |
| 19 | Keine echten personenbezogenen Testdaten | `[NocoDB]` | nur fiktive Beispiel-Attraktionen | fiktiv | 🟢 | — |
| 20 | LLM-Chat: kein Erfinden/Injection/Leak | `[Browser]`/`[n8n]` | antwortet nur aus Attraktionsdaten, gibt Unwissen zu | robust | 🟢 | Prompt-Härtung dokumentieren |
| 21 | Lighthouse ausgeführt | `[Browser]` | PWA ✓, Perf 94, BP 100, A11y 88 | dokumentiert | 🟢 | A11y: Kontrast in Footer nachziehen |
| 22 | Cache-Strategie/Bilder | `[Code]` | nur Kern-Assets vorab; Fotos komprimiert | sinnvoll | 🟢 | — |
| 23 | QR-Loop schonend; keine Doppel-Requests | `[Browser]` | `requestAnimationFrame`, sauber gestoppt; 1 Request je Aktion | schonend | 🟢 | — |
| 24 | Keine 4xx/5xx unter Normalbedienung | `[Browser]` | Network sauber außer #5-Fehlerfall | keine | 🟢 | s. #5 |

## Zusatzbefunde
- Matomo-Tracking-Snippet noch mit Beispiel-Site-ID — vor echtem Betrieb setzen oder entfernen.

## Zusammenfassung
- **Top-🔴 (sofort):** #12 Google-Maps-Key im Frontend → absichern (Referrer-Restriktion/Proxy).
- **Top-🟡 (geplant):** #5 Fehler-Feedback bei n8n-Ausfall; A11y-Kontrast (#21).
- **Gesamtbewertung Betriebsreife:** 🟡 — funktional abgabereif; ein Sicherheitspunkt (#12) vor „grün" beheben.
