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

### Setup - Teil 1 - Weatherman

- die Sensordaten des Weatherman werden über den Port 8181 in Node-RED abgegriffen
  - dazu muss beim Weatherman im 'Expertenmodus' eingestellt werden:
    - CCU-Betrieb >> JSON-Daten an Server@CCU-IP
      - http://<IP-Weatherman\>/?ccu:<IP-NodeRED-Server\>:
      - http://<IP-Weatherman\>/?param:12:1:
- die Ortshöhe über N.N. muss im Weatherman auf '0' gesetzt werden, da die barometrische Höhenreduktion über das Node-RED Modul erfolgt:
  - http://<IP-Weatherman\>/?param:20:0:

### Setup - Teil 2 - Node-RED

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

### Setup - Teil 3 - Konfiguration Weatherman Device

Hier finden sich die Device CSV-Konfigurationsdateien für den Weatherman:

[myHOME_Devices_WM_all.csv](./bin/myHOME_Devices_WM_all.csv)<br>
[myHOME_Devices_WM_std.csv](./bin/myHOME_Devices_WM_std.csv)

Die CSV-Dateien sind vorkonfiguriert und können sofort benutzt werden. Bitte die CSV-Dateien bei einer Anpassung NICHT mit MS Excel editieren - besser: Notepad++ benutzen (UTF-8 Zeichensatz)!!

Erläuterungen zu den CSV-Spalten finden sich in der Beschreibung:

[Datenbank-Tabelle - SENSOR_DEVICES](/myHOME%20-%20Datenbank/README.md#datenbank-tabelle---sensor_devices)

Anpassung der barometrischen Ortshöhen-Beschreibung "hPa (190 m ü. N.N.)" in der Zeile 23 der Datei 'myHOME_Devices_xxx.csv' von '190' auf den N.N. Höhenwert, auf den der gemessene barometrische Wert reduziert werden soll.

Optional können weitere Anpassungen in den Spalten ID_DEV, DEV_DESC, DEV_LOC, DEV_VAL_DESC, DEV_VAL_UNIT, DEV_MQTT, DEV_FL_ACT, DEV_FL_STO, DEV_FL_MQTT vorgenommen werden.

Kopieren der angepaßten 'myHOME_Devices_xxx.csv' Dateien nach '/home/pi/.node-red/public'

- für die Minimal- und Standard-Version
  - 'myHOME_Devices_WM_std.csv' nach 'myHOME_Devices_WM.csv' kopieren
- für die Maximal-Version
  - 'myHOME_Devices_WM_max.csv' nach 'myHOME_Devices_WM.csv'

### Setup - Teil 4 - Konfiguration  Datenbank

Für die Standard-Version mit Datenbank Option und für die Maximal-Version muss die 'MariaDB SQL-Datenbank' installiert und konfiguriert werden:

[WW-myHOME - Datenbank - Setup.md](/myHOME%20-%20Datenbank/README.md)

### Setup - Teil 5 - Node-RED Weatherman

Kopieren des Weatherman Node-RED Moduls, indem die Datei 'myHOME_FLOW_Weatherman_xxx.md' auf die Flow Ebene von Node-RED im Explorer gezogen wird.

Mit einem Doppelklick auf den Node 'Init-Flow' die Anpassungen für das Weatherman Modul vornehmen.

- für alle Versionen

   - Altitude [m] - eigene Ortshöhe über N.N. zur Berechnung des barometrischen Luftdrucks in Bezug zu N.N.. Von '190' auf den N.N. Höhenwert, auf den der gemessene barometrische Wert umgerechnet werden soll, ändern<br>
   *var myBaro_NN = 190;*<br>
   <br>
   - Flow-Vorgabe, ob nur neue Readings oder alle Readings eines Devices berücksichtigt werden sollen<br>
   // = 0 - alle Readings<br>
   // > 0 - nur neue Readings (default)<br>
   *var myTmpNewOnly_Default = 1;*


- für die Minimal-Version

- für die Standard-Version

- für die Maximal-Version






### Version

1.0.0.0 - 2018-12-23
- Erstausgabe
