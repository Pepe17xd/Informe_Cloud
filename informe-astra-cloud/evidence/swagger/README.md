# Evidencia de documentación de APIs

Verificación remota realizada el **2026-09-23** contra los puntos de entrada HTTPS desplegados. Las consultas no emplearon autenticación ni incluyeron cabeceras `Authorization`.

| Servicio | URL base | Endpoint de documentación | Origen | Resultado y evidencia |
|---|---|---|---|---|
| Identity | `https://no33d1m2aj.execute-api.us-east-1.amazonaws.com` | `/docs` y `/openapi.json` | Remoto | HTTP 200. Esquema descargado en `identity-openapi.json`; interfaz en `identity-swagger.png`. |
| Catalog | `https://g9n6w2p890.execute-api.us-east-1.amazonaws.com` | `/swagger-ui.html` y `/v3/api-docs` | Remoto | HTTP 200. Esquema descargado en `catalog-openapi.json`. La interfaz respondió, pero no terminó de cargar en la captura automatizada; el OpenAPI remoto es la evidencia equivalente. |
| Community | `https://4bk3u12ajd.execute-api.us-east-1.amazonaws.com` | `/docs` y `/openapi.json` | Remoto | HTTP 200. Esquema descargado en `community-openapi.json`. La interfaz respondió, pero no terminó de cargar en la captura automatizada; el OpenAPI remoto es la evidencia equivalente. |
| Interaction | `https://crqqwz4fi5.execute-api.us-east-1.amazonaws.com` | `/docs` | Remoto | HTTP 200. Interfaz Swagger UI capturada en `interaction-swagger.png`; HTML fuente conservado en `interaction-swagger.html`. |
| Cinema Session | `https://c28yw8agnl.execute-api.us-east-1.amazonaws.com` | `/docs` y `/openapi.json` | Remoto | HTTP 200. Esquema descargado en `cinema-openapi.json`; interfaz en `cinema-swagger.png`. |
| Analytics | `https://hy19qpnr0d.execute-api.us-east-1.amazonaws.com` | `/docs` y `/openapi.json` | Remoto | HTTP 200. Esquema descargado en `analytics-openapi.json`; interfaz en `analytics-swagger.png`. |

Los archivos OpenAPI corresponden a las respuestas recibidas desde los endpoints remotos el día de la verificación. No contienen tokens, cookies, credenciales ni cabeceras de autenticación.
