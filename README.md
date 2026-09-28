# Flask

Repositorio de una aplicacion Flask que expone informacion de dispositivos a partir de un archivo JSON.

## Estado del repositorio

Ramas observadas al preparar esta documentacion:

| Rama | Estado observado | Uso |
| --- | --- | --- |
| `main` | Rama local inicial, alineada con `origin/main` | Rama principal |
| `origin/feature` | Rama remota existente, alineada con el commit inicial | Rama de funcionalidad solicitada para publicar esta version |

La rama local `feature` debe seguir a `origin/feature` para los cambios documentados aqui. El estado de ramas puede cambiar despues de esta revision.

## Requisito de datos

Al importar `app.py`, la aplicacion abre `API.json` desde el directorio de trabajo actual y carga su contenido en memoria. Ese archivo no esta presente en el repositorio observado, por lo que la aplicacion no puede arrancar hasta que se proporcione. Las respuestas de exito dependen de las claves y los valores incluidos en ese archivo.

## Rutas HTTP

Flask registra las dos rutas siguientes. Como no se especifica `methods` en los decoradores, ambas aceptan `GET`.

| Metodo | URL | Comportamiento |
| --- | --- | --- |
| `GET` | `/` | Devuelve una pagina HTML sencilla con el titulo "Hola mundo" y un enlace a `/json/3D:RF:09:7F::`. |
| `GET` | `/json/<mac>` | Busca `mac` como clave de primer nivel en `API.json`. Si no existe, devuelve `{"error":"Dispositivo no encontrado"}` con estado `404`. Si existe, imprime en la salida del servidor `Name`, `Protocolos`, `status` y `VLANs`, y devuelve el objeto del dispositivo como JSON con estado `200`. |

`<mac>` es un segmento de la ruta, no un parametro de query string. Por ejemplo, `GET /json/3D:RF:09:7F::` busca la clave literal `3D:RF:09:7F::`. El contenido exacto de una respuesta exitosa lo determina la entrada correspondiente en `API.json`.

## Mapa de rutas

```mermaid
flowchart TD
	Client[Cliente HTTP] -->|GET /| Home["/ - HTML: Hola mundo"]
	Home -->|Enlace: GET /json/3D:RF:09:7F::| Lookup["/json/{mac} - buscar en API.json"]
	Client -->|GET /json/{mac}| Lookup
	Lookup --> Exists{"¿Existe la clave mac?"}
	Exists -->|Si| Found["200 - objeto del dispositivo como JSON; imprime campos"]
	Exists -->|No| Missing["404 - error JSON: dispositivo no encontrado"]
```

## Ejecucion

Desde el directorio del proyecto, con `API.json` disponible:

```bash
python app.py
```

El servidor de desarrollo de Flask se inicia en la configuracion predeterminada y `debug` esta activado en `app.py`.
