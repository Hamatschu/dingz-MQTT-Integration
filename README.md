 dingz inteligente Schalter - MQTT Integration by Hamatschu & Gemini

 Aufgebaut auf dingz MQTT topics FW 2.1.58 V1.03 - Jan 2025 mit HAOS 2026.8

<img width="1235" height="2131" alt="dingz MQTT HA" src="https://github.com/user-attachments/assets/64ac8816-b42e-48f8-81c7-a3d59203a903" />



    Tipps für die Praxis

    Keine Raumnamen verwenden und keine suggested_area nutzen: 
        Gib den Entitäten keinen Raumnamen mit, das erspart dir Anpassungen in der YAML, wenn du was änderst.
        Das Selbe mit suggested_area: "Wohnzimmer", ordnet Home Assistant das komplette Gerät beim 
        ersten Erkennen direkt dem passenden Raum zu.

    Anker anpassen: 
        Beachte die YAML-Anker &dingz_78c8 oder &f008d1c578c8(Definition beim ersten Eintrag) und 
        *dingz_78c8 / *f008d1c578c8 (Wiederverwendung bei allen Sensoren dieses Raums). 
        Für jedes neue Gerät vergibst du ein neues Wort oder die MAC adresse.

    Lösche alles raus, was du nicht brauchst.
