sequenceDiagram
    autonumber
    participant BLE as BLE Device
    participant GW as Gateway
    participant MQTT as MQTT Broker
    participant DEC as Decoder Service
    participant PROF as Profile Service
    participant DB as Database
    participant UI as Web UI

    note over BLE, GW: Physical RF Layer
    BLE->>GW: Broadcasts Advertisement Packet
    
    activate GW
    note right of GW: Step 2: Wrap packet in JSON<br/>(add RSSI & Gateway MAC)
    GW->>MQTT: Publish payload to MQTT topic
    deactivate GW
    
    activate MQTT
    MQTT->>DEC: Deliver message (Subscribed topic)
    deactivate MQTT
    
    activate DEC
    note right of DEC: Step 4: Normalize format<br/>(extract raw service data)
    DEC->>PROF: Pass normalized raw data
    deactivate DEC
    
    activate PROF
    note right of PROF: Step 5: Identify device type<br/>& parse service data<br/>(e.g., temp, battery)
    PROF->>DB: Save processed device state
    deactivate PROF
    
    activate DB
    note right of DB: Data persisted
    deactivate DB

    par Web UI Update (Asynchronous)
        note over DB, UI: Step 7: Polling or WebSocket push
        DB-->>UI: Receive update data (Socket/Poll response)
        activate UI
        UI->>UI: Update device status on screen
        deactivate UI
    end