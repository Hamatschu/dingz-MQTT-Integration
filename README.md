# Manuelle MQTT-Konfiguration von dingz MQTT in Home Assistant

 Aufgebaut auf dingz MQTT topics FW 2.1.58 V1.03 - Jan 2025 mit HAOS 2026.8
Dieses Repo ist für eien reine MQTT integration von der smarten Lichtschaltern von [dingz](https://dingz.ch/).
Die Konfiguration basiert auf den [dingz MQTT topics FW 2.1.58 V1.03 - Jan 2025](https://cdn.prod.website-files.com/6798af77d7c53b4f7912ec17/67cecc6a432cd53fdbeabc4b_dingz%20MQTT%20Topics%2020250114.pdf) und wurde in **Home Assistant  2026.8** getestet.

> Dieses Projekt stellt keine offizielle Integration von dingz / iolo AG / myStrom AG dar.

## ✨ Funktionen

Mit der enthaltenen `mqtt.yaml` können dingz-Geräte über MQTT als ein Gerät in Home Assistant eingebunden werden.
Aktuell werden unter anderem folgende Funktionen unterstützt:

* 💡 Licht / Dimmer
* 🔌 Schaltbare Ausgänge
* 🌡️ Temperatur
* ☀️ Helligkeit / Lux
* ⚡ Leistung der einzelnen Ausgänge
* 🔘 Taster-Events
* 🚶 PIR-Bewegungssensor
* 💡 Status der Front-LED mit den einzelne LED-Farbkanäle (kein Nachtlicht)
* 📡 Online-/Verbindungsstatus
* 🌐 IP-Adresse
* 🔧 Hardware-Version
* 💾 Firmware-Version
* 📦 Gerätemodell

<img width="1235" height="2131" alt="dingz MQTT HA" src="https://github.com/user-attachments/assets/64ac8816-b42e-48f8-81c7-a3d59203a903" />

---

## 📋 Voraussetzungen

Du benötigst:

* natürlich **Home Assistant** mit **MQTT Broker**
* ein **dingz-Gerät**
* ein dingz, das für MQTT konfiguriert wurde
* dingz verbunden mit Netzwerk

Die verwendeten MQTT Topics orientieren sich an der Dokumentation:
**dingz MQTT Topics – FW 2.1.58 / V1.03 – Januar 2025**

---

## 🚀 Installation



---

# 🔧 Gerät anpassen

Die mitgelieferte YAML ist auf ein Beispielgerät mit der ID

```text
f008d1c578c8
```

ausgelegt.

Diese ID musst du für dein eigenes dingz-Gerät überall anpassen.

Beispielsweise:

```yaml
state_topic: "dingz/f008d1c578c8/dz1f-pir/state/light/0"
```

wird zu:

```yaml
state_topic: "dingz/DEINE_ID/dz1f-pir/state/light/0"
```


---

## ⚓ YAML Anchors

Die YAML verwendet **YAML Anchors**, damit die Geräteinformationen nicht bei jeder Entität wiederholt werden müssen.

Die Definition erfolgt beispielsweise hier mit **(&)**:

```yaml
device: &f008d1c578c8
  identifiers: "dingz_f008d1c578c8"
  name: "dingz_f008d1c578c8"
  manufacturer: "dingz"
  model: "dz1f-pir"
```

Weitere Entitäten desselben Geräts verwenden anschliessend einen **(*)**:

```yaml
device: *f008d1c578c8
```

### Für ein neues Gerät

Für jedes weitere dingz-Gerät solltest du einen eigenen Anchor verwenden.

Beispielsweise:

```yaml
device: &dingz_123456789abc
  identifiers: "dingz_123456789abc"
  name: "dingz_123456789abc"
  manufacturer: "dingz"
  model: "dz1f-pir"
```

Danach:

```yaml
device: *dingz_123456789abc
```

Die Anchor-Namen können frei gewählt werden. Eine Verwendung der Geräte-ID oder MAC Adresse als Anchor macht die Zuordnung vor allem bei mehreren Geräten einfacher.

---

# 💡 Licht und Dimmer

Die vier Ausgänge des Beispielgeräts sind als Home-Assistant-Licht-Entitäten konfiguriert, da dies der primäre Verwendungszwek ist.

```text
Licht 1
Licht 2
Licht 3
Licht 4
```

Die Dimmerwerte werden zwischen `0` und `100` Prozent verarbeitet.

Beispiel:

```yaml
brightness_command_template: '{"turn":"on","brightness":{{ value }}}'
```

Damit kann Home Assistant sowohl den Ein-/Aus-Zustand als auch die Helligkeit steuern.

---

# 🔌 Ausgänge als Schalter verwenden

Wenn ein Ausgang nicht als Dimmer, sondern ausschließlich als **Ein-/Aus-Schalter bzw. Steckdose** verwendet werden soll, kann die entsprechende `switch:`-Konfiguration benutzt werden.

Beispiel:

```yaml
switch:
  - name: "Steckdose 2"
    unique_id: "dingz_f008d1c578c8_switch_1"
    state_topic: "dingz/f008d1c578c8/dz1f-pir/state/light/1"
    command_topic: "dingz/f008d1c578c8/dz1f-pir/command/light/1"
    value_template: "{{ value_json.turn }}"
    state_on: "on"
    state_off: "off"
    payload_on: '{"turn":"on"}'
    payload_off: '{"turn":"off"}'
    device_class: outlet
    device: *f008d1c578c8
```

Die entsprechende Konfiguration ist in der `mqtt.yaml` bereits vorbereitet, aber standardmäßig auskommentiert.

---

# 🌡️ Sensoren

Die YAML stellt verschiedene Sensoren bereit.


## 🌡️ Sensoren-Werte

Die YAML stellt verschiedene Sensoren bereit:

| Sensor             | Einheit |
| ------------------ | ------- |
| Temperatur         | `°C`    |
| Helligkeit         | `lx`    |
| Leistung Ausgang 1 | `W`     |
| Leistung Ausgang 2 | `W`     |
| Leistung Ausgang 3 | `W`     |
| Leistung Ausgang 4 | `W`     |
|


## 🔘 Taster-Events

Die Taster werden als Sensoren eingebunden.

Beispielsweise:

```text
Taste 1
Taste 2
Taste 3
Taste 4
```

Die dingz MQTT Events können unter anderem folgende Werte liefern:

| Event | Bedeutung              |
| ----- | ---------------------- |
| `p`   | Press / Tastendruck    |
| `h`   | Hold / Gedrückt halten |
| `m2`  | Doppelklick            |
| `m3`  | Dreifachklick          |
| `r`   | Release nach Hold      |

Der Sensor enthält jeweils das zuletzt empfangene Event.

Damit können in Home Assistant beispielsweise Automationen erstellt werden:

```yaml
trigger:
  - platform: state
    entity_id: sensor.dingz_taste_1
    to: "m2"
```

---

## 💡 LED

Der Status der dingz-LED kann ebenfalls ausgelesen werden.

Zusätzlich sind einzelne Farbkanäle vorhanden:

```text
LED -> on / off
LED Rot
LED Grün
LED Blau
```

Die Werte liegen im Bereich `0–255`.

> Die LED und RGB Status zeigt nicht die Nachtlicht Funktion an.

---

# 🚶 PIR-Bewegungssensor

Der PIR-Sensor wird als `binary_sensor` eingebunden.

Die dingz Events werden dabei auf einen klassischen Home-Assistant-Zustand abgebildet:

```text
s  -> ON
ss -> ON
n  -> OFF
```

Dadurch kann der PIR direkt für Home-Assistant-Automationen verwendet werden.

Beispiel:

```yaml
trigger:
  - platform: state
    entity_id: binary_sensor.dingz_pir
    to: "on"
```

---

# 📡 Diagnoseinformationen

Das Gerät stellt verschiedene Diagnoseinformationen über das `announce` MQTT Topic bereit.

Daraus werden folgende Sensoren erstellt:

* Modell
* IP-Adresse
* Firmware
* Hardware

Zusätzlich gibt es einen Online-Status:

```text
Online Status
```

Damit lässt sich beispielsweise erkennen, ob das dingz-Gerät aktuell erreichbar ist.

---

# 🏠 Home Assistant Räume

## Empfehlung

Ich empfehle, in der MQTT-Konfiguration **keine festen Raumnamen** zu hinterlegen.

Also beispielsweise nicht:

```yaml
suggested_area: "Wohnzimmer"
```

und auch keine Raumnamen direkt als Entität-Namen einzubauen.

Der Grund:

Wenn du das Gerät später in Home Assistant einem anderen Raum zuordnest, Räume umbenenst oder Geräte neu einbindest, musst du die YAML entsprechend nicht anpassen.


---

# 🧹 Nicht benötigte Entitäten entfernen

Die `mqtt.yaml` enthält möglichst viele Funktionen des dingz-Geräts. Du musst aber nicht alles verwenden.

Wenn du beispielsweise keine LED-Sensoren benötigst, kannst du diese Bereiche einfach entfernen oder auskommentieren.

Das Gleiche gilt für:

* einzelne Leistungssensoren
* Taster
* Diagnose-Sensoren
* RGB-Sensoren
* Schalter
* PIR

**Je weniger du benötigst, desto übersichtlicher bleibt deine Home-Assistant-Installation.**

---

# 🛠️ Fehlerbehebung

### Gerät wird in Home Assistant nicht angezeigt

Prüfe:

1. Ist das dingz-Gerät im Netzwerk erreichbar?
2. Ist MQTT auf dem Gerät korrekt konfiguriert?
3. Läuft der MQTT Broker?
4. Ist Home Assistant mit dem MQTT Broker verbunden?
5. Stimmen die MQTT Topics?
6. Wurde die richtige Geräte-ID in `mqtt.yaml` eingetragen?
7. Wurde Home Assistant nach Änderungen an der YAML neu gestartet bzw. die MQTT-Konfiguration neu geladen?

---

### MQTT Topics überprüfen

Mit einem MQTT Client kannst du überprüfen, ob das dingz tatsächlich Nachrichten veröffentlicht.

Beispielsweise:

```bash
mosquitto_sub -h MQTT_BROKER -t 'dingz/#' -v
```

Damit sollten die MQTT Nachrichten des dingz-Geräts sichtbar werden.


---

# ⚠️ Hinweise

Dieses Projekt wurde für die genannten dingz MQTT Topics und die verwendete Home-Assistant-Version erstellt. Andere Firmware-Versionen können andere MQTT Topics oder Payloads verwenden.

Wenn sich die MQTT API des dingz-Geräts ändert, müssen möglicherweise die Topics und Templates in `mqtt.yaml` angepasst werden.

---

# 🤝 Beiträge

Verbesserungen, Fehlerberichte und Erweiterungen sind willkommen.

Wenn du einen Fehler findest oder Unterstützung für weitere dingz-Funktionen hinzufügen möchtest, kannst du gerne ein **Issue** oder einen **Pull Request** erstellen.

---

# 📜 Lizenz

Dieses Projekt steht unter der im Repository enthaltenen **Unlicense**.

---

    Tipps für die Praxis
## 🔗 Links

    Keine Raumnamen verwenden und keine suggested_area nutzen: 
        Gib den Entitäten keinen Raumnamen mit, das erspart dir Anpassungen in der YAML, wenn du was änderst.
        Das Selbe mit suggested_area: "Wohnzimmer", ordnet Home Assistant das komplette Gerät beim 
        ersten Erkennen direkt dem passenden Raum zu.
* [GitHub Repository](https://github.com/Hamatschu/dingz-MQTT-Integration)
* [Home Assistant](https://www.home-assistant.io/)
* [dingz](https://dingz.ch/)

    Anker anpassen: 
        Beachte die YAML-Anker &dingz_78c8 oder &f008d1c578c8(Definition beim ersten Eintrag) und 
        *dingz_78c8 / *f008d1c578c8 (Wiederverwendung bei allen Sensoren dieses Raums). 
        Für jedes neue Gerät vergibst du ein neues Wort oder die MAC adresse.
---

    Lösche alles raus, was du nicht brauchst.
**dingz MQTT Integration für Home Assistant**
*by Hamatschu & Gemini*
