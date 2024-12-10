```mermaid
sequenceDiagram
    participant Client
    participant SP as Service Provider (SP)
    participant IdP as Identity Provider (IdP)
    participant LDAP

    Client->>SP: L'utente tenta di accedere al SP (Esse3)
    SP->>IdP: Reindirizza l'utente all'IdP per l'autenticazione
    IdP->>Client: Richiede le credenziali dell'utente
    IdP->>LDAP: Verifica le credenziali dell'utente tramite LDAP
    LDAP->>IdP: Restituisce il risultato della verifica
    IdP->>LDAP: Recupera gli attributi dell'utente da LDAP
    IdP->>SP: Genera un token di autenticazione e reindirizza l'utente al SP
    SP->>Client: Concede l'accesso all'utente
