# NimbusDocs — Portal Documental Seguro Multi-Marca

## Práctica 1 — Fase 1

Curso: 2026/2027

Tecnología utilizada:

* Docker
* Docker Compose
* Apache HTTP Server
* Virtual Hosts
* MPM Event

---

## 1. Objetivo

NimbusDocs es una empresa ficticia que dispone de una infraestructura documental para dos marcas diferentes.

En esta primera fase se ha implementado un único servidor Apache dentro de un contenedor Docker.

El servidor utiliza Virtual Hosts basados en nombre para separar las dos marcas:

* apachedocs.local
* nginxdocs.local

Cada marca dispone de su propio DocumentRoot, ErrorLog y CustomLog.

---

## 2. Dominios elegidos

### apachedocs.local

Representa la marca pública de NimbusDocs.

El nombre combina la referencia a Apache con el concepto documental de la empresa.

### nginxdocs.local

Representa la marca interna de NimbusDocs.

El nombre hace referencia al futuro uso de Nginx en la segunda fase del proyecto, donde se incorporará un proxy inverso delante de Apache.

En esta primera fase ambos dominios son atendidos directamente por Apache.

---

## 3. MPM utilizado

Se ha utilizado el MPM Event de Apache.

El MPM Event está orientado a manejar múltiples conexiones de forma eficiente y permite una buena gestión de conexiones persistentes.

Para un servidor documental que posteriormente tendrá un proxy inverso delante, resulta una elección adecuada porque permite mantener un modelo eficiente de gestión de conexiones.

---

## 4. Arquitectura Docker

La práctica utiliza un único contenedor Apache.

La estructura es:

```
Docker Compose
      |
      v
nimbusdocs-apache
      |
      +---- apachedocs.local
      |
      +---- nginxdocs.local
```

El puerto 8080 del equipo anfitrión se conecta con el puerto 80 del contenedor Apache.

---

## 5. Virtual Host apachedocs.local

El DocumentRoot utilizado es:

```
/var/www/apachedocs
```

Dispone de:

* ErrorLog propio.
* CustomLog propio.
* DirectoryIndex.
* Protección frente al listado de directorios.

---

## 6. Virtual Host nginxdocs.local

El DocumentRoot utilizado es:

```
/var/www/nginxdocs
```

Este dominio representa la zona interna de NimbusDocs.

También dispone de:

* ErrorLog propio.
* CustomLog propio.
* DirectoryIndex.
* Desactivación del listado de directorios.

---

## 7. Protección del recurso sensible

Se ha creado un fichero sensible simulado denominado:

```
configuracion-nimbus.conf
```

No se ha utilizado wp-config.php porque el objetivo de la práctica exige utilizar nombres propios.

El acceso al fichero se bloquea mediante:

```
<Files "configuracion-nimbus.conf">
    Require all denied
</Files>
```

La petición devuelve un error HTTP 403 Forbidden.

---

## 8. Protección frente al listado de directorios

Se ha utilizado:

```
Options -Indexes
```

en los DocumentRoot de los Virtual Hosts.

De esta forma Apache no muestra automáticamente el contenido de los directorios cuando no existe un documento índice.

---

## 9. Dominios desconocidos

Se ha decidido que cualquier petición cuyo Host no coincida con:

```
apachedocs.local
```

o:

```
nginxdocs.local
```

debe recibir una respuesta HTTP 404 Not Found.

Esto evita que un dominio desconocido pueda terminar mostrando accidentalmente el contenido de una de las marcas.

---

## 10. Pruebas realizadas

### Virtual Host público

```
curl -H "Host: apachedocs.local" http://localhost:8080
```

Resultado esperado:

```
Página ApacheDocs
```

### Virtual Host interno

```
curl -H "Host: nginxdocs.local" http://localhost:8080
```

Resultado esperado:

```
Página NginxDocs
```

### Fichero sensible

```
curl -i -H "Host: nginxdocs.local" http://localhost:8080/documentos/configuracion-nimbus.conf
```

Resultado esperado:

```
HTTP 403 Forbidden
```

### Dominio desconocido

```
curl -i -H "Host: dominio-inventado.local" http://localhost:8080
```

Resultado esperado:

```
HTTP 404 Not Found
```

### Validación de Apache

```
docker exec -it nimbusdocs-apache apachectl -t
```

Resultado:

```
Syntax OK
```

### Comprobación de Virtual Hosts

```
docker exec -it nimbusdocs-apache apachectl -S
```

Se comprueba la existencia de los dos Virtual Hosts configurados.

---

## 11. Conclusión

La Fase 1 permite disponer de un único Apache capaz de alojar dos marcas independientes mediante Virtual Hosts basados en nombre.

Cada marca dispone de su propio contenido y registros, mientras que la zona interna incorpora protección adicional para un recurso sensible y desactivación del listado de directorios.

La infraestructura queda preparada para la incorporación posterior de Nginx como proxy inverso y terminador TLS en la Fase 2.
