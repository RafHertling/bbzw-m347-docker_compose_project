# Statusbericht – Woche 3 (DL11)

Name: Hertling Rafael
Klasse: IN2023c

---

## Umgesetzte Arbeiten

- Hardcodierte Werte in `compose.yml` und `app/main.py` analysiert und identifiziert
- `.env`-Datei mit allen Konfigurationswerten erstellt (DB-Name, User, Passwort, Host, Port)
- `.gitignore` erstellt, sodass `.env` nicht ins Git-Repository gelangt
- `.env.example` geprüft und verifiziert, dass alle Schlüssel vorhanden aber keine echten Passwörter enthalten sind
- `compose.yml` auf `${VARIABLE}`-Syntax umgestellt (DATABASE_URL, POSTGRES_DB, POSTGRES_USER, POSTGRES_PASSWORD)
- `app/main.py` angepasst: `DATABASE_URL` wird jetzt via `os.getenv("DATABASE_URL")` aus der Laufzeitumgebung gelesen
- System mit `docker compose up --build` gestartet und alle Endpunkte getestet
- Dokumentation (`questions.md`, `statusbericht-woche3.md`) ausgefüllt

---

## Aktueller Stand

- Das System startet mit einem einzigen Befehl (`docker compose up --build`)
- Alle Endpunkte funktionieren korrekt:
  - `/health` → `{"status": "ok"}`
  - `/db-check` → `{"db": "connected"}`
  - Adminer unter Port 8080 erreichbar
  - Frontend unter Port 3000 erreichbar
- Kein einziges Passwort steht mehr im Code oder in versionierten Dateien

---

## Offene Probleme / Herausforderungen

- Keine offenen Probleme. Die Umstellung auf Umgebungsvariablen verlief reibungslos.

---

## Nächste Schritte

- Secrets-Management in Produktivumgebungen kennenlernen (z. B. Docker Secrets, Kubernetes Secrets, HashiCorp Vault)
- Weitere Endpunkte für das TicketBoard implementieren (Tickets erstellen, lesen, aktualisieren, löschen)

---

## Selbsteinschätzung (optional)

Der Auftrag hat gut aufgezeigt, warum sauberes Konfigurationsmanagement wichtig ist. Das Muster `.env` + `.gitignore` + `.env.example` ist einfach zu verstehen und sofort anwendbar. Die Aufgabe war gut strukturiert und die Zusammenhänge wurden durch das schrittweise Vorgehen klar.
