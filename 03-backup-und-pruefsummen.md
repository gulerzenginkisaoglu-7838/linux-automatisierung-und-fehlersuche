# 03 Backup, Synchronisation und Prüfsummen

Backups gehören zu den wichtigsten Aufgaben eines Administrators. Hier sind die Werkzeuge, mit denen ich geübt habe: `tar`, `rsync`, ein Rotationsskript und Prüfsummen.

## Archiv erstellen, prüfen, wiederherstellen (tar)

```bash
# Backup des Ordners /srv/app anlegen
tar czf /srv/backups/app-backup.tar.gz -C /srv app

# Inhalt ansehen, ohne zu entpacken
tar tzf /srv/backups/app-backup.tar.gz

# Wiederherstellen nach /srv
tar xzf /srv/backups/kunde.tar.gz -C /srv
```

| Option | Bedeutung |
|---|---|
| `c` | Archiv erstellen |
| `x` | Archiv entpacken |
| `t` | Inhalt anzeigen |
| `z` | mit gzip komprimieren |
| `f` | Dateiname des Archivs |
| `-C` | vor dem Packen/Entpacken in dieses Verzeichnis wechseln |

Mit `-C /srv app` stehen im Archiv relative Pfade (`app/...`) und nicht `/srv/app/...`. Das Archiv lässt sich dadurch an jeder Stelle wiederherstellen.

## Ordner spiegeln mit rsync

```bash
# Verzeichnis spiegeln
rsync -av /srv/app/ /srv/mirror/

# bestimmte Dateien ausschließen
rsync -av --exclude='*.tmp' --exclude='cache/' /srv/app/ /srv/mirror2/
```

**Gelernt:** Der Schrägstrich am Ende der Quelle (`/srv/app/`) bedeutet: Inhalt des Ordners kopieren. Ohne Schrägstrich würde ein Unterordner `app` im Ziel entstehen. `rsync` überträgt beim zweiten Lauf nur Änderungen.

## Alte Backups aufräumen (`rotate.sh`)

Es sollen nur die 7 neuesten Backups behalten werden.

```bash
#!/bin/bash
ls -t /srv/backups/backup-*.tar.gz | tail -n +8 | xargs -r rm --
```

- `ls -t` sortiert nach Änderungszeit, neueste zuerst
- `tail -n +8` gibt alles ab der 8. Zeile aus, also die älteren Dateien
- `xargs -r rm --` löscht diese; `-r` verhindert einen Fehler bei leerer Liste

## Backup mit Datum und Prüfsumme (`sichern.sh`)

```bash
#!/bin/bash
datei="/srv/backups/app-$(date +%F).tar.gz"
tar czf "$datei" -C /srv app
sha256sum "$datei" > "$datei.sha256"
sha256sum -c "$datei.sha256"
```

Das Skript legt ein Backup mit Datum im Namen an, speichert die SHA-256-Prüfsumme in einer eigenen Datei und prüft sie sofort. Die Ausgabe `OK` zeigt, dass die Datei unverändert ist. Vor einer Wiederherstellung sollte man die Prüfsumme immer erneut prüfen, damit man kein beschädigtes Backup einspielt.

## Zeitgesteuert ausführen

```bash
echo '* * * * * root /root/ops/nightly.sh' > /etc/cron.d/nightly
```

Zum Testen läuft das Skript jede Minute. Im Alltag würde man es nachts ausführen, zum Beispiel `30 2 * * *` für 02:30 Uhr.

## Was ich daraus mitnehme

- Ein Backup ist erst dann ein Backup, wenn man es **wiederherstellen** und **prüfen** kann.
- Datum im Dateinamen und Rotation verhindern, dass der Speicher volläuft.
- Prüfsummen erkennen beschädigte oder veränderte Dateien.
- Automatisierung mit cron spart Arbeit und vermeidet vergessene Backups.
