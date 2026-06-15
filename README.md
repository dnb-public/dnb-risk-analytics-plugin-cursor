# D&B Risk Analytics MCP Server — Quick Start Guide

## Welcome

Thank you for choosing Dun & Bradstreet! This document will help you set up and authenticate into the D&B Risk Analytics MCP server.

For inquiries, please contact CursorLeads@dnb.com.

---

## What is the D&B Risk Analytics MCP Server?

The D&B Risk Analytics MCP server leverages the standardized MCP client-server architecture to provide AI and automation applications with secure, scalable access to trusted, comprehensive business data in D&B Risk Analytics. This enables intelligent systems—such as AI assistants, agents, and enterprise applications—to seamlessly integrate with the D&B Risk Analytics platform for enhanced decision-making and contextual insights.

Through this architecture, D&B Risk Analytics capabilities become an intelligent extension of your development environment or AI application, delivering real-time access to risk insights and compliance data. By standardizing connectivity, the MCP server helps ensure interoperability, security, and performance across diverse AI ecosystems.

**The D&B Risk Analytics MCP server enables:**

- Agent as a Tool discovery (**RiskAnalyticsAgent** acts as the orchestrator that routes to the correct internal tool based on the natural language query)
- Retrieval of domain-specific expert insights
- Seamless agent-to-API interaction without customers writing endpoint-level integration logic

**The D&B Risk Analytics MCP server exposes a Risk Analytics Agent as a Tool capable of executing both read and write operations across a wide variety of use cases:**

- Agents able to access detailed company insights, including firmographic data, financial risk indicators, predictive risk indicators and much more, all powered by D&B's comprehensive data.
- Agents able to provide concise summaries of thousands of data points, for more efficient risk management and analysis.
- Agents able to provide deep insight on beneficial ownership structures and corporate linkage family trees.
- Agents able to perform actionable tasks, including: adding entities to portfolio, adding and updating users, initiating screenings, assigning custom fields, assigning tags, and more.

---

## Prerequisites for Access

**Integration API Credentials:** Generate the Client ID and Client Secret from your Risk Analytics workspace, located in the **Admin → Integrations → API** page.

---

## How to Connect to the D&B Risk Analytics MCP Server

There are two methods that allow you to connect to the D&B Risk Analytics MCP server:

- **OAuth PKCE**
- **Client Credentials**

Both approaches are covered below.

---

## OAuth 2.1 PKCE

The D&B Risk Analytics MCP server supports the OAuth 2.1 Authentication protocol, using PKCE (Proof Key for Code Exchange) and the authorization code grant type. Below is a step-by-step guide on how this process works when attempting to connect to the MCP server.

### D&B Risk Analytics MCP Server Endpoint

https://agents.riskanalytics.dnb.com/mcp

### Invalid Token Redirect

If you send a request to the MCP server Endpoint with an invalid/missing/expired access token, the response will return **HTTP 401** and include the protected resource metadata (`resource_metadata`) in the `www-authenticate` header.

```
www-authenticate: Bearer error="invalid_token", error_description="Authentication required",
resource_metadata=https://agents.riskanalytics.dnb.com/.well-known/oauth-protected-resource/mcp
```

### Protected Resource Metadata

Use the `resource_metadata` URL to discover the D&B Risk Analytics MCP Authorization Server URL.

**Sample Response:**

```json
{
  "resource": "https://agents.riskanalytics.dnb.com/mcp",
  "authorization_servers": [
    "https://agents.riskanalytics.dnb.com/.well-known/oauth-authorization-server"
  ],
  "scopes_supported": [
    "mcp.read",
    "mcp.write"
  ],
  "bearer_methods_supported": [
    "header"
  ]
}
```

### Authorization Server Endpoints

Use the `authorization_servers` URL to discover the relevant authorization and access token endpoints.

**Sample Response:**

```json
{
  "issuer": "https://agents.riskanalytics.dnb.com",
  "authorization_endpoint": "https://agents.riskanalytics.dnb.com/authorize",
  "token_endpoint": "https://agents.riskanalytics.dnb.com/token",
  "response_types_supported": [
    "code"
  ],
  "grant_types_supported": [
    "authorization_code"
  ],
  "code_challenge_methods_supported": [
    "S256",
    "plain"
  ]
}
```

### Initiate Authorize Flow

| Method | Authorize URL |
|--------|---------------|
| GET | `https://agents.riskanalytics.dnb.com/authorize` |

Make a request to the Authorize URL with the following query parameters:

- `response_type=code`
- `client_id` — use the Client ID you generated earlier from your Risk Analytics workspace
- `redirect_uri`
- `scope`
- `state`
- `code_challenge`
- `code_challenge_method`

If the request is successful, the MCP server returns **HTTP 302**, generates an authorization code (`code`), and returns the `code` + the `state` parameter from the client request (if provided) in the location header, using the provided redirect URI.

**Sample Curl Request:**

```bash
curl -G "https://agents.riskanalytics.dnb.com/authorize" \
  --data-urlencode "response_type=code" \
  --data-urlencode "client_id=<client_id>" \
  --data-urlencode "redirect_uri=cursor://anysphere.cursor-mcp/oauth/callback" \
  --data-urlencode "state=<state>" \
  --data-urlencode "code_challenge=<challenge>" \
  --data-urlencode "code_challenge_method=S256"
```

**Sample Response** *(Redirect URI with code and state in the location header):*

```
cursor://anysphere.cursor-mcp/oauth/callback?code={authorization_code}&state={state}
```

### Token Exchange

Once you receive the authorization code from the `/authorize` response, retrieve it and pass it in the request to the `/token` endpoint. The token endpoint will generate the access token required to access tools provided by the D&B Risk Analytics MCP server.

| Method | Token URL |
|--------|-----------|
| POST | `https://agents.riskanalytics.dnb.com/token` |

**Supported content-types:**

- `application/x-www-form-urlencoded`
- `application/x-www-form-urlencoded;charset=UTF-8`

**Request elements:**

- `grant_type=authorization_code`
- `code`
- `redirect_uri`
- `code_verifier`

**Pass Client ID and Client Secret through the request body or the header:**

- `Authorization: Basic base64(client_id:client_secret)`, or
- Request body/form fields `client_id`, `client_secret`

When the client passes the `code_verifier` value, the Authorization Server will ensure that it matches the `code_challenge` sent by the client in the earlier `/authorize` request.

### PKCE Validation

- **SHA256:** If `code_challenge_method=S256`, the Authorization Server will apply a SHA-256 hash to the code verifier and match it against the `code_challenge` value. If the values match, the PKCE is verified.
- **Plain:** If `code_challenge_method=plain`, the Authorization Server will check if the `code_verifier` is equal to the `code_challenge`. If the values match, the PKCE is verified.

After the Authorization Server performs other validation checks from the request parameters and the checks are successful, it will generate the access token and refresh token in the response.

**Sample Request:**

```bash
curl -X POST "https://agents.riskanalytics.dnb.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded;charset=UTF-8" \
  -H "Authorization: Basic <base64(client_id:client_secret)>" \
  --data-urlencode "grant_type=authorization_code" \
  --data-urlencode "code=<auth_code>" \
  --data-urlencode "redirect_uri=cursor://anysphere.cursor-mcp/oauth/callback" \
  --data-urlencode "code_verifier=<original_verifier>"
```

**Sample Response:**

```json
{
  "access_token": "<jwt>",
  "token_type": "Bearer",
  "expires_in": 86400,
  "refresh_token": "<refresh>"
}
```

> **Security Notice:** Your Client ID, Client Secret, and Access Token should be treated as sensitive information, equivalent to a password. They must only be used by authorized parties.

### Use Access Token

Use the access token from the `/token` response and pass it in the `Authorization` header in the request to the `/mcp` endpoint.

```
Authorization: Bearer <access_token>
```

If the token is valid and not expired, the request will succeed and the MCP server will be connected and ready for use.

### Refresh Token Flow

Since the access token is only valid for a certain period of time, it is bound to expire. Upon expiry, use the `refresh_token` generated in the `/token` response, and use it in a new request to `/token` to generate a new, valid access token. Note that the `grant_type` value must be set to `refresh_token`.

**Request:**

```
grant_type=refresh_token
client_id=<client_id>
client_secret=<client_secret>
refresh_token=<refresh_token>
```

---

## Client Credentials

The Client Credentials method involves generating a Client ID and Client Secret in your Risk Analytics Workspace and then generating an access token using those parameters in the request to the Authorization Server Token endpoint.

### Generate Client Credentials

The process for generating the Client ID and Client Secret in Risk Analytics is mentioned [here](https://agents.riskanalytics.dnb.com).

### Generate Access Token

Pass the Client ID and Client Secret into the `/token` endpoint, to generate the access token that will then be used for authenticating against the MCP server.

**Request Parameters:**

```
grant_type=client_credentials
client_id=<client_id>
client_secret=<client_secret>
```

**Sample Response:**

```json
{
  "access_token": "<jwt>",
  "token_type": "Bearer",
  "expires_in": 86400,
  "refresh_token": "<refresh>"
}
```

Use the access token to authenticate against the MCP server as detailed in the [Use Access Token](#use-access-token) section above.

---

## Customer Support

For information on the tools, domain, data blocks, and prompt examples, see documentation [here](https://view.highspot.com/viewer/c30512264f4ef2ef3c481ddb5bdd8c97#1).

If you have any issues during onboarding, you may contact D&B Customer Service:

| Region | Contact |
|--------|---------|
| US | 1.800.234.3867 or open a case via [Digital Service Center](https://support.dnb.com) |
| CA | 1-800-463-6362 |
| UK & Ireland | 44(0)845 145 1700 |
| AsiaPac | 91 114 149 7974 |
| China | 86 21 23213775 |

Worldwide Network alliance members, please submit inquiries using the [support page](https://www.dnb.com/utility-pages/contact-us.html).

For all other markets, please visit [Choose your country](https://www.dnb.com/utility-pages/contact-us.html).

---

## D&B Trust Centre

For information on D&B's security and privacy practices, please visit the [D&B Trust Centre](https://www.dnb.com/utility-pages/trust-center.html).

---