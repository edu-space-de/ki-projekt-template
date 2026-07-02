# FEATURE.md — 04-karte

> **Hinweis:** Konkretes LB3-Feature (Stufe **C**). LB3-Aufgaben: **C4, C5** *(optional)*.
> Datei: `assets/js/map.js`.

**Priorität:** Erweiterung
**Status:** offen
**Abhängigkeit:** 03-standort (aktuelle Position) + 01-attraktionen-laden (gewählte Attraktion)

---

## Ziel

Nutzende sehen ihre Position und die gewählte Attraktion auf einer Google-Karte mit passendem
Ausschnitt — die visuelle Ergänzung zur Entfernungsanzeige aus `03-standort`.

---

## Beschreibung

`MapService.showMap()` liest `locationID` (aus `toDetailPage`) und `currentLat`/`currentLng`
(aus `03-standort`) aus dem `localStorage`. Fehlt die Position, bricht es mit Hinweis ab. Sonst
erstellt es eine `google.maps.Map` in `#mapOutput`, setzt Marker für aktuelle Position und
Attraktion und grenzt den Ausschnitt via `LatLngBounds`/`fitBounds` ein. Optional (C5) ermittelt
ein `Geocoder` die postalische Adresse der aktuellen Position und zeigt sie in `#adressOutput`.

---

## Kartenanbieter — zwei Varianten (eine genügt)

| Variante | Anbieter | API-Key? | Empfehlung |
|---|---|---|---|
| **A** | Google Maps | **ja** (`DEIN_GOOGLE_MAPS_API_KEY` in `index.html`, nie committen) | wenn Google-Features/Geocoder gewünscht |
| **B** | **Leaflet + OpenStreetMap** | **nein (keyfrei)** | **empfohlen zum Start** — nichts zu beantragen, kein Secret-Risiko |

Beide erfüllen dieselben Abnahmekriterien. Details/Code: `IMPLEMENTATION.md`. Architektur-Begründung: `ARCHITECTURE.md` → ADR-04.

## Abnahmekriterien

- [ ] **C4** — Karte in `#mapOutput` mit zwei Markern (Position + Attraktion), Ausschnitt umfasst beide (`fitBounds`) — **mit Variante A oder B**
- [ ] Ohne bestimmte Position: `alert('Bitte vorher Position bestimmen!')` und Abbruch
- [ ] **C5** *(optional)* — aktuelle Adresse in `#adressOutput` (Google Geocoder bzw. keyfrei via Nominatim)
- [ ] Gewählte Variante lädt fehlerfrei (Google-Key eingebunden **oder** Leaflet/OSM keyfrei)

---

## Abgrenzung — was dieses Feature nicht leistet

- Keine Turn-by-Turn-Navigation / Routenberechnung
- Keine Offline-Karten (Google Maps benötigt Netz)
- Keine Kartenanbieter-Alternative (fix Google Maps in LB3)

---

## Offene Fragen

- [ ] (Variante A) Wie wird der Google-API-Key sicher/pro Umgebung hinterlegt (nicht committen)?
- [x] Kartenanbieter-Wechsel geklärt: **Leaflet/OSM als keyfreie Variante B** ergänzt (2026-07-01); Google Maps bleibt Variante A.
