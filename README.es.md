# Azure AI Translator + Blob Storage: colección de Postman

🇬🇧 [English](README.md) · 🇪🇸 **Español**

Colección de Postman para traducir documentos con **Azure AI Translator** usando contenedores de **Azure Blob Storage** como origen y destino. Acompaña al artículo del blog: [Azure AI Translator y Blob Storage con Postman](https://danielzafon.github.io/mi-bc-copilot-blog/posts/azure-ai-translator-blob-storage-postman/).

> Esta colección **no contiene claves ni URLs con firma**. Todo se parametriza con variables que rellenas en tu propio entorno de Postman.

## Contenido

```
collection/   Azure-Translator-Blob.postman_collection.json
environment/  Azure-Translator-Blob.postman_environment.template.json
```

## Peticiones incluidas

| # | Petición | Para qué sirve |
|---|----------|----------------|
| 1 | Traducir texto (síncrono) | Prueba rápida de la clave y la región (opcional) |
| 2 | Blob: subir documento (origen) | Sube un fichero al contenedor de origen |
| 3 | Blob: listar documentos (origen) | Comprueba qué hay en el origen |
| 4 | Blob: eliminar documento (origen) | Limpieza de pruebas |
| 5 | Blob: listar documentos (destino) | Revisa los documentos traducidos |
| 6 | Blob: eliminar documento (destino) | Limpieza de pruebas |
| 7 | Translator: lanzar traducción de documentos | Crea el trabajo por lotes |
| 8 | Translator: consultar estado del trabajo | Sigue el progreso con `job_id` |

## Requisitos

- Un recurso de **Azure AI Translator** (clave, región y endpoint).
- Una **cuenta de almacenamiento** con dos contenedores: origen y destino.
- Un **SAS** para cada contenedor:
  - Origen: permisos `r`, `l` (añade `c`, `w` si subes desde Postman y `d` si quieres borrar).
  - Destino: permisos `w`, `l` (añade `d` si quieres borrar).

## Puesta en marcha

1. Importa la colección en Postman (*Import* > selecciona el fichero de `collection/`).
2. Importa la plantilla de `environment/` y **duplícala** con otro nombre.
3. Rellena las variables en tu copia:

   | Variable | Contenido |
   |----------|-----------|
   | `translator_key` | Clave del recurso Translator *(secreta)* |
   | `translator_region` | Región del recurso, por ejemplo `northeurope` |
   | `translator_endpoint` | `https://<tu-recurso>.cognitiveservices.azure.com` |
   | `storage_account` | Nombre de la cuenta de almacenamiento |
   | `container_origen` / `container_destino` | Nombres de los contenedores |
   | `blob_name` | Nombre del documento, por ejemplo `manual.pdf` |
   | `token_origen` / `token_destino` | Cadena SAS (`sv=...&sig=...`) de cada contenedor *(secreta)* |
   | `source_container_sas_url` / `target_container_sas_url` | URL completa del contenedor con su SAS *(secreta)* |
   | `target_language` | Idioma de destino, por ejemplo `en` |
   | `job_id` | Se rellena con el identificador que devuelve la petición 7 |

4. Selecciona tu entorno y ejecuta en este orden: **2** (subir) → **7** (lanzar) → **8** (estado) → **5** (comprobar el destino).

## Notas

- Una petición de lanzamiento traduce a **un idioma**; lanza otra por cada idioma adicional.
- Un estado global `Succeeded` no garantiza que todos los documentos se hayan traducido bien: revisa el detalle por documento.
- La petición 7 usa `/translator/document/batches` y la 8 usa `/translator/text/batch/v1.1/batches/{id}`. Si tu recurso responde con otra versión del API, ajusta la URL de la 8.

## Seguridad

- **No subas nunca** un entorno con valores reales. El `.gitignore` de este repo lo evita.
- Si exportas la colección desde Postman, revisa que no incluya valores iniciales o actuales de variables.
- Usa SAS con la mínima caducidad y los mínimos permisos, preferiblemente con una *stored access policy* para poder revocarlos.
- Si alguna clave llegó a publicarse por error, **regenérala** de inmediato: borrar el commit no basta.

## Licencia

MIT.
