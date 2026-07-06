# PWA testen & deployen — drei Wege

Eine PWA braucht für Serviceworker, Kamera und Geolocation einen **Secure Context**
(HTTPS). Es gibt drei Wege, eure App zu testen — vom schnellsten zum öffentlichsten.

## Weg 1: Lokal testen mit `localhost` (Standard während der Entwicklung)

`http://localhost` gilt im Browser als Secure Context — **alle** PWA-Funktionen
(Serviceworker, Cache, Kamera, GPS, Notifications) funktionieren ohne Upload.

Im Projektordner starten (eine der Varianten):

```bash
python3 -m http.server 8000     # Python (vorinstalliert auf macOS/Linux)
npx serve .                     # Node.js
```

Dann `http://localhost:8000` öffnen. Alternativ in VS Code die Extension
**Live Server** installieren und unten rechts „Go Live" klicken.

> Serviceworker-Stolperstein: Der SW cached eure App. Nach Codeänderungen in den
> DevTools (F12) → *Application → Service Workers → Update* klicken oder den
> `CACHE_NAME` in `serviceworker.js` hochzählen (`v01` → `v02`).

Grenze von Weg 1: Test auf dem **eigenen Smartphone** (Add2Homescreen, echte
Kamera/GPS) geht so nicht — dafür Weg 2 oder 3.

## Weg 2: Auf euren FTP-Slot deployen — automatisch bei jedem Speichern

Jede Gruppe hat einen Server-Slot mit eigener HTTPS-URL (z. B.
`https://pwa11.prj.edu-space.de`) und FTP-Zugangsdaten (kommen von der
Kursleitung — **niemals ins Repo committen!**).

Statt manuell mit FileZilla hochzuladen, lasst VS Code das bei jedem Speichern tun:

1. Extension **SFTP** (von *Natizyskunk*) installieren.
2. `Cmd/Ctrl+Shift+P` → **SFTP: Config** → es entsteht `.vscode/sftp.json`.
3. So ausfüllen (Werte durch die eures Slots ersetzen):

```json
{
    "name": "PWA-Slot",
    "protocol": "ftp",
    "host": "pwaXX.prj.edu-space.de",
    "port": 21,
    "username": "edu_space_ftpXX",
    "remotePath": "/prj/pwaXX",
    "uploadOnSave": true,
    "ignore": [".vscode", ".git", ".idea", "docs"]
}
```

4. Beim ersten Upload fragt VS Code nach dem Passwort. Das Passwort **nicht** in
   die `sftp.json` schreiben — die Datei ist zwar in `.gitignore`, aber Passwörter
   gehören grundsätzlich nie in Dateien im Projekt (siehe `SECURITY.md`).
5. Speichern → Datei liegt auf dem Server → `https://pwaXX.prj.edu-space.de`
   im Handy-Browser öffnen und testen.

Wichtig für die PWA: `PATH_ROOT` in `serviceworker.js` muss zum Slot-Pfad passen
(z. B. `/prj/pwaXX` bzw. `/obw/pwaXX`), sonst cached der Serviceworker ins Leere.

So können auch **andere Gruppen** euren Prototyp unter der URL testen — verschiedene
Geräte, verschiedene Browser, echtes Feedback.

## Weg 3: GitHub Pages (öffentlicher Showcase)

Liegt euer Repo (public) auf GitHub, gibt es kostenloses HTTPS-Hosting per Klick:

1. Repo → **Settings → Pages**
2. Source: *Deploy from a branch* → Branch `main`, Ordner `/ (root)` → **Save**
3. Nach ~1 Minute: `https://<username>.github.io/<repo>/`

Jeder `git push` deployt automatisch neu. Auch hier: `PATH_ROOT` im Serviceworker
auf `/<repo>` setzen. Achtung: Das Repo und damit euer Code sind öffentlich —
für Abgaben gilt weiterhin der Weg über euer Kurs-Repo (PR-Abgabe).

## Übersicht

| Weg | Wofür | HTTPS-URL | Aufwand |
|---|---|---|---|
| localhost | tägliche Entwicklung | – (localhost = Secure Context) | keiner |
| FTP-Slot + VS Code | Handy-Test, Gruppen-Feedback | `pwaXX.prj.edu-space.de` | einmalig 5 Min |
| GitHub Pages | öffentlicher Showcase | `<user>.github.io/<repo>` | einmalig 2 Min |
