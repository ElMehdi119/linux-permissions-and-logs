# Linux-Benutzerrechte und Dienst-Logs prüfen

## Ziel
An einem lokalen Testbenutzer prüfen, wie Dateirechte den Lesezugriff beeinflussen, und die Logs eines laufenden Dienstes ansehen.

## Umgebung
Kali Linux in VirtualBox mit systemd. Alle Tests fanden auf der eigenen VM statt.

## Durchführung
- Testbenutzer `labreader` ohne Login-Shell angelegt.
- Testdatei `/tmp/linux-permissions-lab/note.txt` als Benutzer `kali` erstellt.
- Rechte und Eigentümer mit `stat` geprüft.
- Lesezugriff mit `sudo -u labreader -- cat /tmp/linux-permissions-lab/note.txt` getestet.
- Rechte von `600` auf `644` geändert, denselben Zugriff wiederholt und anschließend `600` wiederhergestellt.
- Dienststatus mit `systemctl is-active cron.service` und Logs mit `sudo journalctl -u cron.service -b -n 20 --no-pager` geprüft.

## Beobachtungen
- Bei `600` gehörte die Datei `kali:kali`; `labreader` erhielt „Permission denied“ (Exit-Status 1).
- Bei `644` konnte `labreader` den Testinhalt lesen (Exit-Status 0).
- `cron.service` war aktiv. Das Journal zeigte unter anderem cron-Sitzungen für root sowie Einträge zu `run-parts` für `/etc/cron.hourly`, `debian-sa1` und `php sessionclean`.
- Zum Schluss hatte die Testdatei wieder die Rechte `600`.

## Was ich gelernt habe
Die Dateirechte entscheiden, ob ein anderer lokaler Benutzer eine Datei lesen kann. Mit `systemctl` lässt sich der Dienststatus prüfen; `journalctl` zeigt die zugehörigen protokollierten Ereignisse.
