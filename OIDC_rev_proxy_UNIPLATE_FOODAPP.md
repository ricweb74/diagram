```mermaid
sequenceDiagram
    autonumber

    participant User as Utente (App/WebView)
    participant RP as Reverse Proxy OIDC (Apache)
    participant IdP as Shibboleth IdP
    participant BE as Backend PHP

    User->>RP: 1. GET https://foodapp-devel/uniplate_server/login
    RP-->>User: 302 Redirect to IdP (HTTPS)

    User->>IdP: 2. Login Page (HTTPS)
    IdP-->>User: 3. Redirect to OIDCRedirectURI (HTTPS)\n/uniplate_server/login/redirect_uri#id_token=...

    User->>RP: 4. Hits redirect_uri (HTTPS)
    RP->>RP: 5. mod_auth_openidc validates token

    alt SENZA fix (problema attuale)
        RP-->>User: 302 Redirect to http://foodapp-devel/uniplate_server/login
        User--xRP: BLOCCATO (CLEARTEXT_NOT_PERMITTED)
        Note over User: Il backend non viene mai chiamato
    else CON fix (Header edit Location)
        RP-->>User: 302 Redirect to https://foodapp-devel/uniplate_server/login
        User->>RP: GET HTTPS /uniplate_server/login
        RP->>BE: Request to backend (HTTP)\ncon claim OIDC in $_SERVER
        BE-->>User: Risposta / redirect finale app (HTTPS)
    end
