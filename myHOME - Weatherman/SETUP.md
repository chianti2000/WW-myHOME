## WW-myHOME - Setup Node-RED - Weatherman

### Funktion
Übernahme, Aufbereitung, Deployment und Visualisierung der per WiFi versandten Sensordaten der Wetterstation 'Weatherman' (Dr. Stall) durch Node-RED

### Allgemein

Das Node-RED Modul ist so ausgelegt worden, dass es universiell konfiguriert und eingesetzt werden kann. Dies gilt sowohl für die technischen Voraussetzungen, als auch für die Modul interne Konfiguration. Im Folgenden wird das Aufsetzen einer Minimal-Version, einer (sinnvollen) Standard-Version und der maximalen (Debug) Version gezeigt.
Besonderer Wert wurde bei der Entwicklung darauf gelegt, dass das Deployment der Sensordaten individuell konfigurierbar ist. So werden z.B. in der Standardeinstellung nur die Sensordaten weiterverarbeitet, die sich geändert haben - dies sorgt besonders bei der MQTT Weitergabe oder dem Abspeichern in einer Datenbank für eine starke Reduzierung des Datenverkehrs bzw. für deutlich weniger Speicherplatzbedarf!

### Technische Voraussetzungen

Als Hardware Grundlage wird ein Raspberry Pi mit einer aktuellen Debian Version benötigt. Folgende Software Module werden für die einzelnen Ausbaustufen benötigt:

- Minimal-Version
  - Funktion: Übernahme, Aufbereitung und Visualisierung der Weatherman Sensordaten
  - Software Module:
    - Node-RED Installation (z.Z.: latest version ... v0.19.5 (npm))


- Standard-Version
  - Funktion: Übernahme, Aufbereitung, Deployment und Visualisierung der Weatherman Sensordaten
  - Software Module:
    - Node-RED Installation (z.Z.: latest version ... v0.19.5 (npm))
    - MQTT Broker - Mosquitto
    - optional: MariaDB SQL-Datenbank


- Maximal-Version
  - Funktion: wie Standard-Version - nur mit allen Optionen und (Debug) Möglichkeiten zur eigenen Anpassung
  - Software Module:
    - Node-RED Installation (z.Z.: latest version ... v0.19.5 (npm))
    - MQTT Broker - Mosquitto
    - MariaDB SQL-Datenbank

Die Installation von Node-RED, Mosquitto und MariaDB wird hier nicht im Einzelnen dargestellt. Es macht jedoch Sinn, alle Module so aufzusetzen, dass sie über sichere Verbindungen miteinander kommunizieren.
In der eigenen myHOME-Umgebung laufen so z.B. Node-RED, FHEM und Grafanna über einem Nginx Proxy Server.

### Setup - Teil 1 - Node-RED

Überprüfen, ob Node-RED in der aktuellen Version installiert ist. Weiter muss das Modul 'node-red-dashboard' installiert sein - siehe im Node-RED Fenster Einstellungen oben rechts und dann unter dem Menü-Eintrag 'Manage palette'. Unter dem Reiter 'Install' kann das Modul gesucht und installiert werden - falls es schon installiert ist, kann unter 'Nodes' die Version geprüft und evtl. aktualisiert werden.

Für eigene Node-RED Erweiterungen sollte man einen 'public' Ordner anlegen (TTY-Konsole - als pi user):
```
mkdir /home/pi/.node-red/public
```
Node-RED stoppen:
```
node-red-stop
```
Die Node-RED 'settings.js'-Datei öffnen:
```
sudo nano /home/pi/.node-red/settings.js
```
Folgenden Eintrag vornehmen und Datei mit 'CTRL-X' - 'Ja' abspeichern:
```
httpStatic: '/home/pi/.node-red/public',
```
Node-RED starten:
```
node-red-start
```

### Setup - Teil 2 - Konfiguration Weatherman Device

Hier finden sich die UTF-8 Device CSV-Konfigurationsdateien für den Weatherman:

[myHOME_Devices_WM_all.csv](./bin/myHOME_Devices_WM_all.csv)<br>
[myHOME_Devices_WM_std.csv](./bin/myHOME_Devices_WM_std.csv)

Die CSV-Dateien sind vorkonfiguriert und können sofort benutzt werden. Bitte die CSV-Dateien bei einer Anpassung NICHT mit MS Excel editieren - besser: Notepad++ benutzen!!

Erläuterungen zu den CSV-Spalten finden sich in der Beschreibung

[Datenbank-Tabelle - SENSOR_DEVICES](/myHOME%20-%20Datenbank/README.md)

Kopieren der 'myHOME_Devices_xxx.csv' (angepaßten) Dateien nach '/home/pi/.node-red/public'

- Minimal- und Standard-Version
  - 'myHOME_Devices_WM_std.csv' nach 'myHOME_Devices_WM.csv' kopieren
- Maximal-Version
  - 'myHOME_Devices_WM_max.csv' nach 'myHOME_Devices_WM.csv'










### Version

1.0.0.0 - 2018-12-23
- Erstausgabe
