# Listings Guide - Creating & Managing Etsy Listings

This is the core workflow for building out a storefront.

## Listing Lifecycle

```
createDraftListing (draft) --> updateListing state=active (live) --> updateListing state=inactive (paused)
                                                                 --> deleteListing (removed)
```

Listings are created as **drafts** and must be explicitly activated. A listing requires a shipping profile and return policy before it can go active.

## Creating a Draft Listing

```bash
curl -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "quantity=10" \
  -d "title=Handmade Ceramic Mug - Speckled Blue Glaze" \
  -d "description=A beautiful handmade ceramic mug..." \
  -d "price=28.00" \
  -d "who_made=i_did" \
  -d "when_made=2020_2026" \
  -d "taxonomy_id=TAXONOMY_ID" \
  -d "shipping_profile_id=${SHIPPING_PROFILE_ID}" \
  -d "return_policy_id=${RETURN_POLICY_ID}" \
  -d "tags[]=ceramic,mug,handmade,pottery,blue" \
  -d "materials[]=ceramic,glaze" \
  -d "type=physical"
```

### Required Fields

| Field | Type | Notes |
|-------|------|-------|
| `quantity` | integer | Must be > 0 |
| `title` | string | Max 140 chars. No all-caps. |
| `description` | string | Product description |
| `price` | number | In shop's currency. Must be > 0 |
| `who_made` | enum | `i_did`, `someone_else`, `collective` |
| `when_made` | enum | `made_to_order`, `2020_2026`, `2010_2019`, etc. |
| `taxonomy_id` | integer | From seller taxonomy tree (see below) |

### Important Optional Fields

| Field | Type | Notes |
|-------|------|-------|
| `shipping_profile_id` | integer | Required to activate. Create one first. |
| `return_policy_id` | integer | Required to activate. Create one first. |
| `tags` | array | Up to 13 tags, each max 20 chars |
| `materials` | array | Up to 13 materials |
| `shop_section_id` | integer | Organize into shop sections |
| `type` | enum | `physical`, `download`, `both` |
| `is_supply` | boolean | true = craft supply, false = finished product |
| `is_personalizable` | boolean | Allow buyer personalization |
| `is_customizable` | boolean | Allow custom order requests |
| `should_auto_renew` | boolean | Auto-renew on expiration ($0.20/renewal) |
| `item_weight` | number | Product weight |
| `item_weight_unit` | enum | `oz`, `lb`, `g`, `kg` |
| `item_length/width/height` | number | Dimensions |
| `item_dimensions_unit` | enum | `in`, `ft`, `mm`, `cm`, `m`, `yd` |
| `image_ids` | array | Pre-uploaded image IDs |
| `production_partner_ids` | array | If using production partners |

## Finding Your Taxonomy ID

Taxonomy IDs categorize your product. Use the seller taxonomy endpoint:

```bash
# Get the full taxonomy tree
curl "https://openapi.etsy.com/v3/application/seller-taxonomy/nodes" \
  -H "x-api-key: ${API_KEY}" | python3 -m json.tool
```

The response is a tree. Navigate through `children` to find the right category. Example path: `Home & Living > Kitchen & Dining > Drinkware > Mugs`.

```bash
# Get available properties for a taxonomy node
curl "https://openapi.etsy.com/v3/application/seller-taxonomy/nodes/${TAXONOMY_ID}/properties" \
  -H "x-api-key: ${API_KEY}"
```

This returns property definitions (color, size, etc.) with their valid values -- needed for creating variations.

## Activating a Draft

```bash
curl -X PATCH "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings/${LISTING_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "state=active"
```

This will fail if the listing is missing required data (shipping profile, return policy, at least one image).

## Updating a Listing

All fields are optional on update -- only send what you're changing:

```bash
curl -X PATCH "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings/${LISTING_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "title=Updated Title Here" \
  -d "price=32.00" \
  -d "quantity=25"
```

### Updatable Fields

`title`, `description`, `price` (via inventory), `quantity` (via inventory), `tags`, `materials`, `who_made`, `when_made`, `taxonomy_id`, `shipping_profile_id`, `return_policy_id`, `shop_section_id`, `state` (`active`/`inactive`), `featured_rank`, `is_personalizable`, `is_supply`, `is_customizable`, `should_auto_renew`, `type`, image IDs, dimensions, weight, production partners.

**Note:** Price and quantity are better managed through the Inventory endpoint for listings with variations.

## Uploading Images

Images use `multipart/form-data`:

```bash
curl -X POST "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings/${LISTING_ID}/images" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -F "image=@/path/to/photo.jpg" \
  -F "rank=1" \
  -F "alt_text=Blue ceramic mug on wooden table"
```

- `rank=1` makes it the primary/thumbnail image
- Up to 10 images per listing
- `overwrite=true` replaces an existing image at that rank
- `alt_text` max 500 chars
- Supported formats: JPG, PNG, GIF

## Managing Inventory & Variations

Inventory tracks price, quantity, and SKU per product variant. Use this for listings with size/color options.

### Get current inventory

```bash
curl "https://openapi.etsy.com/v3/application/listings/${LISTING_ID}/inventory" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

### Update inventory

```bash
curl -X PUT "https://openapi.etsy.com/v3/application/listings/${LISTING_ID}/inventory" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "products": [
      {
        "sku": "MUG-BLUE-SM",
        "offerings": [
          {
            "price": 28.00,
            "quantity": 10,
            "is_enabled": true
          }
        ],
        "property_values": [
          {
            "property_id": 200,
            "value_ids": [1],
            "values": ["Small"]
          }
        ]
      },
      {
        "sku": "MUG-BLUE-LG",
        "offerings": [
          {
            "price": 34.00,
            "quantity": 5,
            "is_enabled": true
          }
        ],
        "property_values": [
          {
            "property_id": 200,
            "value_ids": [2],
            "values": ["Large"]
          }
        ]
      }
    ],
    "price_on_property": [200],
    "quantity_on_property": [200],
    "sku_on_property": [200]
  }'
```

**Note:** The inventory endpoint is one of the few that accepts `application/json` rather than form-urlencoded.

The `property_id` values come from the taxonomy properties endpoint. Common ones:
- `200` = Primary color
- `100` = Size (varies by taxonomy)

Use `getPropertiesByTaxonomyId` to find the exact property IDs and valid values for your category.

## Listing Properties

Properties are taxonomy-specific attributes (color, dimensions, material, etc.) separate from variations:

```bash
# Set a property
curl -X PUT "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings/${LISTING_ID}/properties/${PROPERTY_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d "value_ids[]=VALUE_ID" \
  -d "values[]=Custom Value"
```

## Browsing Your Listings

```bash
# All listings (any state) -- requires OAuth
curl "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings?state=active&limit=25&offset=0" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"

# With associations (images, shipping info embedded)
curl "https://openapi.etsy.com/v3/application/shops/${SHOP_ID}/listings?state=active&includes=Images,Shipping" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

State filter values: `active`, `inactive`, `draft`, `expired`, `sold_out`.

Sort options: `sort_on=created` (default), `price`, `updated`. Direction: `sort_order=desc` (default) or `asc`.

## Deleting a Listing

```bash
curl -X DELETE "https://openapi.etsy.com/v3/application/listings/${LISTING_ID}" \
  -H "x-api-key: ${API_KEY}" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

Only works on draft, inactive, expired, or sold_out listings. Active listings must be deactivated first.

## Batch Workflow for Populating a Store

1. **Create shipping profile(s)** (see shop-setup.md)
2. **Create return policy** (see shop-setup.md)
3. **Create shop sections** to organize listings
4. **Look up taxonomy IDs** for your product categories
5. **For each product:**
   a. `createDraftListing` with required fields + shipping/return IDs
   b. `uploadListingImage` (at least 1, ideally 5-10)
   c. `updateListingInventory` if the listing has variations
   d. `updateListing` with `state=active` to go live
