```mermaid
sequenceDiagram
    autonumber

    participant HAProxy as HAProxy (external-check)
    participant Script as ECP Check Script
    participant SP as Service Provider (URL protetto)
    participant IdP as IdP ECP Endpoint (SOAP/BASIC)
    participant ACS as SP ACS (responseConsumerURL)
    participant Mail as SMTP
    participant State as StateFS (anti-spam)

    HAProxy->>Script: Avvio check
    Note over Script: Pre-flight: args, pass-file, TZ=Europe/Rome, tmp, timeouts

    alt Pre-flight KO
        Script-->>HAProxy: exit 1 (errore locale)
    else Pre-flight OK
        Script->>SP: GET risorsa protetta (Accept: PAOS, header PAOS)
        SP-->>Script: Risposta

        alt Content-Type = application/vnd.paos+xml
            Note over Script: Estrai RelayState e hint ACS
            Script->>IdP: POST SOAP/ECP + BASIC (inoltro body PAOS)
            opt Target nodo specifico
                Note over Script: --resolve/SNI verso HAPROXY_SERVER_ADDR:ECP_PORT
            end
            IdP-->>Script: Risposta SOAP (HTTP 2xx + CT)

            alt IdP/Rete KO (HTTP/CT errato o timeout)
                Script->>Mail: Notifica failure IdP/Rete
                Script->>State: set flag (TTL opzionale)
                Script-->>HAProxy: exit 77 (mark DOWN)
            else IdP OK
                Note over Script: Determina ACS_URL (dalla risposta o hint); reinietta RelayState se mancante
                Script->>ACS: POST PAOS alla ACS
                ACS-->>Script: HTTP 200/302 + header

                alt Set-Cookie _shibsession_ presente
                    Script->>State: clear flag (global/per-node)
                    Script-->>HAProxy: exit 0 (OK)
                else Cookie assente o codice inatteso
                    Script->>Mail: Notifica failure SP
                    Script->>State: set flag SP (scope global/per-node)
                    Script-->>HAProxy: exit 0 (nodo resta UP)
                end
            end

        else Content-Type ≠ PAOS / errore SP
            Script->>Mail: Notifica failure SP
            Script->>State: set flag SP (scope global/per-node)
            Script-->>HAProxy: exit 0 (nodo resta UP)
        end
    end
