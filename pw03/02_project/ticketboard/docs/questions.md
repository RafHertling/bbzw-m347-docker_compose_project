# Fragen – Konfiguration und Umgebungsvariablen (DL11)

Name: Hertling Rafael
Klasse: IN2023c

---

## 1. Konfiguration

Welche Werte sind aktuell hardcoded in `compose.yml` und `app/main.py`?

Antwort:
In `compose.yml` waren folgende Werte hardcoded:
- `DATABASE_URL`: `postgresql://ticketuser:secret@db:5432/ticketdb`
- `POSTGRES_DB`: `ticketdb`
- `POSTGRES_USER`: `ticketuser`
- `POSTGRES_PASSWORD`: `secret`

In `app/main.py` war ebenfalls `DATABASE_URL` direkt als String eingetragen:
`"postgresql://ticketuser:secret@db:5432/ticketdb"`

---

Warum ist es ein Problem, Passwörter direkt in `compose.yml` einzutragen?

Antwort:
Wenn `compose.yml` ins Git-Repository eingecheckt wird, sind die Passwörter für alle sichtbar, die Zugriff auf das Repository haben. Git speichert jeden Commit dauerhaft – selbst wenn das Passwort später entfernt wird, bleibt es in der Git-History und kann mit `git log` oder `git show` eingesehen werden. Bei öffentlichen Repositories sind die Zugangsdaten sogar weltweit lesbar.

---

Was ist der Unterschied zwischen `.env` und `.env.example`?

Antwort:
- `.env` enthält die **echten Werte** (z. B. das echte Passwort) und wird **nicht** ins Git eingecheckt (steht in `.gitignore`).
- `.env.example` enthält nur die **Schlüsselnamen** mit Platzhalterwerten (z. B. `POSTGRES_PASSWORD=changeme`) und wird ins Git eingecheckt. Sie zeigt anderen Entwicklern, welche Variablen benötigt werden, ohne echte Secrets preiszugeben.

---

Warum muss `.env` in `.gitignore` eingetragen sein?

Antwort:
Damit die Datei mit den echten Zugangsdaten niemals versehentlich ins Git-Repository hochgeladen wird. Git ignoriert alle Dateien, die in `.gitignore` aufgeführt sind – sie werden weder getrackt noch committed. So bleiben Passwörter und Secrets lokal und gelangen nie in die Versionsverwaltung.

---

## 2. Variablen in Compose

Wie referenziert man eine Variable aus `.env` in `compose.yml`?

Antwort:
Mit der Syntax `${VARIABLE_NAME}`. Docker Compose liest `.env` automatisch beim Start und ersetzt alle `${...}`-Platzhalter im YAML durch die entsprechenden Werte. Beispiel: `POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}`

---

Was passiert, wenn eine Variable in `.env` fehlt, aber in `compose.yml` verwendet wird?

Antwort:
Docker Compose ersetzt den Platzhalter durch einen leeren String. Das kann zu Fehlern beim Start führen – z. B. schlägt die Datenbankverbindung fehl, wenn `POSTGRES_PASSWORD` leer ist. Mit `docker compose config` kann man prüfen, ob alle Variablen korrekt aufgelöst wurden.

---

Was zeigt der Befehl `docker compose config`? Wann ist er nützlich?

Antwort:
`docker compose config` gibt die vollständig aufgelöste Compose-Konfiguration aus – also das YAML mit allen bereits eingesetzten Variablenwerten. Er ist nützlich zur Fehlersuche, um zu prüfen ob alle `${VARIABLE}`-Platzhalter korrekt durch die Werte aus `.env` ersetzt wurden, bevor man die Container startet.

---

## 3. Dockerfile und Build

Warum wird `requirements.txt` in einem eigenen `COPY`-Schritt vor dem App-Code kopiert?

Antwort:
Docker baut Images schichtweise (Layer-Caching). Wenn `requirements.txt` in einem separaten Schritt vor dem App-Code kopiert wird, muss Docker die Abhängigkeiten nur dann neu installieren, wenn sich `requirements.txt` geändert hat. Ändert sich nur der App-Code, wird die zwischengespeicherte Layer mit den installierten Paketen wiederverwendet – der Build ist dadurch deutlich schneller.

---

Was bewirkt `.dockerignore`? Welche Dateien sollten darin stehen?

Antwort:
`.dockerignore` verhindert, dass bestimmte Dateien und Ordner in den Docker Build-Kontext übertragen werden. Das beschleunigt den Build und verhindert, dass sensible Dateien ins Image gelangen. Typische Einträge:
- `.env` (Secrets nicht ins Image)
- `__pycache__/`, `*.pyc` (Python-Cache)
- `.git/` (Git-History nicht nötig im Image)
- `.venv/` (lokale virtuelle Umgebung)

---

## 4. Systemtest

Funktioniert `/db-check` nach Ihrer Konfigurationsanpassung?

Antwort:
Ja. Nach dem Umstellen auf Umgebungsvariablen und dem Start mit `docker compose up --build` antwortet der Endpunkt `/db-check` mit `{"db": "connected"}`.

---

Was zeigt der Endpunkt `/db-check` an, wenn die Verbindung funktioniert?

Antwort:
Er gibt `{"db": "connected"}` zurück. Falls die Verbindung fehlschlägt, gibt er `{"db": "error", "detail": "<Fehlermeldung>"}` zurück.

---

## 5. Reflexion

Was war der wichtigste Schritt in dieser Woche?

Antwort:
Der wichtigste Schritt war das Verstehen, warum Secrets niemals direkt im Code oder in versionierten Dateien stehen dürfen. Die Kombination aus `.env` (lokal, nicht im Git) und `.env.example` (im Git, ohne echte Werte) ist ein einfaches aber wirkungsvolles Muster, das in der Praxis überall eingesetzt wird.

---

Was ist noch unklar oder möchten Sie besser verstehen?

Antwort:
Es wäre interessant zu verstehen, wie man in Produktivumgebungen (z. B. in Kubernetes oder CI/CD-Pipelines) Secrets noch sicherer verwaltet – z. B. mit Tools wie HashiCorp Vault oder Docker Secrets.
