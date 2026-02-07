# Authentication - Etsy OAuth 2.0 + PKCE

Etsy uses OAuth 2.0 Authorization Code grants with mandatory PKCE. No client secret is passed in API calls -- PKCE replaces it.

## Prerequisites

1. Register an app at https://www.etsy.com/developers/your-apps
2. You'll get an **API Key** (keystring) and a **shared secret**
3. Register a **redirect URI** (must be HTTPS, exact match including case and trailing slash)

## The OAuth Flow

### Step 1: Generate PKCE values

```bash
# Generate code_verifier (43-128 chars from [A-Za-z0-9._~-])
CODE_VERIFIER=$(python3 -c "import secrets, base64; print(base64.urlsafe_b64encode(secrets.token_bytes(32)).rstrip(b'=').decode())")

# Generate code_challenge = BASE64_URL(SHA256(code_verifier))
CODE_CHALLENGE=$(printf '%s' "$CODE_VERIFIER" | openssl dgst -sha256 -binary | base64 | tr '+/' '-_' | tr -d '=')

echo "code_verifier:  $CODE_VERIFIER"
echo "code_challenge: $CODE_CHALLENGE"
```

### Step 2: Direct user to authorize

Build this URL and open it in a browser:

```
https://www.etsy.com/oauth/connect?
  response_type=code&
  client_id=YOUR_API_KEY&
  redirect_uri=YOUR_REDIRECT_URI&
  scope=listings_r%20listings_w%20listings_d%20shops_r%20shops_w%20transactions_r&
  state=RANDOM_CSRF_STRING&
  code_challenge=YOUR_CODE_CHALLENGE&
  code_challenge_method=S256
```

The user approves, and Etsy redirects to:
```
YOUR_REDIRECT_URI?code=AUTH_CODE&state=RANDOM_CSRF_STRING
```

**Always verify the returned `state` matches what you sent.**

### Step 3: Exchange code for tokens

```bash
curl -X POST https://api.etsy.com/v3/public/oauth/token \
  -d "grant_type=authorization_code" \
  -d "client_id=YOUR_API_KEY" \
  -d "redirect_uri=YOUR_REDIRECT_URI" \
  -d "code=AUTH_CODE_FROM_REDIRECT" \
  -d "code_verifier=$CODE_VERIFIER"
```

Response:
```json
{
  "access_token": "12345678.O1zLu...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "12345678.JNGIJt..."
}
```

The numeric prefix on tokens is the `user_id`.

### Step 4: Refresh when expired

Access tokens last **1 hour**. Refresh without user interaction:

```bash
curl -X POST https://api.etsy.com/v3/public/oauth/token \
  -d "grant_type=refresh_token" \
  -d "client_id=YOUR_API_KEY" \
  -d "refresh_token=YOUR_REFRESH_TOKEN"
```

Returns a new access_token and a new refresh_token. **Store the new refresh_token** -- the old one is invalidated.

## Making Authenticated Requests

Every request needs the API key. Authenticated requests also need the Bearer token:

```bash
curl https://openapi.etsy.com/v3/application/users/me \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

## Scopes Reference

Request the minimum scopes you need. Changing scopes requires re-authorization.

| Scope | Permits |
|-------|---------|
| `listings_r` | Read all listings (including inactive/expired/draft) |
| `listings_w` | Create and edit listings |
| `listings_d` | Delete listings |
| `shops_r` | Read private shop info |
| `shops_w` | Update shop, sections, shipping profiles, return policies |
| `transactions_r` | Read receipts, transactions, payments |
| `transactions_w` | Update receipts, create shipments |
| `email_r` | Read user email/profile |
| `address_r` | Read user addresses |
| `address_w` | Update user addresses |
| `profile_r` | Read full user profile |
| `profile_w` | Update user profile |
| `favorites_r` | Read favorites |
| `favorites_w` | Modify favorites |

### Recommended scopes for storefront management

```
listings_r listings_w listings_d shops_r shops_w transactions_r transactions_w
```

URL-encoded for the auth URL:
```
listings_r%20listings_w%20listings_d%20shops_r%20shops_w%20transactions_r%20transactions_w
```

## Token Storage

Store tokens securely (e.g., `.env` file excluded from git):

```bash
# .env
ETSY_API_KEY=your_keystring
ETSY_SHARED_SECRET=your_shared_secret
ETSY_ACCESS_TOKEN=your_access_token
ETSY_REFRESH_TOKEN=your_refresh_token
ETSY_SHOP_ID=your_shop_id
```

## Dormancy Warning

Apps that make no successful API calls for 6 months are marked dormant and banned. Keep tokens fresh.
