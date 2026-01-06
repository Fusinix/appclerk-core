![AppClerk Core Banner](https://appclerk.dev/images/logo-main.png)
<p align="center">
  <img src="./assets/appclerk-core.png" alt="AppClerk Core Banner" width="400" />
</p>

# appclerk-core

Core SDK for AppClerk - Platform-agnostic compliance infrastructure.

## Installation

```bash
npm install appclerk-core
# or
yarn add appclerk-core
# or
pnpm add appclerk-core
```

## Usage

```typescript
import { init } from "appclerk-core";

// Initialize SDK
const appclerk = init({
  apiKey: process.env.APPCLERK_API_KEY,
  projectId: process.env.APPCLERK_PROJECT_ID,
});

// Get document markdown
const privacyPolicy = await appclerk.getDocumentMarkdown("privacy-policy");
console.log(privacyPolicy.content);

// Get document metadata
const metadata = await appclerk.getDocumentMetadata("privacy-policy");
console.log(metadata.hostedUrl);

// Get compliance status
const compliance = await appclerk.getComplianceStatus();
console.log(compliance.score);

// Get hosted URLs
const privacyUrl = await appclerk.getPrivacyPolicyUrl();
const termsUrl = await appclerk.getTermsUrl();
```

## API Reference

### `init(config: AppClerkConfig): AppClerk`

Initialize the SDK with your API key and project ID.

**Config:**
- `apiKey` (string, required): Your AppClerk API key
- `projectId` (string, required): Your AppClerk project ID
- `apiUrl` (string, optional): Custom API URL (defaults to production)
- `debug` (boolean, optional): Enable debug logging
- `cacheTtl` (number, optional): Cache TTL in milliseconds (default: 5 minutes)
- `maxRetries` (number, optional): Maximum retry attempts (default: 3)

### `getDocumentMarkdown(documentType: DocumentType): Promise<DocumentContent>`

Fetch markdown content for a document.

**Returns:**
- `content`: Markdown content
- `lastUpdated`: ISO 8601 timestamp
- `version`: Document version number
- `title`: Document title
- `hostedUrl`: Public hosted URL

### `getDocumentMetadata(documentType: DocumentType): Promise<DocumentMetadata>`

Fetch document metadata without content.

**Returns:**
- `title`: Document title
- `lastUpdated`: ISO 8601 timestamp
- `version`: Document version number
- `hostedUrl`: Public hosted URL
- `status`: Document status

### `getComplianceStatus(): Promise<ComplianceStatus>`

Get overall compliance status for the project.

**Returns:**
- `score`: Overall compliance score (0-100)
- `status`: Compliance status
- `verificationStatus`: Verification status
- `issues`: Array of compliance issues
- `documents`: Array of document compliance statuses
- `lastChecked`: ISO 8601 timestamp

### `getPrivacyPolicyUrl(): Promise<string>`

Get hosted URL for privacy policy.

### `getTermsUrl(): Promise<string>`

Get hosted URL for terms of service.

### `clearCache(): void`

Clear all cached data.

## Error Handling

The SDK throws custom error classes:

- `AuthenticationError`: Invalid API key (401)
- `AuthorizationError`: Access denied (403)
- `NotFoundError`: Resource not found (404)
- `RateLimitError`: Rate limit exceeded (429)
- `ValidationError`: Invalid request (400)
- `NetworkError`: Network request failed
- `ServerError`: Server error (500+)

```typescript
import { NotFoundError, RateLimitError } from "appclerk-core";

try {
  const doc = await appclerk.getDocumentMarkdown("privacy-policy");
} catch (error) {
  if (error instanceof NotFoundError) {
    console.error("Document not found");
  } else if (error instanceof RateLimitError) {
    console.error("Rate limit exceeded", error.retryAfter);
  }
}
```

## Caching

The SDK includes built-in in-memory caching:

- Document content: 5 minutes (configurable)
- Document metadata: 5 minutes (configurable)
- Compliance status: 1 minute

Cache is automatically cleaned up and can be cleared with `clearCache()`.

## Retry Logic

The SDK automatically retries failed requests:

- Network errors: Up to 3 retries with exponential backoff
- Rate limit errors: Retries after the `Retry-After` header delay

## License

MIT

