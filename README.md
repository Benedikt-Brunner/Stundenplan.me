# Stundenplan.me

Stundenplan.me ist eine moderne Webanwendung zur Verwaltung und zum Teilen von Stundenplänen. Sie bietet eine benutzerfreundliche Oberfläche, mit der Nutzer ihre Stundenpläne erstellen, anpassen und mit Freunden vergleichen können.

## Features
- Stundenplan-Editor
- Freundesverwaltung und Vergleich von Stundenplänen
- Anpassbare Darstellung (Themes, Sprachen, Vorlagen)
- Persistente Speicherung und Synchronisation

---

## Technische Anforderungen

### Node.js
- Für das Frontend wird Node.js (empfohlen: v18+) benötigt.
- Installiere die Abhängigkeiten mit:
  ```bash
  npm install
  ```

### Rust & Cargo
- Das Backend ist in Rust geschrieben und benötigt Cargo (empfohlen: stable).
- Stelle sicher, dass Rust und Cargo installiert sind: https://rustup.rs
- Backend-Abhängigkeiten werden automatisch über Cargo verwaltet.

### Migrationen
- Migrationen befinden sich im Ordner `StundenplanBackEnd/Migrations/`.
- Beim Starten des Backends werden alle Migrationen automatisch ausgeführt.
- Neue Migrationen können als `.sql`-Dateien hinzugefügt werden (Namensschema beachten).

---

## Lokaler Entwicklungsablauf

### Frontend starten
```bash
npm run dev
```

### Backend starten
```bash
cd StundenplanBackEnd
cargo run
```

### Komplettes Build & Deployment (lokal)
```bash
npm run build
```

---

## Beitrag leisten (Contributing)
1. Forke das Repository und erstelle einen Branch für deine Änderungen.
2. Installiere alle Abhängigkeiten (`npm install` und Rust/Cargo).
3. Starte die lokale Datenbank und führe Migrationen aus.
4. Teste deine Änderungen lokal.
5. Erstelle einen Pull Request mit einer Beschreibung deiner Änderungen.

---

## Verzeichnisstruktur (Auszug)
- `src/` – Svelte Frontend
- `StundenplanBackEnd/` – Rust Backend
- `StundenplanBackEnd/Migrations/` – SQL-Migrationen
- `templates/` – HTML-Templates für das Backend

---

## Hinweise
- Für die Entwicklung werden Linux und Bash empfohlen, andere Plattformen sind möglich.
- Bei Problemen mit der lokalen Datenbank bitte sicherstellen, dass PostgreSQL läuft und erreichbar ist.
- Bei Fragen oder Problemen bitte ein Issue im Repository eröffnen.
