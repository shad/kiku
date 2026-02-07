# Etsy Open API v3 - Endpoint Overview

Base URL: `https://openapi.etsy.com`

## Authentication Levels

| Level | What it gets you |
|-------|-----------------|
| **API key only** (`x-api-key` header) | Public reads: active listings, taxonomy, reviews, shop info, images |
| **OAuth token** (`Authorization: Bearer`) | Private reads + all writes: draft listings, orders, inventory, shop management |

## All Endpoints by Category (70 total)

### ShopListing (16 endpoints) - Core listing management

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| POST | `/v3/application/shops/{shop_id}/listings` | createDraftListing | listings_w |
| GET | `/v3/application/shops/{shop_id}/listings` | getListingsByShop | listings_r |
| GET | `/v3/application/listings/{listing_id}` | getListing | api_key |
| PATCH | `/v3/application/shops/{shop_id}/listings/{listing_id}` | updateListing | listings_w |
| DELETE | `/v3/application/listings/{listing_id}` | deleteListing | listings_d |
| GET | `/v3/application/listings/active` | findAllListingsActive | api_key |
| GET | `/v3/application/shops/{shop_id}/listings/active` | findAllActiveListingsByShop | api_key |
| GET | `/v3/application/listings/batch` | getListingsByListingIds | api_key |
| GET | `/v3/application/shops/{shop_id}/listings/featured` | getFeaturedListingsByShop | api_key |
| PUT | `/v3/application/shops/{shop_id}/listings/{listing_id}/properties/{property_id}` | updateListingProperty | listings_w |
| DELETE | `/v3/application/shops/{shop_id}/listings/{listing_id}/properties/{property_id}` | deleteListingProperty | listings_w |
| GET | `/v3/application/listings/{listing_id}/properties/{property_id}` | getListingProperty | api_key |
| GET | `/v3/application/shops/{shop_id}/listings/{listing_id}/properties` | getListingProperties | api_key |
| GET | `/v3/application/shops/{shop_id}/receipts/{receipt_id}/listings` | getListingsByShopReceipt | transactions_r |
| GET | `/v3/application/shops/{shop_id}/policies/return/{return_policy_id}/listings` | getListingsByShopReturnPolicy | listings_r |
| GET | `/v3/application/shops/{shop_id}/shop-sections/listings` | getListingsByShopSectionId | api_key |

### ShopListing Image (4 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| POST | `/v3/application/shops/{shop_id}/listings/{listing_id}/images` | uploadListingImage | listings_w |
| GET | `/v3/application/listings/{listing_id}/images` | getListingImages | api_key |
| GET | `/v3/application/listings/{listing_id}/images/{listing_image_id}` | getListingImage | api_key |
| DELETE | `/v3/application/shops/{shop_id}/listings/{listing_id}/images/{listing_image_id}` | deleteListingImage | listings_w |

### ShopListing Video (4 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| POST | `/v3/application/shops/{shop_id}/listings/{listing_id}/videos` | uploadListingVideo | listings_w |
| GET | `/v3/application/listings/{listing_id}/videos` | getListingVideos | api_key |
| GET | `/v3/application/listings/{listing_id}/videos/{video_id}` | getListingVideo | api_key |
| DELETE | `/v3/application/shops/{shop_id}/listings/{listing_id}/videos/{video_id}` | deleteListingVideo | listings_w |

### ShopListing File (4 endpoints) - Digital product files

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| POST | `/v3/application/shops/{shop_id}/listings/{listing_id}/files` | uploadListingFile | listings_w |
| GET | `/v3/application/shops/{shop_id}/listings/{listing_id}/files` | getAllListingFiles | listings_r |
| GET | `/v3/application/shops/{shop_id}/listings/{listing_id}/files/{listing_file_id}` | getListingFile | listings_r |
| DELETE | `/v3/application/shops/{shop_id}/listings/{listing_id}/files/{listing_file_id}` | deleteListingFile | listings_w |

### ShopListing Inventory (2 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/listings/{listing_id}/inventory` | getListingInventory | listings_r |
| PUT | `/v3/application/listings/{listing_id}/inventory` | updateListingInventory | listings_w |

### ShopListing Personalization (3 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| POST | `/v3/application/shops/{shop_id}/listings/{listing_id}/personalization` | updateListingPersonalization | listings_w |
| GET | `/v3/application/listings/{listing_id}/personalization` | getListingPersonalization | api_key |
| DELETE | `/v3/application/shops/{shop_id}/listings/{listing_id}/personalization` | deleteListingPersonalization | listings_w |

### ShopListing VariationImage (2 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/shops/{shop_id}/listings/{listing_id}/variation-images` | getListingVariationImages | api_key |
| POST | `/v3/application/shops/{shop_id}/listings/{listing_id}/variation-images` | updateVariationImages | listings_w |

### ShopListing Translation (3 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| POST | `/v3/application/shops/{shop_id}/listings/{listing_id}/translations/{language}` | createListingTranslation | listings_w |
| GET | `/v3/application/shops/{shop_id}/listings/{listing_id}/translations/{language}` | getListingTranslation | api_key |
| PUT | `/v3/application/shops/{shop_id}/listings/{listing_id}/translations/{language}` | updateListingTranslation | listings_w |

### ShopListing Product / Offering (2 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/listings/{listing_id}/inventory/products/{product_id}` | getListingProduct | listings_r |
| GET | `/v3/application/listings/{listing_id}/products/{product_id}/offerings/{product_offering_id}` | getListingOffering | api_key |

### Shop (4 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/shops/{shop_id}` | getShop | api_key |
| PUT | `/v3/application/shops/{shop_id}` | updateShop | shops_r, shops_w |
| GET | `/v3/application/users/{user_id}/shops` | getShopByOwnerUserId | api_key |
| GET | `/v3/application/shops` | findShops | api_key |

### Shop Section (5 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| POST | `/v3/application/shops/{shop_id}/sections` | createShopSection | shops_w |
| GET | `/v3/application/shops/{shop_id}/sections` | getShopSections | api_key |
| GET | `/v3/application/shops/{shop_id}/sections/{shop_section_id}` | getShopSection | api_key |
| PUT | `/v3/application/shops/{shop_id}/sections/{shop_section_id}` | updateShopSection | shops_w |
| DELETE | `/v3/application/shops/{shop_id}/sections/{shop_section_id}` | deleteShopSection | shops_w |

### Shop ShippingProfile (14 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/shipping-carriers` | getShippingCarriers | api_key |
| POST | `/v3/application/shops/{shop_id}/shipping-profiles` | createShopShippingProfile | shops_w |
| GET | `/v3/application/shops/{shop_id}/shipping-profiles` | getShopShippingProfiles | shops_r |
| GET | `/v3/application/shops/{shop_id}/shipping-profiles/{shipping_profile_id}` | getShopShippingProfile | shops_r |
| PUT | `/v3/application/shops/{shop_id}/shipping-profiles/{shipping_profile_id}` | updateShopShippingProfile | shops_w |
| DELETE | `/v3/application/shops/{shop_id}/shipping-profiles/{shipping_profile_id}` | deleteShopShippingProfile | shops_w |
| POST | `.../shipping-profiles/{id}/destinations` | createShopShippingProfileDestination | shops_w |
| GET | `.../shipping-profiles/{id}/destinations` | getShopShippingProfileDestinationsByShippingProfile | shops_r |
| PUT | `.../shipping-profiles/{id}/destinations/{dest_id}` | updateShopShippingProfileDestination | shops_w |
| DELETE | `.../shipping-profiles/{id}/destinations/{dest_id}` | deleteShopShippingProfileDestination | shops_w |
| POST | `.../shipping-profiles/{id}/upgrades` | createShopShippingProfileUpgrade | shops_w |
| GET | `.../shipping-profiles/{id}/upgrades` | getShopShippingProfileUpgrades | shops_r |
| PUT | `.../shipping-profiles/{id}/upgrades/{upgrade_id}` | updateShopShippingProfileUpgrade | shops_w |
| DELETE | `.../shipping-profiles/{id}/upgrades/{upgrade_id}` | deleteShopShippingProfileUpgrade | shops_w |

### Shop Return Policy (6 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| POST | `/v3/application/shops/{shop_id}/policies/return` | createShopReturnPolicy | shops_w |
| GET | `/v3/application/shops/{shop_id}/policies/return` | getShopReturnPolicies | shops_r |
| GET | `.../policies/return/{return_policy_id}` | getShopReturnPolicy | shops_r |
| PUT | `.../policies/return/{return_policy_id}` | updateShopReturnPolicy | shops_w |
| DELETE | `.../policies/return/{return_policy_id}` | deleteShopReturnPolicy | shops_w |
| POST | `/v3/application/shops/{shop_id}/policies/return/consolidate` | consolidateShopReturnPolicies | shops_w |

### Shop Receipt (4 endpoints) - Order management

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/shops/{shop_id}/receipts` | getShopReceipts | transactions_r |
| GET | `/v3/application/shops/{shop_id}/receipts/{receipt_id}` | getShopReceipt | transactions_r |
| PUT | `/v3/application/shops/{shop_id}/receipts/{receipt_id}` | updateShopReceipt | transactions_w |
| POST | `/v3/application/shops/{shop_id}/receipts/{receipt_id}/tracking` | createReceiptShipment | transactions_w |

### Shop Receipt Transactions (4 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/shops/{shop_id}/transactions` | getShopReceiptTransactionsByShop | transactions_r |
| GET | `/v3/application/shops/{shop_id}/transactions/{transaction_id}` | getShopReceiptTransaction | transactions_r |
| GET | `.../receipts/{receipt_id}/transactions` | getShopReceiptTransactionsByReceipt | transactions_r |
| GET | `.../listings/{listing_id}/transactions` | getShopReceiptTransactionsByListing | transactions_r |

### Payment / Ledger (5 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `.../payment-account/ledger-entries` | getShopPaymentAccountLedgerEntries | transactions_r |
| GET | `.../payment-account/ledger-entries/{id}` | getShopPaymentAccountLedgerEntry | transactions_r |
| GET | `.../payment-account/ledger-entries/payments` | getPaymentAccountLedgerEntryPayments | transactions_r |
| GET | `/v3/application/shops/{shop_id}/receipts/{receipt_id}/payments` | getShopPaymentByReceiptId | transactions_r |
| GET | `/v3/application/shops/{shop_id}/payments` | getPayments | transactions_r |

### Taxonomy (4 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/buyer-taxonomy/nodes` | getBuyerTaxonomyNodes | api_key |
| GET | `/v3/application/buyer-taxonomy/nodes/{id}/properties` | getPropertiesByBuyerTaxonomyId | api_key |
| GET | `/v3/application/seller-taxonomy/nodes` | getSellerTaxonomyNodes | api_key |
| GET | `/v3/application/seller-taxonomy/nodes/{id}/properties` | getPropertiesByTaxonomyId | api_key |

### Review (2 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/listings/{listing_id}/reviews` | getReviewsByListing | api_key |
| GET | `/v3/application/shops/{shop_id}/reviews` | getReviewsByShop | api_key |

### User (2 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/users/me` | getMe | shops_r |
| GET | `/v3/application/users/{user_id}` | getUser | email_r |

### UserAddress (3 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/user/addresses` | getUserAddresses | address_r |
| GET | `/v3/application/user/addresses/{id}` | getUserAddress | address_r |
| DELETE | `/v3/application/user/addresses/{id}` | deleteUserAddress | address_r |

### Shop ProcessingProfiles (5 endpoints) - New, migration in progress

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| POST | `.../readiness-state-definitions` | createShopReadinessStateDefinition | shops_w |
| GET | `.../readiness-state-definitions` | getShopReadinessStateDefinitions | shops_r |
| GET | `.../readiness-state-definitions/{id}` | getShopReadinessStateDefinition | shops_r |
| PUT | `.../readiness-state-definitions/{id}` | updateShopReadinessStateDefinition | shops_w |
| DELETE | `.../readiness-state-definitions/{id}` | deleteShopReadinessStateDefinition | shops_w |

**Note:** Processing profiles require `?legacy=false` query param. Use test shops only until Etsy announces go-live.

### Shop HolidayPreferences (2 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/shops/{shop_id}/holiday-preferences` | getHolidayPreferences | shops_r |
| PUT | `.../holiday-preferences/{holiday_id}` | updateHolidayPreferences | shops_w |

### Shop ProductionPartner (1 endpoint)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/shops/{shop_id}/production-partners` | getShopProductionPartners | shops_r |

### Other (2 endpoints)

| Method | Path | Operation | Auth |
|--------|------|-----------|------|
| GET | `/v3/application/openapi-ping` | ping | api_key |
| POST | `/v3/application/scopes` | tokenScopes | api_key |

## Pagination

Paginated endpoints accept:
- `limit` - Results per page (default 25, max 100)
- `offset` - Number of records to skip
- `sort_on` - Sort field (e.g., `created`, `price`, `updated`)
- `sort_order` - `asc` or `desc`

## Associations (Includes)

Some GET endpoints accept an `includes` parameter to embed related resources in a single request:
- `Shipping` - Shipping profile data
- `Images` - Listing images
- `Shop` - Shop data
- `User` - User data
- `Translations` - Listing translations
- `Inventory` - Inventory/variant data

Example: `?includes=Images,Shipping`
