flowchart TB
  subgraph Edge["Global Edge (GCP)"]
    DNS[Cloud DNS: pouriax.com]
    LB[HTTPS Load Balancer\n+ Cloud Armor/WAF + TLS + CDN]
    DNS --> LB
  end

  subgraph EU["EU Region (europe-west1)"]
    FE_EU[Cloud Run\nFrontend EU\n(eu.app.pouriax.com)]
    API_EU[Cloud Run\nAPI EU\n(eu.api.pouriax.com)]
    VPC_EU[VPC Serverless Connector]
    SQL_EU[Cloud SQL Postgres (EU)\nPrivate IP/PSC]
    SM_EU[Secret Manager (EU)]
    LOG_EU[Cloud Logging/Monitoring (EU)]
    FE_EU --> API_EU
    API_EU --> VPC_EU --> SQL_EU
    API_EU -.-> SM_EU
    FE_EU -. metrics/logs .-> LOG_EU
    API_EU -. metrics/logs .-> LOG_EU
  end

  subgraph CA["Canada Region (northamerica-northeast1)"]
    FE_CA[Cloud Run\nFrontend CA\n(ca.app.pouriax.com)]
    API_CA[Cloud Run\nAPI CA\n(ca.api.pouriax.com)]
    VPC_CA[VPC Serverless Connector]
    SQL_CA[Cloud SQL Postgres (CA)\nPrivate IP/PSC]
    SM_CA[Secret Manager (CA)]
    LOG_CA[Cloud Logging/Monitoring (CA)]
    FE_CA --> API_CA
    API_CA --> VPC_CA --> SQL_CA
    API_CA -.-> SM_CA
    FE_CA -. metrics/logs .-> LOG_CA
    API_CA -. metrics/logs .-> LOG_CA
  end

  LB -->|Host: eu.app.pouriax.com| FE_EU
  LB -->|Host: eu.api.pouriax.com| API_EU
  LB -->|Host: ca.app.pouriax.com| FE_CA
  LB -->|Host: ca.api.pouriax.com| API_CA

  Clerk[Clerk (SaaS)]
  FE_EU <-- auth/JWT --> Clerk
  FE_CA <-- auth/JWT --> Clerk
  API_EU <-- verify JWT (region=EU) --> Clerk
  API_CA <-- verify JWT (region=CA) --> Clerk
