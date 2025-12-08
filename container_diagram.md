                   ┌──────────────────────────┐
                   │      Your Browser        │
                   │  https://host            │
                   └─────────────┬────────────┘
                                 │
                                 ▼
                       ┌─────────┴──────────┐
                       │   nginx (frontend) │
                       │   listens on 443   │
                       └─────────┬──────────┘
                                 │
                 (network: guac_frontend)
                                 │
                                 ▼
                       ┌─────────┴──────────┐
                       │     guacamole      │
                       │    web backend     │
                       └─────────┬──────────┘
                                 │
                (network: guac_backend)
                     ┌──────────┴──────────┐
                     │                     │
                     ▼                     ▼
        ┌────────────┴───────────┐   ┌─────┴──────────────┐
        │        guacd           │   │     postgres       │
        │ protocol proxy (RDP/   │   │  guacamole_db      │
        │ VNC/SSH)               │   │  schema via initdb │
        └────────────────────────┘   └────────────────────┘

Shared volumes on host:
- `./record      <-> /record` (guacd, guacamole)
- `./drive       <-> /drive` (guacd)
- `./guac_initdb <-> /docker-entrypoint-initdb.d` (postgres init SQL)
- `./data        <-> /var/lib/postgresql/data` (postgres data)
- `./nginx/templates -> /etc/nginx/templates`
- `./nginx/ssl/*     -> /etc/nginx/ssl/*`