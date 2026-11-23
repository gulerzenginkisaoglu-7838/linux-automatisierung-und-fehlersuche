# 01 Fehlersuche bei Diensten

Vorgehen, das ich in jedem Fall benutzt habe: **nicht raten, Logs lesen**. Erst Status prüfen, dann die Ursache finden, dann reparieren und testen.

## Fall 1: Ein Webdienst startet nicht

**Symptom:** Der Dienst `firma-web` läuft nicht.

```bash
systemctl status firma-web
journalctl -u firma-web --no-pager
```

Die Meldung lautete: Die Datei `/opt/firma-web/web-server.py` existiert nicht.

```bash
ls -l /opt/firma-web/
cat /etc/systemd/system/firma-web.service
```

**Ursache:** In der Unit-Datei stand `web-server.py`, die Datei heißt aber `webserver.py`.

**Lösung:**

```bash
sed -i 's/web-server.py/webserver.py/' /etc/systemd/system/firma-web.service
grep ExecStart /etc/systemd/system/firma-web.service
systemctl daemon-reload
systemctl restart firma-web
systemctl status firma-web
```

Ergebnis: `active (running)`.

**Gelernt:** `ExecStart=` nennt genau die Datei, die gestartet wird. Nach jeder Änderung an einer Unit-Datei braucht es `systemctl daemon-reload`.

## Fall 2: Die Festplatte ist fast voll

```bash
df -h
du -sh /* 2>/dev/null | sort -h | tail -n 5
```

- `df -h` zeigt, welche Partition voll ist (Spalte `Use%`).
- `du -sh /*` zeigt die Größe jedes Ordners. `sort -h` sortiert nach Größe, `tail -n 5` zeigt die fünf größten.
- `2>/dev/null` blendet Fehlermeldungen aus.

Danach in den größten Ordner wechseln und mit demselben Befehl weitersuchen, bis der Verursacher gefunden ist.

## Fall 3: Der Server ist plötzlich langsam

```bash
ps aux --sort=-%cpu | head
systemctl status rechenknecht
systemctl stop rechenknecht
```

Ein Skript verbrauchte fast 100 Prozent CPU. Es gehörte zu einem Dienst. Darum habe ich den Dienst sauber mit `systemctl stop` beendet und nicht den Prozess mit `kill` abgeschossen.

**Gelernt:** Erst herausfinden, wem ein Prozess gehört. Ein Dienst sollte über systemd gestoppt werden.

## Hinweis zu Logs

Auf systemd-Systemen liegen viele Logs nicht als Textdatei in `/var/log`, sondern im Journal. `grep` allein findet sie nicht, dafür gibt es `journalctl`:

```bash
journalctl --no-pager | grep -iE 'fail|error|denied'
```
