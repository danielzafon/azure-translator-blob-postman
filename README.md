# Azure AI Translator + Blob Storage: Postman collection

🇬🇧 **English** · 🇪🇸 [Español](README.es.md)

Postman collection to translate documents with **Azure AI Translator**, using **Azure Blob Storage** containers as source and target. It accompanies the blog article (in Spanish): [Azure AI Translator y Blob Storage con Postman](https://danielzafon.github.io/mi-bc-copilot-blog/posts/azure-ai-translator-blob-storage-postman/).

> This collection **contains no keys or signed URLs**. Everything is parameterized with variables that you fill in your own Postman environment.

## Contents

```
collection/   Azure-Translator-Blob.postman_collection.json
environment/  Azure-Translator-Blob.postman_environment.template.json
```

## Included requests

| # | Request | Purpose |
|---|---------|---------|
| 1 | Translate text (synchronous) | Quick check of key and region (optional) |
| 2 | Blob: upload document (source) | Uploads a file to the source container |
| 3 | Blob: list documents (source) | Checks what is in the source |
| 4 | Blob: delete document (source) | Test cleanup |
| 5 | Blob: list documents (target) | Reviews the translated documents |
| 6 | Blob: delete document (target) | Test cleanup |
| 7 | Translator: start document translation | Creates the batch job |
| 8 | Translator: get job status | Tracks progress using `job_id` |

## Requirements

- An **Azure AI Translator** resource (key, region and endpoint).
- A **storage account** with two containers: source and target.
- A **SAS** for each container:
  - Source: permissions `r`, `l` (add `c`, `w` if you upload from Postman and `d` if you want to delete).
  - Target: permissions `w`, `l` (add `d` if you want to delete).

## Getting started

1. Import the collection into Postman (*Import* > select the file in `collection/`).
2. Import the template in `environment/` and **duplicate** it under another name.
3. Fill in the variables in your copy:

   | Variable | Value |
   |----------|-------|
   | `translator_key` | Translator resource key *(secret)* |
   | `translator_region` | Resource region, e.g. `northeurope` |
   | `translator_endpoint` | `https://<your-resource>.cognitiveservices.azure.com` |
   | `storage_account` | Storage account name |
   | `container_origen` / `container_destino` | Source / target container names |
   | `blob_name` | Document name, e.g. `manual.pdf` |
   | `token_origen` / `token_destino` | SAS string (`sv=...&sig=...`) for each container *(secret)* |
   | `source_container_sas_url` / `target_container_sas_url` | Full container URL including its SAS *(secret)* |
   | `target_language` | Target language, e.g. `en` |
   | `job_id` | Filled in with the identifier returned by request 7 |

4. Select your environment and run in this order: **2** (upload) → **7** (start) → **8** (status) → **5** (check the target).

## Notes

- One start request translates into **one language**; send another request for each additional language.
- An overall `Succeeded` status does not guarantee that every document was translated correctly: check the per-document detail.
- Request 7 uses `/translator/document/batches` and request 8 uses `/translator/text/batch/v1.1/batches/{id}`. If your resource answers with a different API version, adjust the URL of request 8.

## Security

- **Never commit** an environment with real values. This repo's `.gitignore` prevents it.
- If you export the collection from Postman, check that it does not include initial or current variable values.
- Use SAS tokens with the shortest expiry and the minimum permissions, preferably backed by a *stored access policy* so you can revoke them.
- If a key is ever published by mistake, **regenerate it** immediately: deleting the commit is not enough.

## License

MIT.
