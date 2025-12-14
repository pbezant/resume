graph TD
    %% Define Components
    A[BLE Devices]
    B[Gateways]
    C[MQTT Broker]
    D(Database: MongoDB)
    E(Frontend: Vue.js FMS)
    
    subgraph Sense_Engine_Core
        subgraph Decoder_Layer [Decoder Layer]
            D1[ESP Decoder]
            D2[MOKO Decoder]
            D3[Meraki Decoder]
        end
        
        subgraph Profile_Layer [Profile Layer]
            P1[BXP Profile]
            P2[Eye Sensor Profile]
        end
        
        T[Triangulation Engine]
        
        %% Internal Flow
        D1 --> P1
        D2 --> P1
        D3 --> P2
        P1 --> T
        P2 --> T
    end
    
    %% External Flow (Data Path)
    A --> B
    B --> C
    
    %% MQTT input to all Decoders
    C --> D1
    C --> D2
    C --> D3
    
    %% Storage & Output
    T --> D
    D --> E