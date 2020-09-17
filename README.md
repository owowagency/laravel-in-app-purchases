# Laravel in-app purchases

[![Latest Version on Packagist](https://img.shields.io/packagist/v/imdhemy/laravel-purchases.svg?style=flat-square)](https://packagist.org/packages/imdhemy/laravel-purchases)
[![Total Downloads](https://img.shields.io/packagist/dt/imdhemy/laravel-purchases.svg?style=flat-square)](https://packagist.org/packages/imdhemy/laravel-purchases)

Laravel Receipt validator for Google play Billing. After a user has made a purchase, you should do the following:
- Send the corresponding `purchaseToken` to your backend. This means that you should maintain a record of all `purchaseToken` values for all purchases.
- Verify that the `purchaseToken` value for the current purchase does not match any previous purchaseToken values. `purchaseToken` is globally unique, so you can safely use this value as a primary key in your database.
- Use the Purchases.products:get or Purchases.subscriptions:get endpoints in the Google Play Developer API to verify with Google that the purchase is legitimate.
- If the purchase is legitimate and has not been used in the past, you can then safely grant entitlement to the in-app item or subscription.

## Installation
Install the package via composer:
```
composer require imdhemy/laravel-purchases
```

Publish and run the migrations:
```
 php artisan vendor:publish --provider="Imdhemy\Purchases\PurchaseServiceProvider" --tag="migrations"
 php artisan migrate
```

Publish the config file:
```
php artisan vendor:publish --provider="Imdhemy\Purchases\PurchaseServiceProvider" --tag="config"
```

## Usage
### Subscriptions

```php
use Imdhemy\Purchases\GooglePlay\Subscriptions\Subscription as GooglePlaySubscription;
// ...
$subscriptionId = "subscription_id";
$purchaseToken = "unique_purchase_token";

$receipt = GooglePlaySubscription::check($subscriptionId, $purchaseToken);

if($receipt->isValid()){
    $receipt->persist();
    // extend user's subscription

    if ($receipt->isAutoRenewing()){
         // schedule next auto-renewing
    }
}

// ...
```

### Products
```php
use Imdhemy\Purchases\GooglePlay\Products\Product as GooglePlayProduct;

// ...
$productId = "product_id";
$purchaseToken = "unique_purchase_token";

$receipt = GooglePlayProduct::check($productId, $purchaseToken);
if ($receipt->isValid()){
    $receipt->persist();
    // Allow the user to access the purchased product
}

// ...
```
