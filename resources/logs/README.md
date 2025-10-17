# Logger

Det finnes 9 ulike loggger:

```
logs/
├── azure/
│   ├── azure_signin_log.csv          # M365 + Azure AD innlogginger
│   └── azure_audit_log.csv           # Administrative endringer i Azure
│
├── network/
│   └── oslo_office_firewall.csv      # On-prem perimeter firewall
│
├── prod-norway-east/
│   ├── app-server-01.csv             # Hovedserver - Norge Øst
│   └── db-server-01.csv              # Database - Norge Øst
│
├── prod-norway-west/
│   ├── app-server-02.csv             # Backup server - Norge Vest
│   └── db-server-02.csv              # Database - Norge Vest
│
└── prod-west-europe/
    ├── app-server-03.csv             # Internasjonal server - West Europe
    └── db-server-03.csv              # Database - West Europe
```

## **Angrepets flyt gjennom infrastrukturen:**

1. **Initial compromise (Oslo):**
   - Ola i Oslo-kontoret klikker på phishing
   - Spor: `oslo_office_firewall.csv` (DNS til fake domain)
   - Spor: `azure_signin_log.csv` (første innlogging)
2. **Privilege escalation (Azure AD):**
   - Angriper deaktiverer Conditional Access
   - Spor: `azure_audit_log.csv`
3. **Lateral movement (Norway East først):**
   - SSH til `prod-app-server-01` (primær region)
   - Spor: `app-server-01.csv`
   - Database eksfiltrasjon fra `db-server-01`
   - Spor: `db-server-01.csv`
4. **Spreading (andre regioner):**
   - Bruker admin-tilgang til å hoppe til andre regioner
   - Kompromitterer app-server-02 og 03
   - Spor: `app-server-02.csv`, `app-server-03.csv`
5. **Ransomware deployment:**
   - Deployes til ALLE 3 regioner simultant
   - Krypterer både app-servere OG databaser
   - Spor: Alle server-logger

## Detaljert Event Timeline - Nordavind Solutions Cyberangrep

### IP-adresse Referanse

- **Ola Hansen kontor-PC:** `10.0.1.142`
- **Ola VPN IP (når koblet til Azure):** `10.10.0.15`
- **Kjetil Berg kontor-PC:** `10.0.1.087`
- **Mari Hansen kontor-PC:** `10.0.1.156`
- **Angriper - Norsk VPN:** `185.23.187.142`
- **Angriper - Romania 1:** `89.34.237.89`
- **Angriper - Romania 2:** `89.34.237.91`
- **Eksfiltrasjon server 1:** `104.21.58.247` (backup-cdn-eu.net)
- **Eksfiltrasjon server 2:** `172.67.142.33` (data-sync-service.com)
