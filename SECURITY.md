# SECURITY — Umgang mit Geheimnissen (verbindliche Vorgabe)

Diese Datei ist Teil des Projekts und **Prüfungsbestandteil**. Sicherer Umgang mit
Zugangsdaten ist kein Bonus, sondern Grundhandwerk — im Studium wie im Beruf. Ein einziger
öffentlich gewordener API-Key kann echtes Geld kosten, Konten sperren oder Daten offenlegen.

> **Grundregel:** Geheimnisse gehören **nie ins Repository, nie in KI-Tools, nie in
> Screenshots/Tickets/Chats.** Im Zweifel: wie ein Geheimnis behandeln.

> **Zusammenhang:** Diese Datei ist die **Sicherheits-Baseline**. Das Abnahme-Audit
> [`AUDIT.md`](AUDIT.md) prüft ihre Einhaltung (Dimension 2.3 Sicherheit) zusammen mit
> Zuverlässigkeit, Wartbarkeit und Performance — vor jeder Demo/LB3-Abgabe.

---

## Was zählt als Geheimnis?

API-Keys, Access-Tokens (PAT, OAuth), Passwörter, Datenbank-Zugangsdaten, SMTP-Credentials,
private SSH-/GPG-Schlüssel, Session-/Encryption-Keys, S3-/Cloud-Keys, vollständige
Verbindungsstrings mit Zugangsdaten. **Auch von uns bereitgestellte Schlüssel** (z. B. für
LLM-APIs) fallen darunter.

## Wo Geheimnisse sich verstecken (aus der Praxis — nicht nur `.env`!)

Ein sauberes `.env` allein reicht nicht. Secrets tauchen an vielen Stellen auf:

- **PHP:** `define('DB_PASSWORD', '…')`, `wp-config.php`; **serialisierte Werte** in
  Datenbankfeldern (z. B. `wp_options`: `s:8:"password";s:12:"geheim1234";`) — die rutschen an
  naiven „Wegstreich"-Prüfungen vorbei.
- **JavaScript:** `const apiKey = "…"`; besonders tückisch: Werte, die in **gebaute Bundles**
  (`dist/`, minifiziert) oder ins **Frontend** gelangen — im Browser für jeden lesbar.
- **Config & Daten:** `config.json`, `*.yml`, `docker-compose.yml`, jede `.env*`,
  **DB-Dumps** (`*.sql`), **SQLite-Dateien** (`*.db`), Backups.
- **Logs & Ausgaben:** Fehler-Logs, `console.log`, Terminal-Ausgaben, `curl -v`.
- **Git-Historie:** ein *früher* committetes Secret bleibt in der History, auch wenn die
  aktuelle Datei sauber ist.

## Regeln

1. **`.env` + `.gitignore`.** Alle Secrets ausschließlich in `.env` (steht in `.gitignore`).
   Committe stattdessen **`.env.example`** mit **Platzhaltern** — so weiß das Team, welche
   Variablen nötig sind, ohne echte Werte zu teilen.
2. **Von uns erhaltene Keys** → in `.env`, **nie** hardcoden, **nie** committen, nie in einen PR.
3. **Kein Secret ins Frontend/JS** — alles, was im Browser landet, ist lesbar. Secrets nur im
   Backend/über einen Proxy verwenden.
4. **KI-Tools (Claude Code, Cursor, Copilot, ChatGPT, Codex …):** niemals echte Secrets in
   Prompts oder in gezeigtem Code/Dateien. **Vorher maskieren** (Platzhalter `<API_KEY>`).
   Besonders: **keine ganzen Config-Dateien, `.env`, DB-Dumps oder Logs roh einfügen** — dort
   stecken Secrets, teils versteckt in serialisierten oder binären Feldern. Die KI braucht den
   echten Wert nie.
5. **Beim Hilfeholen/Debuggen maskieren.** Falle aus der Praxis: eine Suche wie
   `grep -A3 password config.php` zeigt den Wert **direkt mit an** — der Kontext um das Feld
   *ist* das Secret. Also Log-/Such-Ausschnitte vor dem Teilen prüfen und Werte ersetzen.
6. **Vor jedem Commit scannen.** `git diff` bewusst ansehen (keine `.env`, keine Keys).
   Werkzeug: **gitleaks**, **git-secrets** oder **trufflehog** finden Keys automatisch —
   idealerweise als Pre-Commit-Hook.
7. **Leak passiert? Sofort rotieren.** Neuen Schlüssel erzeugen, alten beim Aussteller
   **widerrufen**. **Nur aus Git löschen reicht NICHT** — der Wert bleibt in Klonen, Forks,
   Mirrors und Caches. Ein einmal exponierter Key ist verbrannt.
8. **Sichtbarkeit.** Repo privat halten; erst veröffentlichen, wenn sicher **nie** ein Secret
   drin war — Historie inklusive.

## Kurz-Rezept „Ich muss etwas teilen (Hilfe/Review)"

1. Wert durch Platzhalter ersetzen: `sk-live-abc123…` → `<OPENAI_API_KEY>`.
2. Binäres/serialisiertes/DB-Material **gar nicht roh** teilen — die Struktur versteckt Secrets,
   die ein schnelles Überfliegen übersieht.
3. Unsicher, ob es ein Secret ist? → wie eins behandeln.

## Für KI-Agenten in diesem Projekt

Diese Datei gehört zum Projektkontext. Agenten dürfen Secrets **weder anfordern noch ausgeben**
noch in Commits/Vorschläge schreiben; bei erkannten Secrets aktiv warnen und auf `.env`
verweisen.

---

*Diese Vorgabe spiegelt gelebte Betriebspraxis: Secrets werden serverseitig erzeugt, nie in
Klartext übertragen, bei Verdacht sofort rotiert. Genau das übt ihr hier ein.*
