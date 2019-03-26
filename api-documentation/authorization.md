# Authorization

Authorization is done via sending two headers:

1. **X-Processing-Key** – The public key, that can be obtained from user's account
2. **X-Processing-Signature** – POST body, signed by the secret key HMAC-SHA512, secret key is also obtained from user's account

See example below in PHP language

```php
$paramsArray = ['key' => 'value'];
$requestBody = json_encode($paramsArray);
$signature   = hash_hmac('sha512', $requestBody, $apiSecret);
```

