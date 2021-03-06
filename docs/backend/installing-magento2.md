---
title: Magento 2 Module
---

## Getting Started

Install and configure your Magento 2 shop to proceed with Deity Module installation

### Prerequisites

DEITY Magento module is compatible with Magento version 2.2+ CE and EE versions.
Full scale support of versions 2.0.* and 2.1.* is not our priority.
However if you encounter an issue running with Magento version lower than 2.2 feel free
to open an issue or reach out to our support channel.

### Installing

Installing DEITY Magento 2 Module is similar to installing any module for the Magento 2 platform

```bash
composer require deity/falcon-magento ^4.0.2
bin/magento setup:upgrade
```

When the module is setup, create one extra magento admin user for DEITY Falcon to connect.
This user exists purely for the API.

```bash
bin/magento admin:user:create \
  --admin-user='your-admin-username' \
  --admin-password='your-admin-password' \
  --admin-email='admin@deity.test' \
  --admin-firstname='your-admin-username' \
  --admin-lastname='your-admin-password'
```

Configure magento to use web server rewrites.
You can do it from magento admin in `Configuration->General->Web->Search Engine Optimization`. Or by running this command:
```bash
bin/magento config:set web/seo/use_rewrites 1
```
Clean Magento cache for changes to take effect.

#### Connect Falcon to your Magento instance

Configure Falcon Server to connect to your Magento instance.
You can do so by changing your [Falcon-Server config](miscellaneous/config.md).

> Note: Use your API admin user credentials that you've just created here

```json
{
  "apis": {
    "api-magento2": {
      "package": "@deity/falcon-magento2-api",
      "config": {
        "host": "your-magento-host-url-here",
        "protocol": "https",
        "username": "your-admin-username",
        "password": "your-admin-password"
      }
    }
  }
}
```

Please be aware that Magento performance depends heavily on which mode it is running in, `developer` mode will negatively impact performance. Also remember to follow Magento's best practices.

## Versioning

[SemVer](http://semver.org/) is used for versioning. For the versions available, see the [tags on this repository](https://github.com/deity-io/falcon-magento2-module/tags).

## Elements introduced to Magento by this module

Custom REST API endpoints provided by this module:

### General
- `[GET] /V1/falcon/urls` - Url resolver. Provides info about entity behind the url or error if URL does not exist in Magento.
- `[GET] /V1/falcon/menus` - Get items for top navigation menu.

### Catalog

- `[GET] /V1/falcon/breadcrumbs` - Get list of breadcrumbs for given url.
- `[GET] /V1/falcon/categories/:categoryId/products` - Get product and filters data for given category id. Can be used to provide filtered or sorted content.

### Checkout
- `[POST] /V1/falcon/carts/mine/save-payment-information-and-order` - Save payment information and place order for customer.
- `[POST] /V1/falcon/guest-carts/:cartId/save-payment-information-and-order` - Save payment information and place order for guest.
- `[PUT] /V1/falcon/carts/mine/place-order` - Place order for customer.
- `[PUT] /V1/falcon/guest-carts/:cartId/place-order` - Place order for guest.
- `[POST] /V1/falcon/carts/mine` - Get existing or create customer cart id.

### Customer

- `[PUT] /V1/falcon/customers/password/reset` - Reset password API.
- `[GET] /V1/falcon/customers/me/address/` - Get list of customer addresses.
- `[GET] /V1/falcon/customers/me/address/:addressId` - Get customer address info.
- `[POST] /V1/falcon/customers/me/address` - Create customer address.
- `[PUT] /V1/falcon/customers/me/address` - Updated customer address.
- `[DELETE] /V1/falcon/customers/me/address/:addressId` - Delete customer address.
- `[PUT] /V1/falcon/customers/me/newsletter/subscribe` - Subscribe customer to newsletter.
- `[PUT] /V1/falcon/customers/me/newsletter/unsubscribe` - Unsubscribe customer from newsletter.


### Paypal
- `[GET] /V1/falcon/guest-carts/:cartId/paypal-express-fetch-token` - Get paypal token for guest.
- `[GET] /V1/falcon/carts/mine/paypal-express-fetch-token` - Get paypal token for customer.
- `[GET] /V1/falcon/guest-carts/:cartId/paypal-express-return` - 3d secure `success` return API for guest.
- `[GET] /V1/falcon/carts/mine/paypal-express-return` - 3d secure `success` return API for customer.
- `[GET] /V1/falcon/guest-carts/:cartId/paypal-express-cancel` - 3d secure `cancel` return API for guest.
- `[GET] /V1/falcon/carts/mine/paypal-express-cancel` - 3d secure `cancel` return API for customer.

### Orders
- `[GET] /V1/falcon/orders/mine` - Get customer orders.
- `[GET] /V1/falcon/orders/:orderId/order-info` - Get customer order info.
- `[GET] /V1/falcon/guest-orders/:orderId/order-info` - Get guest order info.

Extension attributes:

- `Magento\Bundle\Api\Data\LinkInterface`:
    ```json
    {
      "name": "string",
      "catalog_display_price": "string"
    }
    ```
- `Magento\Catalog\Api\Data\ProductInterface`:
    ```json
    {
      "thumbnail_resized_url": "string",
      "thumbnail_url": "string",
      "media_gallery_sizes": "Deity\MagentoApi\Api\Data\GalleryMediaEntrySizeInterface[]",
      "catalog_display_price": "float",
      "min_price": "float",
      "max_price": "float"
    }
    ```
- `Magento\Customer\Api\Data\CustomerInterface`:
    ```json
    {
      "newsletter_subscriber": "bool"
    }
    ```
- `Magento\Sales\Api\Data\OrderInterface`:
    ```json
    {
      "currency": "string",
      "masked_id": "string",
      "shipping_address": "Magento\Sales\Api\Data\OrderAddressInterface"
    }
    ```
- `Magento\Sales\Api\Data\OrderItemInterface`:
    ```json
    {
      "currency": "string",
      "display_price": "string",
      "link": "string",
      "row_total_incl_tax": "string",
      "thumbnail_url": "string",
      "url_key": "string",
      "options": "string"
    }
    ```
- `Magento\Store\Api\Data\StoreConfigInterface`:
    ```json
    {
      "optional_post_codes": "mixed",
      "min_password_length": "int",
      "min_password_char_class": "int",
      "api_version": "string",
      "customer_token_lifetime": "int",
      "admin_token_lifetime": "int"
    }
    ```
- `Magento\Store\Api\Data\StoreInterface`:
    ```json
    {
      "is_active": "int"
    }
    ```
- `Magento\Quote\Api\Data\TotalsItemInterface`:
    ```json
    {
      "thumbnail_url": "string",
      "url_key": "string",
      "available_qty": "string"
    }
    ```
    
## Contribution

For issues, feature or improvements or pull requests please go to [falcon-development](https://github.com/deity-io/falcon-magento2-development).


