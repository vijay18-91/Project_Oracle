sequenceDiagram
    autonumber
    participant A as 🕵️ Attacker / Malicious Actor
    participant I as 🌐 Public Internet
    participant P as 🍓 Scribe (Pi 5 / Gateway)
    participant O as 🚀 Node.js Orchestrator
    participant W as 🛡️ Watchman (i9 iGPU / SmolLM2)
    participant K as 🔌 Physical Relay (Hardware Kill-Switch)
    participant H as 🏠 Compromised Home IoT Device

    Note over H,I: [Scenario: IoT Device attempts 0-day Data Exfiltration]
    H->>P: 1. Outbound Egress Request (Non-standard port)
    
    Note right of P: nftables intercepts, <br/>but doesn't block yet (monitoring mode)
    P->>O: 2. Emit Network Log Event (CrowdSec alert)
    
    O->>W: 3. Request Security Inference (Raw Telemetry)
    Note right of W: SmolLM2-1.7B Logic: "Signature <br/>indicates unauthorized cloud sync."
    
    W-->>O: 4. Decision: [THREAT_DETECTED_CRITICAL]
    
    alt AUTOMATED DEFENSE ( <200ms )
        O->>P: 5a. Software-Layer Block (nftables DROP rule active)
        O->>K: 6a. GPIO TRIGGER: HIGH
        K-->>K: 7a. PHYSICAL RELAY "CLICK" (Air-Gap)
        
        Note over K,H: NETWORK LINK PHYSICALLY CUT. NO EGRESS POSSIBLE.
    else Attacker Response (Fails)
        A-X I: Attacker attempts re-connection
        I-X P: Attacker is blocked at Hardware Layer
    end
