```mermaid
flowchart TD
  A[Utente accede a un service provider] --> B{Sessione SP ancora valida}
  B -->|Si| C[Accesso diretto al service provider]
  B -->|No| D[SP reindirizza verso IdP Shibboleth]

  D --> E{Cookie di sessione IdP presente}
  E -->|No| H[IdP vede nuova sessione utente]
  E -->|Si| F{Sessione IdP entro idp.session.timeout}

  F -->|No| H
  F -->|Si| G{Risultato di autenticazione entro idp.authn.defaultTimeout}

  G -->|No| H
  G -->|Si| I{Risultato di autenticazione entro idp.authn.defaultLifetime}

  I -->|No| H
  I -->|Si| L[IdP riusa autenticazione esistente nessuna delega Entra ID]

  L --> M[IdP emette risposta verso SP]
  M --> N[SP crea nuova sessione SP e concede accesso]

  H --> O[IdP avvia delega verso Entra ID con forceAuthn attivo]
  O --> P[Entra ID chiede login all utente]
  P --> Q[Utente completa login su Entra ID]
  Q --> R[IdP crea nuova sessione IdP e nuovo risultato di autenticazione]
  R --> M
