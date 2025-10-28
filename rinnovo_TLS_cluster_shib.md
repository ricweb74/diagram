```mermaid
flowchart LR
    Client["Internet Client / Let's Encrypt"] -- "HTTP:80" --> ha[(HAProxy 160.78.48.199)]

    subgraph HAProxy_sg["HAProxy"]
      direction TB
      A80["frontend fe_http:80"]
      A80 -- "path_beg /.well-known/acme-challenge/" --> BEACME["backend be_acme_master"]
      A80 -- else --> REDIR["301 → HTTPS"]
    end

    BEACME -- HTTP --> Apache8888["Apache :8888 (shibidp-v5 160.78.48.207)"]

    subgraph Shib_Cluster["Shib Cluster"]
      Master["shibidp-v5 160.78.48.207"]
      N1["shibidp-v5-01 160.78.48.208"]
      N2["shibidp-v5-02 160.78.48.212"]
      N3["shibidp-v5-03 160.78.48.213"]
    end

    Apache8888 --> Master
    N1 -. "ProxyPass /.well-known/acme-challenge/" .-> Apache8888
    N2 -. "ProxyPass /.well-known/acme-challenge/" .-> Apache8888
    N3 -. "ProxyPass /.well-known/acme-challenge/" .-> Apache8888

    subgraph Renewal
      CRON["cron certbot renew"]
      HOOK["deploy-hook"]
      DEPLOY["deploy-certs.sh"]
    end

    CRON -->|renew ok| HOOK --> DEPLOY
    DEPLOY --> Master
    DEPLOY --> N1
    DEPLOY --> N2
    DEPLOY --> N3
