sequenceDiagram
    autonumber
    participant U as 👤 User Device
    participant P as 🍓 Scribe (Pi 5 Gateway)
    participant ISP as 🏢 ISP (Blind Transit)
    participant Q as 🛡️ Quad9 (9.9.9.9)
    participant C as ☁️ AWS CloudFront (Relay)
    participant I as 🌐 Public Internet (Target)

    Note over U,ISP: [PHASE 1: DNS DISCOVERY]
    U->>P: 1. Request: "anonymize sensitive-site.com"
    
    P->>ISP: 2. Encrypted DNS Query (DoT / Port 853)
    Note over ISP: ISP SEES: [Home IP] -> [9.9.9.9] <br/> Payload: [ENCRYPTED BLOB]
    
    ISP->>Q: 3. Deliver Blob to Quad9
    Q-->>ISP: 4. Return Encrypted IP Response
    ISP-->>P: 5. Relay IP to Scribe

    Note over P,I: [PHASE 2: TRAFFIC OBFUSCATION]
    P->>ISP: 6. Request via CloudFront (TLS 1.3)
    Note over ISP: ISP SEES: [Home IP] -> [AWS IP] <br/> Host: [secure-relay.yourdomain.com]
    
    ISP->>C: 7. Forward to CloudFront Edge
    Note right of C: CloudFront strips Home IP & <br/> randomizes Metadata
    
    C->>I: 8. Exit to Target (via AWS Anycast IP)
    Note over I: Target SEES: [AWS IP] <br/> (Zero link to Home IP)
    
    I-->>C: 9. Data Response
    C-->>ISP: 10. Relay Encrypted Response
    ISP-->>P: 11. Delivery to Gateway
    P-->>U: 12. Final Secure Render

    Note over ISP: TOTAL ISP KNOWLEDGE: <br/> 1. You use Quad9 <br/> 2. You use AWS <br/> 3. 0% Content Visibility
