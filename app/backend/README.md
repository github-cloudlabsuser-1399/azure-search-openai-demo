# Backend Services (`app/backend`)

This directory contains the backend services for the RAG Chat App, built with [Quart](https://quart.palletsprojects.com/) (an async Python web framework). The backend exposes RESTful APIs for chat, document ingestion, authentication, and integration with Azure services.

---

## Table of Contents

- [Overview](#overview)
- [Directory Structure](#directory-structure)
- [Key Components](#key-components)
- [API Endpoints](#api-endpoints)
- [Configuration & Environment Variables](#configuration--environment-variables)
- [Development](#development)
- [Testing](#testing)
- [Extending the Backend](#extending-the-backend)
- [References](#references)

---

## Overview

The backend is responsible for:

- Serving chat and ask APIs for the frontend.
- Handling user authentication and authorization (optional, via Azure AD).
- Managing document ingestion and indexing into Azure AI Search.
- Integrating with Azure OpenAI for LLM completions.
- Providing endpoints for file upload, deletion, and listing.
- Supporting custom RAG approaches and prompt engineering.

---

## Directory Structure

```
app/backend/
├── app.py                # Main Quart app and API routes
├── prepdocs.py           # Document ingestion and indexing logic
├── approaches/           # RAG approaches (chat, ask, etc.)
├── auth/                 # Authentication and authorization helpers
├── static/               # Static files (if any)
├── templates/            # Jinja2 templates (if any)
└── ...                   # Other utility modules
```

---

## Key Components

### [`app.py`](app.py)

- **Entry point** for the backend server.
- Registers API routes for chat, ask, file upload, file deletion, and listing.
- Handles authentication using decorators.
- Initializes and tears down Azure/OpenAI clients.

### [`prepdocs.py`](prepdocs.py)

- Handles **document ingestion** from local files or Azure Data Lake Storage Gen2.
- Processes, splits, and indexes documents into Azure AI Search.
- Supports configuration via environment variables and CLI arguments.

### [`approaches/`](approaches/)

- Contains different **retrieval-augmented generation (RAG) approaches**.
- Each approach is a Python class implementing a specific chat or ask strategy.
- Prompts and system messages can be customized here.

### [`auth/`](auth/)

- Implements authentication and authorization logic.
- Integrates with Microsoft Entra ID (Azure AD) for secure access.

---

## API Endpoints

| Endpoint                | Method | Auth Required | Description                                 |
|-------------------------|--------|---------------|---------------------------------------------|
| `/chat`                 | POST   | Yes           | Chat with the LLM using RAG                 |
| `/ask`                  | POST   | Yes           | Ask a question (non-chat)                   |
| `/upload`               | POST   | Yes           | Upload a document for ingestion             |
| `/delete_uploaded`      | POST   | Yes           | Delete an uploaded document                 |
| `/list_uploaded`        | GET    | Yes           | List all uploaded documents                 |
| `/config`               | GET    | No            | Get backend configuration                   |
| `/health`               | GET    | No            | Health check endpoint                       |

> **Note:** All endpoints requiring authentication use the `@authenticated` decorator.

---

## Configuration & Environment Variables

The backend is configured via environment variables. Key variables include:

- `OPENAI_HOST`: Host for OpenAI API (Azure or local).
- `OPENAI_API_KEY` / `AZURE_OPENAI_API_KEY_OVERRIDE`: API key for OpenAI.
- `AZURE_SEARCH_SERVICE`: Azure Cognitive Search service name.
- `AZURE_SEARCH_INDEX`: Name of the search index.
- `AZURE_STORAGE_ACCOUNT`: Azure Storage account for file uploads.
- `AZURE_ADLS_GEN2_STORAGE_ACCOUNT`: Data Lake Storage account.
- `AZURE_ADLS_GEN2_FILESYSTEM`: Data Lake filesystem name.
- `AZURE_ADLS_GEN2_FILESYSTEM_PATH`: Path within the filesystem.
- `AZURE_OPENAI_EMB_DIMENSIONS`: Embedding dimensions (default: 1536).
- `AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, `AZURE_CLIENT_SECRET`: For Azure authentication.

See [docs/login_and_acl.md](../../docs/login_and_acl.md) and [docs/localdev.md](../../docs/localdev.md) for more details.

---

## Development

### Running Locally

1. Ensure you have run `azd up` at least once to provision Azure resources.
2. Start the backend server:

   **Windows:**
   ```sh
   ./app/start.ps1
   ```

   **Linux/Mac:**
   ```sh
   ./app/start.sh
   ```

   **VS Code:** Run the "VS Code Task: Start App" task.

3. The backend will start on the default port (e.g., 50505).

### Hot Reloading

- The backend supports hot reloading when started via the provided scripts.

---

## Testing

- Unit and integration tests are located in the [`tests/`](../../tests/) directory.
- Run tests with:
  ```sh
  pytest
  ```

---

## Extending the Backend

- **Add new RAG approaches:** Implement a new class in `approaches/` and register it in `app.py`.
- **Customize prompts:** Edit the `.prompty` files in `approaches/prompts/`.
- **Add new endpoints:** Define new routes in `app.py` using Quart's route decorators.

---

## References

- [Quart Documentation](https://quart.palletsprojects.com/)
- [Azure OpenAI Service](https://learn.microsoft.com/azure/cognitive-services/openai/)
- [Azure Cognitive Search](https://learn.microsoft.com/azure/search/)
- [docs/login_and_acl.md](../../docs/login_and_acl.md)
- [docs/localdev.md](../../docs/localdev.md)
- [docs/customization.md](../../docs/customization.md)

For further details, see the [main project README](../../README.md).