# Curl Recipes - Ready-to-Use Commands

All recipes assume these environment variables are set:

```bash
export API_KEY="your_api_key"
export ACCESS_TOKEN="your_access_token"
export REFRESH_TOKEN="your_refresh_token"
export SHOP_ID="your_shop_id"
```

## Health Check

```bash
# Ping (verify API key works)
curl -s "https://openapi.etsy.com/v3/application/openapi-ping" \
  -H "x-api-key: ${API_KEY}"
```

## Token Management

```bash
# Refresh an expired access token
curl -s -X POST "https://api.etsy.com/v3/public/oauth/token" \
  -d "grant_type=refresh_token" \
  -d "client_id=${API_KEY}" \
  -d "refresh_token=${REFRESH_TOKEN}"

# Check what scopes a token has
curl -s -X POST "https://openapi.etsy.com/v3/application/scopes" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

## Identity

```bash
# Who am I? (get user_id and shop_id)
curl -s "https://openapi.etsy.com/v3/application/users/me" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"

# Full shop details
curl -s "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}" \
  -H "x-api-key: ${API_KEY}"
```

## Taxonomy

```bash
# Full seller taxonomy tree (large response -- pipe to file)
curl -s "https://openapi.etsy.com/v3/application/seller-taxonomy/nodes" \
  -H "x-api-key: ${API_KEY}" > taxonomy.json

# Search taxonomy for a category (using jq)
curl -s "https://openapi.etsy.com/v3/application/seller-taxonomy/nodes" \
  -H "x-api-key: ${API_KEY}" \
  | jq '[.. | objects | select(.name? and (.name | test("Mug"; "i")))]'

# Get properties for a taxonomy ID
curl -s "https://openapi.etsy.com/v3/application/seller-taxonomy/nodes/${TAXONOMY_ID}/properties" \
  -H "x-api-key: ${API_KEY}"
```

## Shop Sections

```bash
# List all sections
curl -s "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/sections" \
  -H "x-api-key: ${API_KEY}"

# Create a section
curl -s -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/sections" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "title=New Section Name"
```

## Shipping Profiles

```bash
# List existing profiles
curl -s "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/shipping-profiles" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"

# Create a basic domestic profile
curl -s -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/shipping-profiles" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "title=Standard Shipping" \
  -d "origin_country_iso=US" \
  -d "primary_cost=5.99" \
  -d "secondary_cost=2.99" \
  -d "min_processing_time=1" \
  -d "max_processing_time=3" \
  -d "processing_time_unit=business_days"

# Create free shipping profile
curl -s -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/shipping-profiles" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "title=Free Shipping" \
  -d "origin_country_iso=US" \
  -d "primary_cost=0" \
  -d "secondary_cost=0"
```

## Return Policies

```bash
# List return policies
curl -s "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/policies/return" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"

# Create a return policy
curl -s -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/policies/return" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "accepts_returns=true" \
  -d "accepts_exchanges=true"
```

## Listings - Read

```bash
# List active listings (paginated)
curl -s "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings?state=active&limit=25&offset=0" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"

# List draft listings
curl -s "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings?state=draft" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"

# Get a single listing with images and shipping
curl -s "https://openapi.etsy.com/v3/application/listings/${LISTING_ID}?includes=Images,Shipping" \
  -H "x-api-key: ${API_KEY}"

# Get listings by section
curl -s "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/shop-sections/listings?shop_section_ids=${SECTION_ID}" \
  -H "x-api-key: ${API_KEY}"

# Batch get multiple listings by ID
curl -s "https://openapi.etsy.com/v3/application/listings/batch?listing_ids=${ID1},${ID2},${ID3}&includes=Images" \
  -H "x-api-key: ${API_KEY}"
```

## Listings - Create & Manage

```bash
# Create a draft listing
curl -s -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "quantity=10" \
  -d "title=Product Title Here" \
  -d "description=Product description here" \
  -d "price=25.00" \
  -d "who_made=i_did" \
  -d "when_made=2020_2026" \
  -d "taxonomy_id=${TAXONOMY_ID}" \
  -d "shipping_profile_id=${SHIPPING_PROFILE_ID}" \
  -d "return_policy_id=${RETURN_POLICY_ID}" \
  -d "type=physical"

# Activate a draft
curl -s -X PATCH "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings/${LISTING_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "state=active"

# Deactivate a listing
curl -s -X PATCH "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings/${LISTING_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "state=inactive"

# Update listing details
curl -s -X PATCH "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings/${LISTING_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "title=Updated Title" \
  -d "price=30.00"

# Delete a listing (must not be active)
curl -s -X DELETE "https://openapi.etsy.com/v3/application/listings/${LISTING_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

## Images

```bash
# Upload an image (rank 1 = primary/thumbnail)
curl -s -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings/${LISTING_ID}/images" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -F "image=@/path/to/image.jpg" \
  -F "rank=1" \
  -F "alt_text=Description of the image"

# List all images for a listing
curl -s "https://openapi.etsy.com/v3/application/listings/${LISTING_ID}/images" \
  -H "x-api-key: ${API_KEY}"

# Delete an image
curl -s -X DELETE "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings/${LISTING_ID}/images/${IMAGE_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

## Videos

```bash
# Upload a video
curl -s -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings/${LISTING_ID}/videos" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -F "video=@/path/to/video.mp4" \
  -F "name=Product Demo"

# List videos
curl -s "https://openapi.etsy.com/v3/application/listings/${LISTING_ID}/videos" \
  -H "x-api-key: ${API_KEY}"
```

## Inventory / Variations

```bash
# Get inventory
curl -s "https://openapi.etsy.com/v3/application/listings/${LISTING_ID}/inventory" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"

# Update inventory (JSON body -- one of the few endpoints that uses JSON)
curl -s -X PUT "https://openapi.etsy.com/v3/application/listings/${LISTING_ID}/inventory" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -H "Content-Type: application/json" \
  -d @inventory.json
```

## Orders / Receipts

```bash
# List recent orders
curl -s "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/receipts?limit=25" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"

# Get a specific order
curl -s "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/receipts/${RECEIPT_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"

# Add tracking info to an order
curl -s -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/receipts/${RECEIPT_ID}/tracking" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "tracking_code=1Z999AA10123456784" \
  -d "carrier_name=usps"

# Get transactions for an order
curl -s "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/receipts/${RECEIPT_ID}/transactions" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

## Reviews

```bash
# Shop reviews
curl -s "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/reviews?limit=25" \
  -H "x-api-key: ${API_KEY}"

# Reviews for a specific listing
curl -s "https://openapi.etsy.com/v3/application/listings/${LISTING_ID}/reviews" \
  -H "x-api-key: ${API_KEY}"
```

## Tips

- Pipe responses through `| python3 -m json.tool` or `| jq .` for pretty printing
- Add `-v` flag to see response headers (useful for debugging auth issues)
- Use `-o /dev/null -w "%{http_code}"` to just check the status code
- Store tokens in a `.env` file and source it: `source .env`
