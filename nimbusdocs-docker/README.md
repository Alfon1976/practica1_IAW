# Práctica 1: Servidores, Proxies y Certificados - NimbusDocs

## I. Diseño y Requisitos
* **Dominios ficticios:** Se han seleccionado `alpha.nimbus.lan` y `beta.nimbus.lan` para identificar de forma única las marcas del portal documental sin reutilizar valores genéricos de clase (`sitioa.iaw.local`).
* **MPM de Apache:** Se ha elegido **`mpm_event`** debido a su arquitectura asíncrona basada en hilos dedicados para gestión de conexiones concurrentes, superior en eficiencia al modelo clásico `prefork` para cargas web intensas.

## II. Fase 1 y 2: Arquitectura y Seguridad
* **Terminación TLS:** Implementada en Nginx utilizando una curva elíptica ECDSA (`secp384r1`) con protocolos restringidos exclusivamente a TLS 1.2 y TLS 1.3. Redirección HTTP a HTTPS mediante código `301 Moved Permanently`.
* **Rate Limiting:** Configurado con `rate=3r/s` y `burst=6` para tolerar picos legítimos de usuarios recargando pestañas (hasta 6 req/s) bloqueando ataques de scraping masivos (>20 req/s con códigos `503`).

## III. Fase 3: Diagnóstico Provocado
| Síntoma observado | Hipótesis inicial | Comando de diagnóstico | Causa real y solución |
| :--- | :--- | :--- | :--- |
| Error 404/Default al consultar la marca Beta | Error en las rutas DocumentRoot | `docker exec -it nimbus-apache apache2ctl -S` | Omisión de la cabecera `Host` en el reenvío de Nginx. Solución: añadir `proxy_set_header Host $host;`. |
| Peticiones legítimas bloqueadas con código 503 | Fallo en el certificado SSL | `docker logs nimbus-nginx` | Zona de `limit_req` demasiado estricta o burst insuficiente. Solución: ajustar el parámetro `burst=6`. |



## /etc/host

Para que el etc/host funcione no me deja hacelo por que no tengo permisos, con curl si funciona 