# 02 Automatisierung mit Bash

Kleine Skripte für Aufgaben, die man sonst von Hand wiederholt. Jedes Skript habe ich mit `cat > datei << 'EOF'` angelegt, mit `chmod +x` ausführbar gemacht und getestet.

## Die drei häufigsten IP-Adressen aus einem Log (`topips.sh`)

Ein Zugriffslog hat pro Zeile eine IP-Adresse am Anfang. Gesucht sind die drei IPs mit den meisten Zugriffen.

```bash
#!/bin/bash
cut -d' ' -f1 /root/werkstatt/zugriffe.log | sort | uniq -c | sort -rn | head -n 3 | awk '{print $2}'
```

So arbeitet die Pipe:

| Schritt | Aufgabe |
|---|---|
| `cut -d' ' -f1` | erste Spalte (die IP) herausschneiden |
| `sort` | gleiche IPs untereinander bringen |
| `uniq -c` | gleiche Zeilen zählen |
| `sort -rn` | nach Anzahl absteigend sortieren |
| `head -n 3` | die ersten drei behalten |
| `awk '{print $2}'` | nur die IP ausgeben, ohne die Zahl |

**Gelernt:** Das Ergebnis war schon nach `head -n 3` inhaltlich richtig, aber mit den Zahlen davor. Ein Skript muss das Format liefern, das der nächste Schritt erwartet. Mit `awk` bleibt nur die gewünschte Spalte übrig.

## Platzwarnung (`platzwarnung.sh`)

Gibt eine Warnung aus, wenn ein Ordner größer als 50 MB ist.

```bash
#!/bin/bash
groesse=$(du -sm /root/werkstatt/daten | cut -f1)
if [ "$groesse" -gt 50 ]; then
  echo "WARNUNG: daten ist $groesse MB gross"
else
  echo "OK: daten ist $groesse MB gross"
fi
```

**Gelernt:** `du -sm` liefert die Größe in MB. `cut -f1` nimmt nur die Zahl, `$(...)` speichert sie in einer Variablen, `-gt` vergleicht Zahlen.

Beim Einfügen mehrzeiliger Skripte ins Terminal können unsichtbare Zeichen (`^[[200~`) mitkommen. Dann wird das Ende `EOF` nicht erkannt. Mit Strg+C abbrechen und noch einmal einfügen.

## Aufgabe regelmäßig ausführen mit cron

Ein Skript soll jede Minute laufen. Der Eintrag kommt in `/etc/cron.d/`:

```bash
echo '* * * * * root /pfad/zum/skript.sh' > /etc/cron.d/heartbeat
cat /etc/cron.d/heartbeat
```

Aufbau: `Minute Stunde Tag Monat Wochentag Benutzer Befehl`.

**Gelernt:**
- cron startet nur zur **vollen Minute**. Direkt nach dem Eintrag gibt es noch keine Ausgabe, einfach bis zur nächsten Minute warten.
- Immer den **vollständigen Pfad** zum Skript angeben, cron kennt `~` nicht.
- In `/etc/cron.d/` braucht jede Zeile den Benutzernamen (hier `root`).
