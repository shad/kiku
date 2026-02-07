# Betsy - Etsy Storefront Management

## Project Goal

Build and manage an Etsy storefront and its listings programmatically using the Etsy Open API v3.

## Etsy API Spec

The canonical OpenAPI 3.0 spec is published at:

```
https://www.etsy.com/openapi/generated/oas/3.0.0.json
```

To download/refresh the local copy:

```bash
curl -s https://www.etsy.com/openapi/generated/oas/3.0.0.json -o etsy-openapi-spec.json
```

The local file `etsy-openapi-spec.json` is the working copy (~868KB). Re-download periodically as Etsy updates endpoints (especially during the processing profiles migration through Q1 2026).

## Key References

- API docs: https://developers.etsy.com/documentation/
- API reference: https://developers.etsy.com/documentation/reference/
- Auth guide: https://developer.etsy.com/documentation/essentials/authentication/
- GitHub issues: https://github.com/etsy/open-api

## Project Docs

See `/docs/` for detailed guides:

- `api-overview.md` - Full endpoint inventory and capabilities
- `authentication.md` - OAuth 2.0 + PKCE flow, token management, scopes
- `listings-guide.md` - Creating, updating, managing listings (the core workflow)
- `shop-setup.md` - Shop configuration, sections, shipping profiles, return policies
- `curl-recipes.md` - Ready-to-use curl commands for common operations

## API Conventions

- Base URL: `https://openapi.etsy.com`
- Auth header: `x-api-key: YOUR_KEYSTRING` (all requests)
- OAuth header: `Authorization: Bearer ACCESS_TOKEN` (authenticated requests)
- Request bodies: `application/x-www-form-urlencoded` (not JSON)
- Image/video uploads: `multipart/form-data`
- Pagination: `limit` (default 25, max 100) + `offset` params
- Access tokens expire in 1 hour; use refresh tokens to renew
