Spike for PHP
=============

\[ABANDONED\]
-------------

This project is already abandoned due to [SPIKE](https://spike.cc) payment service close, and you can no longer access to their api endpoint anymore.

Latest release: [v1.0-BETA5](https://packagist.org/packages/issei-m/spike-php#v1.0-BETA5)

The client of https://spike.cc REST api for PHP (5.4+, 7+, HHVM).

Basic Usage
-----------

First, initialize the `Spike` object with your **api secret key**. It's the entry point for accessing the all api interfaces:

```php
$spike = new \Issei\Spike\Spike('your_api_secret_key');
```

### Create a new charge

To create a new charge, you have to build a `ChargeRequest` object. It can be specified `card token`, `amount`, `currency` and some related products. Next, call `charge()` method with it. If charge succeeded this method will return the new `Charge` object generated by REST api:

```php
// The token's id is retrieved by SPIKE Checkout (client side script) usually.
$token = new \Issei\Spike\Model\Token('tok_xxxxxxxxxxxxxxxxxxxxxxxx');

$request = new \Issei\Spike\ChargeRequest();
$request
    ->setToken($token)
    ->setAmount(123.45, 'USD')
    ->setCapture(true) // If you set false, you can delay capturing.
;

$product = new \Issei\Spike\Model\Product('my-product-00001');
$product
    ->setTitle('Product Name')
    ->setDescription('Description of Product.')
    ->setPrice(123.45, 'USD')
    ->setLanguage('EN')
    ->setCount(3)
    ->setStock(97)
;

// The product can be added any times.
$request->addProduct($product);

/** @var $createdCharge \Issei\Spike\Model\Charge */
$createdCharge = $spike->charge($request);
```

Tips: You can pass the `Token`'s id directly instead of generating the new `Token` object:

```php
$request->setToken('tok_xxxxxxxxxxxxxxxxxxxxxxxx');
```

**NOTE**: If you want to know how to get a `card token`, read [Request a token](#request-a-token) section.

### Find a charge

Call `getCharge()` method with charge id:

```php
/** @var $charge \Issei\Spike\Model\Charge */
$charge = $spike->getCharge('20150101-100000-xxxxxxxxxx');
```

### Capture the charge

If you have a charge which has not been captured, you can use `capture()` method to capture it:

```php
/** @var $charge \Issei\Spike\Model\Charge */
$capturedCharge = $spike->capture($charge);
```

### Refund the charge

Call `refund()` method with the `Charge` object that you want to refund:

```php
/** @var $charge \Issei\Spike\Model\Charge */
$refundedCharge = $spike->refund($charge);
```

Tips: You can pass the `Charge`'s id directly instead of generating/retrieving the `Charge` object:

```php
$refundedCharge = $spike->refund('20150101-100000-xxxxxxxxxx');
```

### Retrieve the all charges

Call `getCharges()` method. it returns an array containing the `Charge` objects.

```php
/** @var $charges \Issei\Spike\Model\Charge[] */
$charges = $spike->getCharges();
```

#### Paging

You can specify the limit of number of records at 1st argument (10 records by default):

```php
$charges = $spike->getCharges(5);
```

If you pass a `Charge` object (or ID as a string directly) into 2nd argument, you can retrieve charges that older than (passed charge is NOT included to list):

```php
$nextCharges = $spike->getCharges(5, $charges[count($charges) - 1]);
```

At 3rd argument, you can also specify the charge (or ID as a string directly) object if you want to retrieve charges that newer than (passed charge is NOT included to list):

```php
$nextCharges = $spike->getCharges(5, $charges[count($charges) - 1], ...);
```

### Request a token

If you have contracted with https://spike.cc to request a new token, you can get a new token by `requestToken()` method with `TokenRequest`:

```php
$request = new \Issei\Spike\TokenRequest();
$request
    ->setCardNumber('4444333322221111')
    ->setExpirationMonth(12)
    ->setExpirationYear(19)
    ->setHolderName('Taro Spike')
    ->setSecurityCode('123')
    ->setCurrency('JPY')
    ->setEmail('test@example.jp')
;

/** @var $charge \Issei\Spike\Model\Token */
$token = $spike->requestToken($request);
```

Of course, you can create a new charge with it:

```php
$request = new \Issei\Spike\ChargeRequest();
$request
    ->setToken($token)
    // ...
;

/** @var $charge \Issei\Spike\Model\Charge */
$charge = $spike->charge($request);
```

### Find a token

Call `getToken()` method with token id:

```php
/** @var $token \Issei\Spike\Model\Token */
$token = $spike->getToken('tok_xxxxxxxxxxxxxxxxxxxxxxxx');
```

Installation
------------

Use [Composer] to install the package:

```
$ composer require issei-m/spike-php
```

Contributing
------------

1. Fork it
2. Create your feature branch
3. Commit your change and push it
4. Create a new pull request

[SPIKE Checkout]: https://spike.cc/dashboard/developer/docs/references#a1
[Composer]: https://getcomposer.org
