## WW-myHOME - Feinstaub

### Funktion
Übernahme, Aufbereitung, Deployment und Visualisierung der per WiFi versandten Sensordaten der Feinstaub Messstation (https://luftdaten.info/) durch Node-RED

### Details
- Autarkes Teilmodul zur Übernahme / Abspeichern / MQTT-Deployment von Sensordaten
- die Sensordaten des Weatherman werden über den Port 8182 in Node-RED abgegriffen
  - dazu muss beim im Feinstaub Modul unter 'Konfiguration' eingestellt werden:
    - 'An eigene API senden' - angehakt
      - Server: <IP-NodeRED-Server\>
      - Pfad: /feinstaub
      - Port: 8182
- die myHOME-Datenbank muss angelegt und für den Feinstaubsensor vorbereitet sein (siehe Doku dort)
- in der myHOME-Datenbank befindet sich für jedes Gerät/Sensor/Wert ein Konfigurationssatz, über den u.a. gesteuert werden kann, was mit dem Datensatz geschehen soll - es kann konfiguriert werden ...
  - nur die Daten werden weiterverarbeitet, für die es einen Konfigurationseintrag in der myHOME-Datenbank gibt
  - über ein Aktiv-/Inaktiv-Flag kann ein Device-Datensatz von der Verarbeitung ein- oder ausgeschlossen werden
  - über ein Datenbank-Flag kann entschieden werden, ob der Device-Datensatz in der myHOME-Datenbank abgelegt wird
  - über ein MQTT-Flag kann entschieden werden, ob der Device-Datensatz per MQTT über den eingetragenen MQTT-Pfad versandt werden soll
  - alle Device-Angaben (Name, Bezeichnung, Einheiten, etc.) sind einzeln konfigurierbar
  - für den Feinstaubsensor werden die 'value_type'-Bezeichnungen neu 'normiert'
    - damit braucht keine Code-Anpassung der Folge-Prozesse mehr erfolgen, wenn bei einer Firmware-Änderung die 'name'-Einträge geändert werden - man muss nur die Konfiguration der Device-Parameter in der myHOME-Datenbank vornehmen (Doku siehe dort) - bei den Folgeprozessen bleiben die festgelegten Device Bezeichner erhalten
- es werden intern unterschiedliche 'payloads' generiert, die für eigene Entwicklungen genutzt werden können:
  - payload: Original Device-Objekte Weatherman
  - payload_sys: 'normierte' Device-Objekte der Weatherman Systemangaben
  - payload_var: 'normierte' Device-Objekte des Weatherman
  - payload_avg: 'normierte' Device-Objekte des Weatherman - ALLE numerischen Werte werden als 'gleitende Mittelwerte' im 5 Minuten-Intervall (einstellbar) bereitgestellt
  - payload_sql: SQL-Befehle zur Übernahme in die myHOME-Datenbank
  - payload_mqtt: 'normierte' Device-Objekte zur Übergabe an MQTT-Server
- Watchdog und JSON-Fehlerbehandlung mit Statusausgabe

### Node-RED - GUI

Gesamter Flow für alle Optionen mit Debug Optionen:

![Node-RED - GUI -  WW-myHOME - Feinstaub](./img/NodeRED_GUI_Feinstaub_1.0.jpg)

### Node-RED - FLOW

Gesamter Flow für alle Optionen mit Debug Möglichkeiten:

![Node-RED - FLOW -  WW-myHOME - Feinstaub](./img/NodeRED_FLOW_Feinstaub_1.0.jpg)

Flow für reine Node-RED GUI-Darstellung über MQTT:

![Node-RED - FLOW -  WW-myHOME - Feinstaub - MQTT](./img/NodeRED_FLOW_Feinstaub_MQTT_1.0.jpg)

### MQTT Feinstaub in FHEM

Feinstaub Daten in FHEM über MQTT Broker:

![Node-RED - Feinstaub - MQTT - FHEM](./img/FHEM_Feinstaub.jpg)

### Hardware Feinstaub-Modul

Feinstaub-Modul mit Selbstbau-Shield für BME280

![Hardware - Feinstaub](./img/Hardware_Feinstaub.jpg)

### Auswertungen Feinstaub-Modul

[Link MDAVI - Feinstaub]<br>
https://www.madavi.de/sensor/graph.php?sensor=esp8266-5437269-sds011

[Link MDAVI - Sensoren]<br>
https://www.madavi.de/sensor/graph.php?sensor=esp8266-5437269-bme280

### Version

1.0.0.0 - 2018-12-07
- Erstausgabe
