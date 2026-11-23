# Linux Automation Labs

Praxisprojekt aus der Umschulung zur **Fachinformatikerin für Systemintegration** (GFN, Oktober 2026).
In diesem Repository sammle ich meine eigenen Lösungen aus den Linux-Laboren: Fehler in Diensten finden und beheben, Routineaufgaben mit Bash automatisieren und Daten sichern.

> **Hinweis:** Die Aufgaben stammen aus dem GFN-Kurs (Linux Essentials). Die Skripte, Befehle und Erklärungen hier sind meine eigene Arbeit.
> Die Übungen liefen in isolierten Lernumgebungen. Es sind keine Zugangsdaten und keine echten Systeme enthalten.

## Inhalt

| Teil | Thema | Wichtigste Werkzeuge |
|---|---|---|
| [01 Fehlersuche bei Diensten](01-fehlersuche-dienste.md) | Dienst startet nicht, Platte voll | systemctl, journalctl, sed, df, du |
| [02 Automatisierung mit Bash](02-automatisierung-bash.md) | Auswertungen, Platzwarnung, cron | cut, sort, uniq, awk, du, cron |
| [03 Backup und Prüfsummen](03-backup-und-pruefsummen.md) | Archive, Spiegel, Rotation, Prüfsummen | tar, rsync, sha256sum, xargs |
| [Bash- und Regex-Übungen](https://github.com/gulerzenginkisaoglu-7838/linux-serveradministration/tree/main/bash-und-regex-uebungen) | Schleifen, Bedingungen, grep | if, for, while, case, grep -E |

## Was ich dabei gelernt habe

- **Nicht raten, Logs lesen:** Erst `systemctl status` und `journalctl`, dann die Konfiguration prüfen, dann reparieren und testen.
- **Ursache vor Symptom:** Ein Dienst, der nicht startet, hatte nur einen falschen Dateipfad in der Unit-Datei.
- **Skripte kurz und prüfbar halten:** Eine Pipe aus einfachen Werkzeugen ersetzt oft ein langes Programm.
- **cron:** Läuft nur zur vollen Minute und braucht absolute Pfade.
- **Backups sind erst gut, wenn man sie prüft:** Prüfsumme erzeugen und sofort verifizieren.

## Verwendete Befehle (Überblick)

`systemctl` · `journalctl` · `sed` · `df` · `du` · `sort` · `uniq` · `awk` · `cut` · `tar` · `rsync` · `xargs` · `sha256sum` · `cron`

---
**Autorin:** Güler Zengin Kisaoğlu · Umschulung Fachinformatikerin für Systemintegration (GFN)
