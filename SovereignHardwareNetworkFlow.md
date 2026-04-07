graph TD
    %% External World
    World((🌐 Public Internet)) --- ISP[🏢 ISP Modem <br/> 'Bridge Mode']
    
    %% The Sovereign Border
    ISP ---|Public IP| Scribe[🍓 Scribe: Pi 5 Gateway]
    
    subgraph Scribe_Logic [Gateway Logic Layer]
        Scribe --> FW[🔥 nftables Firewall]
        Scribe --> DNS[🔍 Unbound DNS <br/> DoT to Quad9]
        Scribe --> Node[🚀 Node.js <br/> Orchestrator]
    end

    %% The Physical Segmentation
    Scribe ===|Trunk Link / 1Gbps| Netgear[🔌 Netgear Managed Switch]

    %% Trust Zones (VLANs)
    subgraph Trusted_Zone [VLAN 10: The Vault]
        Netgear --- Sentinel[🚀 Sentinel PC <br/> i9-12900K / iGPU]
        Sentinel --- W[🧠 Watchman <br/> OpenVINO / SmolLM2]
    end

    subgraph Health_Zone [VLAN 20: Medical]
        Netgear --- Med[🏠 Apple Health Hub <br/> & Medical Sensors]
    end

    subgraph Untrusted_Zone [VLAN 30: IoT Sandbox]
        Netgear --- IoT[🔒 Smart Lamps, <br/> Generic IoT]
    end

    %% Hardware Security
    Relay[🔌 GPIO Physical Relay] -.->|Kill Signal| Scribe
    Relay -.->|Power Cut| IoT

    %% Styling
    style Scribe fill:#f96,stroke:#333,stroke-width:4px
    style Sentinel fill:#bbf,stroke:#333,stroke-width:2px
    style Relay fill:#f66,stroke:#333,stroke-width:2px
    style Trusted_Zone fill:#e1f5fe,stroke:#01579b
    style Untrusted_Zone fill:#ffebee,stroke:#b71c1c
