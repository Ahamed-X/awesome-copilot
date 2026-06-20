---
applyTo: "package.json, **/*.ts, **/*.js"
description: "Instructions for deploying Genkit flows to Google Cloud Run as HTTPS endpoints."
---

# Deploy Genkit flows with Cloud Run

You can deploy Genkit flows as HTTPS endpoints using Cloud Run. Cloud Run has several deployment options, including container-based deployment; these instructions focus on deploying flows directly from code.

## Prerequisites

- Google Cloud CLI installed.
- Familiarity with Genkit flows.
- A Google Cloud project with billing enabled.

## 1. Project Setup

Configure the Google Cloud CLI to use your project:

```bash
gcloud init
```

## 2. Prepare Node.js Project

### Update package.json

Add `start` and `build` scripts to your `package.json`:

```json
"scripts": {
  "start": "node lib/index.js",
  "build": "tsc"
},
```

### Configure and Start Flow Server

In your entry point file (e.g., `index.ts`), use `startFlowServer` from `@genkit-ai/express`:

```ts
import { startFlowServer } from '@genkit-ai/express';

startFlowServer({
  flows: [menuSuggestionFlow],
});
```

**Optional parameters:**
- `port`: Network port (defaults to `PORT` env var, or 3400).
- `cors`: CORS policy for web application access.
- `pathPrefix`: Optional path prefix for endpoints.
- `jsonParserOptions`: Options for Express's JSON body parser.

## 3. Authorization

### Authorization Options

- **Cloud IAM-based authorization**: Use Google Cloud's native IAM to gate access.
- **In-code authorization policy**: Use the `authPolicy` parameter in the flow definition or `expressHandler`.

Example of in-code authorization:

```ts
app.post(
  '/simpleFlow',
  expressHandler(simpleFlow, {
    contextProvider: async (request) => {
      const user = await verifyAuthToken(request.headers['authorization']);
      if (!user) {
        throw new Error('not authorized');
      }
      return { auth: { user } };
    },
  }),
);
```

## 4. API Credentials

Deployed flows need credentials to access model APIs.

### Gemini (Google AI)

1. [Generate an API key](https://aistudio.google.com/app/apikey).
2. Store the key in [Secret Manager](https://console.cloud.google.com/security/secret-manager).
3. Grant the **Secret Manager Secret Accessor** role to the default compute service account.

### Gemini (Vertex AI)

1. Enable the Vertex AI API.
2. Grant the **Vertex AI User** role to the default compute service account.

## 5. Deployment

Deploy using the `gcloud` tool:

**Gemini (Google AI):**
```bash
gcloud run deploy --update-secrets=GEMINI_API_KEY=<your-secret-name>:latest
```

**Gemini (Vertex AI):**
```bash
gcloud run deploy
```

**Unauthenticated Invocations:**
- Answer `Y` if using in-code authorization.
- Answer `N` if requiring IAM credentials.

## 6. Testing the Deployed Flow

Test with `curl` using the service URL:

```bash
curl -X POST https://<service-url>/menuSuggestionFlow \
  -H "Authorization: Bearer $(gcloud auth print-identity-token)" \
  -H "Content-Type: application/json" -d '{"data": "banana"}'
```
