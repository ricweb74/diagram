```mermaid
flowchart TD
    A[Utente <br/> (Browser/Client)] -->|1. Richiede risorsa protetta| B[Applicazione Client]
    B -->|2. Reindirizza l'utente all'Authorization Server con client_id, scope, redirect_uri e state| C[Authorization Server]
    C -->|3. Richiede autenticazione e consenso| A
    A -->|4. Fornisce credenziali e autorizzazione| C
    C -->|5. Reindirizza al redirect_uri con Authorization Code| B
    B -->|6. Invia Authorization Code e client_secret al Token Endpoint| C
    C -->|7. Valida code e rilascia Access Token (+ optional Refresh Token)| B
    B -->|8. Usa Access Token per chiamare le API protette| D[Resource Server]
    D -->|9. Richiede introspezione dell'Access Token| E[Introspection Endpoint<br/>(Authorization Server)]
    E -->|10. Risponde con stato e metadati del token| D
    D -->|11. Restituisce risorsa protetta se token valido, altrimenti errore| B
```
