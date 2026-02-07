# Shop Setup - Shipping, Sections & Policies

Before you can activate listings, the shop needs shipping profiles and return policies configured.

## Discovering Your Shop

```bash
# Get your user info and shop ID
curl "https://openapi.etsy.com/v3/application/users/me" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"

# Get full shop details
curl "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}" \
  -H "x-api-key: ${API_KEY}"
```

Key shop fields: `shop_id`, `shop_name`, `currency_code`, `listing_active_count`, `is_vacation`, `url`.

## Shop Sections

Sections organize listings into browsable categories in your storefront.

```bash
# List existing sections
curl "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/sections" \
  -H "x-api-key: ${API_KEY}"

# Create a section
curl -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/sections" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "title=Mugs & Cups"

# Update a section
curl -X PUT "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/sections/${SECTION_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "title=Ceramic Mugs"

# Delete a section
curl -X DELETE "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/sections/${SECTION_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

Assign listings to sections via `shop_section_id` when creating or updating a listing.

## Shipping Profiles

Every active listing needs a shipping profile. Create profiles once, reuse across listings.

### Create a shipping profile

```bash
curl -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/shipping-profiles" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "title=Standard Domestic Shipping" \
  -d "origin_country_iso=US" \
  -d "primary_cost=5.99" \
  -d "secondary_cost=2.99" \
  -d "min_processing_time=1" \
  -d "max_processing_time=3" \
  -d "processing_time_unit=business_days" \
  -d "destination_country_iso=US" \
  -d "origin_postal_code=90210" \
  -d "min_delivery_days=3" \
  -d "max_delivery_days=7"
```

#### Required fields

| Field | Type | Notes |
|-------|------|-------|
| `title` | string | Name for this profile |
| `origin_country_iso` | string | ISO country code (e.g., `US`, `GB`, `CA`) |
| `primary_cost` | number | Cost to ship one item |
| `secondary_cost` | number | Cost to ship each additional item in same order |

#### Optional fields

| Field | Type | Notes |
|-------|------|-------|
| `min_processing_time` | integer | Min days to prepare shipment |
| `max_processing_time` | integer | Max days to prepare shipment |
| `processing_time_unit` | enum | `business_days` or `weeks` |
| `destination_country_iso` | string | Target country. Null = everywhere. |
| `destination_region` | enum | `eu`, `non_eu`, `none` |
| `origin_postal_code` | string | For calculated shipping |
| `shipping_carrier_id` | integer | For calculated shipping |
| `mail_class` | string | Carrier-specific mail class |
| `min_delivery_days` | integer | Estimated min delivery time |
| `max_delivery_days` | integer | Estimated max delivery time |

### Add international destinations

```bash
# Add a destination to an existing profile
curl -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/shipping-profiles/${PROFILE_ID}/destinations" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "primary_cost=15.99" \
  -d "secondary_cost=5.99" \
  -d "destination_country_iso=GB" \
  -d "min_delivery_days=7" \
  -d "max_delivery_days=21"
```

### List shipping carriers (for calculated shipping)

```bash
curl "https://openapi.etsy.com/v3/application/shipping-carriers?origin_country_iso=US" \
  -H "x-api-key: ${API_KEY}"
```

### List existing profiles

```bash
curl "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/shipping-profiles" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

## Return Policies

Every active listing needs a return policy.

### Create a return policy

```bash
curl -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/policies/return" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "accepts_returns=true" \
  -d "accepts_exchanges=true"
```

### List return policies

```bash
curl "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/policies/return" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

## Updating Shop Details

```bash
curl -X PUT "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "title=My Shop Tagline" \
  -d "announcement=Welcome to my shop!" \
  -d "sale_message=Thank you for your purchase!" \
  -d "digital_sale_message=Here are your files. Enjoy!"
```

## Holiday / Vacation Mode

```bash
# Check holiday preferences
curl "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/holiday-preferences" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

## Setup Checklist

Before activating listings, ensure:

1. [x] Registered app and got API key
2. [x] Completed OAuth flow and stored tokens
3. [ ] Retrieved shop ID via `getMe`
4. [ ] Created at least one **shipping profile**
5. [ ] Created at least one **return policy**
6. [ ] Created **shop sections** for product organization
7. [ ] Looked up **taxonomy IDs** for your product categories
8. [ ] Set shop title, announcement, sale messages
