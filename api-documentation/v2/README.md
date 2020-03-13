---
description: >-
  Documentation for API V2. Major update is addresses with exchange crypto to
  fiat and vice versa abilities.
---

# V2

## General information

API endpoint for all requests is:

```text
https://app.coinspaid.com/api/v2
```

### Deposit flow

1. You obtain new address from CoinsPaid API \(for some currencies it may be address and tag\) and store it somewhere on your side. After that you show this address to your customer in order to make a deposit.
2. Customer sends some funds to this address.  
3. When transaction is sent by a customer - CoinsPaid sends a callback to your callback url with transaction details. It contains status, address, currency, amount and fees.  If status is successful, you should deposit respective amount to customer balance on your side.

### Withdrawal flow

1. You request to send amount of money to address.
2. Your request is validated on our side. If signature is correct, address is valid and you have enough balance - CoinsPaid responds you with the transaction object.
3. You will receive a callback when transaction status is updated.

**Deposit with exchange flow**

You don't want to touch or store cryptocurrency, but only use it as a payment method. Your customer deposits **BTC**, Coinspaid instantly converts it to **EUR** so that **you would receive EUR on your CoinsPaid account**.

1. You obtain new address from CoinsPaid API same as in deposit flow, but additionally pass another parameter "convert\_to" in your request specifying resulting currency.
2. When new deposit is arriving, CoinsPaid converts all arriving funds to destination funds, and sends notifications as in regular deposit

**Withdrawal with exchange flow**

You wish to send Cryptocurrency from your Fiat currency balance. For example you want to **send EUR amount** but your customer receives money **in BTC.**

1. You do exactly same as in withdrawals, but you specify 2 currencies. One is a currency of your **sending balance** and Second is a **cryptocurrency your Customer wishes to receive**.
2. Your request is validated on our side. If signature is correct, address is valid and you have enough balance - CoinsPaid responds you with the transaction object.
3. You will receive a callback when transaction status is updated.

**Futures flow**

In order to guarantee receiving amount, rates should be fixed.

1. You send address for exchange \(If it is not necessary to receive exchange rates you can skip this step\)
2. You receive rates and their fixation time 
3. To confirm futures You have to send exchange pair and amount due. 
4. When new deposit is arriving, CoinsPaid converts all arriving funds to destination funds, and sends notifications as in regular deposit.

In case if received and sent amounts don't equal, CP converts it like normal deposit with exchange.

Limit for this operation is 10 000 EUR**.**

### **Invoice flow**

This feature provides an opportunity to make out an invoice for a B2B or B2C client to a specified amount. Detailed information can be found [here](invoices.md).

## API Endpoints

{% api-method method="get" host="https://app.coinspaid.com/api" path="/v2/ping" %}
{% api-method-summary %}
Ping
{% endapi-method-summary %}

{% api-method-description %}
Test if API is up and running and your authorization is working
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
OK
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="info" %}
Body must be a valid json object or array, example: {}
{% endhint %}

{% api-method method="post" host="https://app.coinspaid.com/api" path="/v2/currencies/list" %}
{% api-method-summary %}
Get list of supported currencies
{% endapi-method-summary %}

{% api-method-description %}
Get all supported currencies
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "data": [
    {
      "id": 1,
      "type": "crypto",
      "currency": "BTC",
      "minimum_amount": "0.001",
      "deposit_fee_percent": "0.99",
      "withdrawal_fee_percent": "0",
      "precision": 8
    }
  ]
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.coinspaid.com/api" path="/v2/currencies/pairs" %}
{% api-method-summary %}
Get list of exchangeable currency pairs
{% endapi-method-summary %}

{% api-method-description %}
Get list of currency pairs if no parameters passed.  
Get particular pair and its price if currency parameters are passed.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="currency\_from" type="string" required=false %}
Filter by currency ISO that exchanges from, example: **BTC**
{% endapi-method-parameter %}

{% api-method-parameter name="currency\_to" type="string" required=false %}
Filter by currency ISO that can be converted to, example: **EUR**
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```text
{
  "data": [
    {
      "currency_from": {
        "currency": "BTC",
        "type": "crypto",
        "min_amount": "0.00300000",
        "min_amount_deposit_with_exchange": "0.00010000"
      },
      "currency_to": {
        "currency": "EUR",
        "type": "fiat"
      },
      "rate_from": "1",
      "rate_to": "8795.80000000"
    },
    {
      "currency_from": {
        "currency": "EUR",
        "type": "fiat",
        "min_amount": "25.00000000"
      },
      "currency_to": {
        "currency": "BTC",
        "type": "crypto"
      },
      "rate_from": "1",
      "rate_to": "8885.72951839"
    },
    {
      "currency_from": {
        "currency": "BCH",
        "type": "crypto",
        "min_amount": "0.00020000",
        "min_amount_deposit_with_exchange": "0.00100000"
      },
      "currency_to": {
        "currency": "USD",
        "type": "fiat"
      },
      "rate_from": "1",
      "rate_to": "332.28264751"
    },
    {
      "currency_from": {
        "currency": "USD",
        "type": "fiat",
        "min_amount": "30.00000000"
      },
      "currency_to": {
        "currency": "BCH",
        "type": "crypto"
      },
      "rate_from": "1",
      "rate_to": "335.94361522"
    }
  ]
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```text
{
    "errors": {
        "currency_from": "The selected currency from is invalid.",
        "currency_to": "The selected currency to is invalid."
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.coinspaid.com/api" path="/v2/accounts/list" %}
{% api-method-summary %}
Get list of balances
{% endapi-method-summary %}

{% api-method-description %}
Get list of all the balances \(including zero balances\).
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
    "data": [
        {
            "currency": "DOGE",
            "type": "crypto",
            "balance": "13234.91276375"
        },
        {    
            "currency": "ADA",
            "type": "crypto",
            "balance": "7272.36400468"
        },
        {
            "currency": "EUR",
            "type": "fiat",
            "balance": "5973.97568920"
        },
        {
            "currency": "USDT",
            "type": "crypto",
            "balance": "0.00000000"
        }
    ]
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.coinspaid.com/api" path="/v2/addresses/take" %}
{% api-method-summary %}
Receive cryptocurrency
{% endapi-method-summary %}

{% api-method-description %}
Take address for depositing crypto and \(it depends on specified params\) exchange from crypto to fiat on-the-fly.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="foreign\_id" type="string" required=true %}
Your info for this address, will returned as reference in Address responses, example: **user-id:2048**
{% endapi-method-parameter %}

{% api-method-parameter name="currency" type="string" required=true %}
ISO of currency to receive funds in, example: **BTC**
{% endapi-method-parameter %}

{% api-method-parameter name="convert\_to" type="string" required=false %}
If you need auto exchange all incoming funds for example to **EUR**, specify this param as EUR or any other supported currency ISO, to see list of pairs see previous method.
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=201 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```text
{
  "data": {
    "id": 1,
    "currency": "BTC",
    "convert_to": "EUR",
    "address": "12983h13ro1hrt24it432t",
    "tag": "tag-123",
    "foreign_id": "user-id:2048"
  }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```text
{
    "errors": {
        "foreign_id": "The foreign id field is required."
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="info" %}
Business logic for using cryptocurrency as a deposit method:  
You are willing to let your customer fund his EUR balance on your platform or website. You will have to generate an address in the desired cryptocurrency and specify EUR as a "convert\_to" currency. This will allow you to let your Customer pay if his favorite currency and fund his balance in EUR. At the same time you will see respective EUR amount in your CoinsPaid merchant account.  
**Hint:** you don't have to generate new address for this customer anymore, address can be reused unlimited amount of times.
{% endhint %}

![Example of the customer facing interface for Deposits. ](../../.gitbook/assets/qr_api_2.png)

* Make sure to use Bitcoin URI format bitcoin: in QR. Works the same way as "mailto:".
* We do recommend making this QR clickable as customers may have a wallet set up on their computer or mobile phone.
* We recommend specifying approximate current exchange rate.

{% api-method method="post" host="https://app.coinspaid.com/api" path="/v2/withdrawal/crypto" %}
{% api-method-summary %}
Withdraw cryptocurrency
{% endapi-method-summary %}

{% api-method-description %}
Withdraw in crypto to any specified address. You can send Cryptocurrency from your Fiat currency balance by using "convert\_to" parameter.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="foreign\_id" type="string" required=true %}
Unique foreign ID in your system, example: "**122929**"
{% endapi-method-parameter %}

{% api-method-parameter name="amount" type="string" required=true %}
Amount of funds to withdraw, example: "**3500**"
{% endapi-method-parameter %}

{% api-method-parameter name="currency" type="string" required=true %}
Currency ISO to be withdrawn, example: "**EUR**"
{% endapi-method-parameter %}

{% api-method-parameter name="convert\_to" type="string" required=false %}
If you want to auto convert for example EUR to BTC, specify this param as **BTC** or any other currency supported \(see list of exchangeable pairs API method\).
{% endapi-method-parameter %}

{% api-method-parameter name="address" type="string" required=true %}
Cryptocurrency address where you want to send funds.
{% endapi-method-parameter %}

{% api-method-parameter name="tag" type="string" required=false %}
Tag \(if it's Ripple or BNB\) or memo \(if it's Bitshares or EOS\)
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=201 %}
{% api-method-response-example-description %}
Example of success response of withdraw without conversion
{% endapi-method-response-example-description %}

```text
{
  "data": {
    "id": 1,
    "foreign_id": "122929",
    "type": "withdrawal",
    "status": "processing",
    "amount": "0.01000000",
    "sender_amount": "0.01000000",
    "sender_currency": "ETH",
    "receiver_amount": "0.01000000",
    "receiver_currency": "ETH"
  }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```text
{
  "errors": {
    "amount": "The amount must be at least 0.001."
  }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="info" %}
Recommendation: ask customer to specify his payout address even before allowing him to deposit, this will improve experience and help to avoid mistakes with withdrawals in future.
{% endhint %}

{% hint style="info" %}
Business logic for using cryptocurrency as a withdrawal method:  
Your customer requests a payout in Cryptocurrency from his EUR balance on your platform. You have to specify withdrawal **amount in EUR** and **specify Cryptocurrency and its destination** \(sometimes it is not only Address\).  
Example of such request &lt;_Send 3500 EUR to Bitcoin 3D2V3tushw7VLJYnK6vZVDpNcNmEG2a7QK_"&gt;.
{% endhint %}

{% api-method method="post" host="https://app.coinspaid.com/api" path="/v2/exchange/calculate" %}
{% api-method-summary %}
Calculate exchange rates
{% endapi-method-summary %}

{% api-method-description %}
Get info about exchange rates.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="receiver\_amount" type="string" required=false %}
Amount you want to calculate for getting, example: **"10".** The parameter is required when the "sender\_amount" parameter is absent
{% endapi-method-parameter %}

{% api-method-parameter name="sender\_currency" type="string" required=true %}
Currency ISO for which you want to calculate the exchange rate, example: **"BTC"**
{% endapi-method-parameter %}

{% api-method-parameter name="receiver\_currency" type="string" required=true %}
Currency ISO to be exchanged, example: **"EUR"**
{% endapi-method-parameter %}

{% api-method-parameter name="sender\_amount" type="string" required=false %}
Amount you want to calculate, example: **"3".** The parameter is required when the "receiver\_amount" parameter is absent
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```text
{
  "data": {
    "sender_amount": 1,
    "sender_currency": "BTC",
    "receiver_amount": "8549.81680000",
    "receiver_currency": "EUR",
    "fee_amount": "85.49816800",    
    "fee_currency": "BTC",
    "price": "8549.81680000",
    "ts_fixed": 1564293159,
    "ts_release": 1564293219,
    "fix_period": 60
  }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```text
{
  "errors": {
    "sender_currency": "Exchange is unavailable for given currencies"
  }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.coinspaid.com/api" path="/v2/exchange/fixed" %}
{% api-method-summary %}
Exchange on fixed exchange rate
{% endapi-method-summary %}

{% api-method-description %}
Make exchange on a given fixed exchange rate.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="sender\_currency" type="string" required=true %}
Currency ISO which you want to exchange, example: **"LTC"**
{% endapi-method-parameter %}

{% api-method-parameter name="receiver\_currency" type="string" required=true %}
Currency ISO to be exchanged, example: **"USD"**
{% endapi-method-parameter %}

{% api-method-parameter name="sender\_amount" type="string" required=true %}
Amount you want to exchange, example: **"6.5"**
{% endapi-method-parameter %}

{% api-method-parameter name="foreign\_id" type="string" required=true %}
Unique foreign ID in your system, example: **"134453"**
{% endapi-method-parameter %}

{% api-method-parameter name="price" type="string" required=true %}
Exchange rate price on which exchange will be placed, example: **"89.75202000"**
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=201 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```text
{
  "data": {
     "id": 2687667,
     "foreign_id": "knwi24op9",
     "type": "exchange",
     "sender_amount": "0.01",
     "sender_currency": "BTC",
     "receiver_amount": "63.52069015",
     "receiver_currency": "EUR",
     "fee_amount": "6.98727592",
     "fee_currency": "EUR",
     "price": "6352.06901520",
     "status": "processing"
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.coinspaid.com/api" path="/v2/exchange/now" %}
{% api-method-summary %}
Exchange regardless the exchange rate
{% endapi-method-summary %}

{% api-method-description %}
Make exchange without mentioning the price.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="sender\_currency" type="string" required=true %}
Currency ISO which you want to exchange, example: **"EUR"**
{% endapi-method-parameter %}

{% api-method-parameter name="receiver\_currency" type="string" required=true %}
Currency ISO to be exchanged, example: **"BTC"**
{% endapi-method-parameter %}

{% api-method-parameter name="sender\_amount" type="string" required=true %}
Amount you want to exchange, example: **"2"**
{% endapi-method-parameter %}

{% api-method-parameter name="foreign\_id" type="string" required=true %}
Unique foreign ID in your system, example: **"124876"**
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=201 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```text
{
  "data": {
     "id": 2687669,
     "foreign_id": "wph27bmsp81",
     "type": "exchange",
     "sender_amount": "0.001",
     "sender_currency": "BTC",
     "receiver_amount": "6.33984642",
     "receiver_currency": "EUR",
     "fee_amount": "0.69738311",
     "fee_currency": "EUR",
     "price": "6339.84642127",
     "status": "processing"
    }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```text
{
  "errors": {
    "sender_amount": "The amount must be at least 0.00300000 "
  }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.coinspaid.com/api" path="/v2/futures/rates" %}
{% api-method-summary %}
Calculate rates for futures
{% endapi-method-summary %}

{% api-method-description %}
Get info about rates for futures.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="address" type="string" required=true %}
Exchange address for which you want to calculate futures' rates
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```text
{
    "data": {
        "addresses": [
            {
                "id": 384620,
                "currency": "BTC",
                "convert_to": "USD",
                "address": "3GQwSBQErsQ863RcuvCkub6SZBPHKuwSV7",
                "tag": null,
                "foreign_id": "ds23fgk"
            }
        ],
        "ts_fixed": 1581507311,
        "ts_release": 1581513311,
        "rates": {
            "BTCUSD": {
                "5.00000000": "10312.67153000",
                "10.00000000": "10312.67153000",
                "20.00000000": "10312.67153000",
                "50.00000000": "10312.67153000",
                "100.00000000": "10312.67153000",
                "200.00000000": "10312.67153000",
                "300.00000000": "10312.67153000"
            }
        }
    }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```text
{
    "errors": {
        "address": "Address is not found, incorrect or without exchange"
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.coinspaid.com/api" path="/v2/futures/confirm" %}
{% api-method-summary %}
Confirm futures transaction
{% endapi-method-summary %}

{% api-method-description %}
Confirm futures transaction.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="address" type="string" required=true %}
Exchange address for which you want to confirm futures
{% endapi-method-parameter %}

{% api-method-parameter name="sender\_currency" type="string" required=true %}
Currency ISO which you want to exchange, example: **"BTC"**
{% endapi-method-parameter %}

{% api-method-parameter name="receiver\_currency" type="string" required=true %}
Currency ISO to be exchanged, example: **"EUR"**
{% endapi-method-parameter %}

{% api-method-parameter name="receiver\_amount" type="string" required=true %}
Amount you want to receive
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```text
{
    "data": {
        "futures_id": 92,
        "sender_currency": "BTC",
        "receiver_currency": "USD",
        "fee_currency": "USD",
        "price": "10299.94946000",
        "address": {
            "id": 384620,
            "currency": "BTC",
            "convert_to": "USD",
            "address": "3GQwSBQErsQ863RcuvCkub6SZBPHKuwSV7",
            "tag": null,
            "foreign_id": "ds23fgk"
        },
        "sender_amount": "0.00292",
        "receiver_amount": "30.00000000",
        "fee_amount": "1.50000000",
        "ts_fixed": 1581506566,
        "ts_release": 1581512566
    }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```text
{
    "errors": {
        "address": "Address is not found, incorrect or without exchange. Error can be related to incorrect sender or receiver currencies."
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

## Transaction statuses

| Status | Meaning |
| :--- | :--- |
| confirmed | **Final**. You are safe to process this transaction |
| not\_confirmed | Transaction is not yet confirmed. |
| cancelled | **Final**. This transaction is a double spend or cancelled withdrawal. Pay attention to this transaction. |

## Transitions

| Transaction | Transition |
| :--- | :--- |
| Successful deposit | not\_confirmed -&gt; confirmed |
| Unsuccessful deposit | not\_confirmed -&gt; cancelled |
| Successful withdrawal | confirmed |
| Unsuccessful withdrawal | cancelled |

## Deposit Callbacks

To provide authentication for the callback, coinspaid API signs the POST your api key and secret:

1. **X-Processing-Key** – Your public key
2. **X-Processing-Signature** – POST body, signed by the your secret key HMAC-SHA512

{% tabs %}
{% tab title="BTC" %}
```text
{
    "id": 1,
    "type": "deposit",
    "crypto_address": {
        "id": 1,
        "currency": "BTC",
        "address": "39mFf3X46YzUtfdwVQpYXPCMydc74ccbAZ",
        "foreign_id": "user-id:2048",
        "tag": null
    },
    "currency_sent": {
        "currency": "BTC",
        "amount": "6.53157512"
    },
    "currency_received": {
        "currency": "BTC",
        "amount": "6.53157512",
        "amount_minus_fee": "6.5119800"
    },
    "transactions": [
        {
            "id": 1,
            "currency": "BTC",
            "transaction_type": "blockchain",
            "type": "deposit",
            "address": "39mFf3X46YzUtfdwVQpYXPCMydc74ccbAZ",
            "tag": null,
            "amount": "6.53157512",
            "txid": "3950ad8149421a850d01dff88f024810e363ac18c9e8dd9bc0b9116e7937ad93",
            "riskscore": "0.5",
            "confirmations": 3
        }
    ],
    "fees": [
        {
            "type": "deposit",
            "currency": "BTC",
            "amount": "0.01959472"
        }
    ],
    "error": "",
    "status": "confirmed"
}
```
{% endtab %}

{% tab title="ETH" %}
```text
{
    "id": 2686563,
    "type": "deposit",
    "crypto_address": {
        "id": 381738,
        "currency": "ETH",
        "address": "0xd61180ff0cf74dc3ee8e264751f18c47060729b9",
        "tag": null,
        "foreign_id": "991904"
    },
    "currency_sent": {
        "currency": "ETH",
        "amount": "0.01000000"
    },
    "currency_received": {
        "currency": "ETH",
        "amount": "0.01000000",
        "amount_minus_fee": "0.00939500"
    },
    "transactions": [
        {
            "id": 714657,
            "currency": "ETH",
            "transaction_type": "blockchain",
            "type": "deposit",
            "address": "0xd61180ff0cf74dc3ee8e264751f18c47060729b9",
            "tag": null,
            "amount": "0.01000000",
            "txid": "0x6b353da88a8ba2df4926c1ccc58991f484a683ba57ec3dde70e812b5c8c7fa1d",
            "confirmations": "9"
        }
    ],
    "fees": [
        {
            "type": "transfer",
            "currency": "ETH",
            "amount": "0.00010500"
        },
        {
            "type": "deposit",
            "currency": "ETH",
            "amount": "0.00050000"
        }
    ],
    "error": "",
    "status": "confirmed"
}
```
{% endtab %}

{% tab title="ERC20" %}
```text
{
    "id": 2686567,
    "type": "deposit",
    "crypto_address": {
        "id": 382357,
        "currency": "NNM",
        "address": "0x19722627885da3fff134dd36de0a14898c8e053b",
        "tag": null,
        "foreign_id": "31"
    },
    "currency_sent": {
        "currency": "NNM",
        "amount": "0.06000000"
    },
    "currency_received": {
        "currency": "NNM",
        "amount": "0.06000000",
        "amount_minus_fee": "0.05910000"
    },
    "transactions": [
        {
            "id": 714662,
            "currency": "NNM",
            "transaction_type": "blockchain",
            "type": "deposit",
            "address": "0x19722627885da3fff134dd36de0a14898c8e053b",
            "tag": null,
            "amount": "0.06000000",
            "txid": "0x835c9f286a311bc9df8c94c2771cadf1d91e6967039358a44e24c56115dc59a1",
            "confirmations": "7"
          }
    ],
    "fees": [
        {
            "type": "transfer",
            "currency": "ETH",
            "amount": "0.00037336"
        },
        {
            "type": "deposit",
            "currency": "NNM",
            "amount": "0.00090000"
        }
    ],
    "error": "",
    "status": "confirmed"
}
```
{% endtab %}

{% tab title="Deposit BTC with exchange to EUR" %}
```text
{
    "id": 2686510,
    "type": "deposit_exchange",
    "crypto_address": {
        "id": 382270,
        "currency": "BTC",
        "convert_to": "EUR",
        "address": "31vnLqxVJ1iShJ5Ly586q8XKucECx12bZS",
        "tag": null,
        "foreign_id": "13a"
    },
    "currency_sent": {
        "currency": "BTC",
        "amount": "0.01000000"
    },
    "currency_received": {
        "currency": "EUR",
        "amount": "84.17070222",
        "amount_minus_fee": "79.96216711"
    },
    "transactions": [
        {
            "id": 714576,
            "currency": "BTC",
            "transaction_type": "blockchain",
            "type": "deposit",
            "address": "31vnLqxVJ1iShJ5Ly586q8XKucECx12bZS",
            "tag": null,
            "amount": "0.01000000",
            "txid": "3a491da90a1ce5a318d0aeff6867ab98a03219abae29ed68d702291703c3538b",
            "riskscore": "0.42",
            "confirmations": "1"
        },
        {
            "id": 714577,
            "currency": "BTC",
            "currency_to": "EUR",
            "transaction_type": "exchange",
            "type": "exchange",
            "amount": "0.01000000",
            "amount_to": "84.17070222"
        }
    ],
    "fees": [
        {
            "type": "exchange",
            "currency": "EUR",
            "amount": "4.20853511"
        }
    ],
    "error": "",
    "status": "confirmed"
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**"riskscore"** is used for coin compliance analysis and to reveal and track malicious behavior. Available for deposits and deposits with exchange in BTC, BCH, ETH.
{% endhint %}

## Withdrawal Callbacks

{% tabs %}
{% tab title="BTC" %}
```text
{
    "id": 1,
    "foreign_id": "10",
    "type": "withdrawal",
    "crypto_address": {
        "id": 1,
        "currency": "BTC",
        "address": "115Mn1jCjBh1CNqug7yAB21Hq2rw8PfmTA",
        "tag": null
    },
    "currency_sent": {
        "currency": "BTC",
        "amount": "0.02000000"
    },
    "currency_received": {
        "currency": "BTC",
        "amount": "0.02000000"
    },
    "transactions": [
        {
            "id": 1,
            "currency": "BTC",
            "transaction_type": "blockchain",
            "type": "withdrawal",
            "address": "115Mn1jCjBh1CNqug7yAB21Hq2rw8PfmTA",
            "tag": null,
            "amount": "0.02",
            "txid": "bb040d895ef7141ea0b06b04227d8f5dd4ee12d5b890e6e5633f6439393a666b",
            "confirmations": 3
        }
    ],
    "fees": [
        {
            "type": "mining",
            "currency": "BTC",
            "amount": "0.00007402"
        },
        {
            "type": "withdrawal",
            "currency": "BTC",
            "amount": "0.00002000"
        }
    ],
    "error": "",
    "status": "confirmed"
}
```
{% endtab %}

{% tab title="ETH" %}
```text
{
    "id": 2686565,
    "foreign_id": "23",
    "type": "withdrawal",
    "crypto_address": {
        "id": 381460,
        "currency": "ETH",
        "address": "0x2D6CA312567986C08CC4eF3F706136D1c9eF0321",
        "tag": null
    },
    "currency_sent": {
        "currency": "ETH",
        "amount": "0.01000000"
    },
    "currency_received": {
        "currency": "ETH",
        "amount": "0.01000000"
    },
    "transactions": [
        {
            "id": 714660,
            "currency": "ETH",
            "transaction_type": "blockchain",
            "type": "withdrawal",
            "address": "0x2D6CA312567986C08CC4eF3F706136D1c9eF0321",
            "tag": null,
            "amount": "0.01000000",
            "txid": "0x1ccb9fa0ef5e8cf7d4cc2a23fe8119170a2a3d08fba36036665a12c88d7bcccb",
            "confirmations": "0"
        }
    ],
    "fees": [
        {
            "type": "mining",
            "currency": "ETH",
            "amount": "0.00010500"
        },
        {
            "type": "withdrawal",
            "currency": "ETH",
            "amount": "0.00010000"
        }
    ],
    "error": "",
    "status": "confirmed"
}
```
{% endtab %}

{% tab title="ERC20" %}
```text
{
    "id": 2686572,
    "foreign_id": "5",
    "type": "withdrawal",
    "crypto_address": {
        "id": 381734,
        "currency": "NNM",
        "address": "0x2D6CA312567986C08CC4eF3F706136D1c9eF0321",
        "tag": null
    },
    "currency_sent": {
        "currency": "NNM",
        "amount": "4.45600000"
    },
    "currency_received": {
        "currency": "NNM",
        "amount": "4.45600000"
    },
    "transactions": [
        {
            "id": 714670,
            "currency": "NNM",
            "transaction_type": "blockchain",
            "type": "withdrawal",
            "address": "0x2D6CA312567986C08CC4eF3F706136D1c9eF0321",
            "tag": null,
            "amount": "4.45600000",
            "txid": "0x884ad10cc60dfe0d6fdc776b541c5c5efce6151886a88994bb4ba41aa9575563",
            "confirmations": "0"
        }
    ],
    "fees": [
        {
            "type": "mining",
            "currency": "ETH",
            "amount": "0.00007480"
        },
        {
            "type": "withdrawal",
            "currency": "NNM",
            "amount": "0.04456000"
        }
    ],
    "error": "",
    "status": "confirmed"
}
```
{% endtab %}

{% tab title="Withdraw EUR with exchange to BTC" %}
```text
{
    "id": 1,
    "foreign_id": "20",
    "type": "withdrawal_exchange",
    "crypto_address": {
        "id": 1,
        "currency": "EUR",
        "convert_to": "BTC",
        "address": "1K2btnZ8cqNFBPhaq729Mdj8W6G3w2nBbL",
        "tag": null
    },
    "currency_sent": {
        "currency": "EUR",
        "amount": "381"
    },
    "currency_received": {
        "currency": "BTC",
        "amount": "0.10882300"
    },
    "transactions": [
        {
            "id": 1,
            "currency": "EUR",
            "currency_to": "BTC",
            "transaction_type": "exchange",
            "type": "exchange",
            "amount": 381,
            "amount_to": 0.10882300
        },
        {
            "id": 1,
            "currency": "BTC",
            "transaction_type": "blockchain",
            "type": "withdrawal",
            "address": "1K2btnZ8cqNFBPhaq729Mdj8W6G3w2nBbL",
            "tag": null,
            "amount": 0.10882300,
            "txid": "aa3345b96389e126f1ce88a670d1b1e38f2c3f73fb3ecfff8d9da1b1ce6964a6",
            "confirmations": 3
        }
    ],
    "fees": [
        {
            "type": "exchange",
            "currency": "EUR",
            "amount": "3.04800000"
        },
        {
            "type": "mining",
            "currency": "EUR",
            "amount": "0.04489780"
        }
    ],
    "error": "",
    "status": "confirmed"
}
```
{% endtab %}
{% endtabs %}

## Exchange Callbacks

{% tabs %}
{% tab title="Buy BTC for EUR" %}
```text
{
    "id": 2686900,
    "type": "exchange",
    "currency_sent": {
        "currency": "EUR",
        "amount": "26.75248865"
    },
    "currency_received": {
        "currency": "BTC",
        "amount": "0.00300000"
    },
    "transactions": [
        {
            "id": 715072,
            "currency": "EUR",
            "currency_to": "BTC",
            "transaction_type": "exchange",
            "type": "exchange",
            "amount": "26.75248865",
            "amount_to": "0.00300000"
        }
    ],
    "fees": [
        {
            "type": "exchange",
            "currency": "EUR",
            "amount": "0.26752489"
        }
    ],
    "error": "",
    "status": "confirmed"
}
```
{% endtab %}

{% tab title="Sell BTC for USD" %}
```text
{
    "id": 2686901,
    "type": "exchange",
    "currency_sent": {
        "currency": "BTC",
        "amount": "0.00300000"
    },
    "currency_received": {
        "currency": "USD",
        "amount": "29.50408884"
    },
    "transactions": [
        {
            "id": 715072,
            "currency": "BTC",
            "currency_to": "USD",
            "transaction_type": "exchange",
            "type": "exchange",
            "amount": "0.00300000",
            "amount_to": "29.50408884"
        }
    ],
    "fees": [
        {
            "type": "exchange",
            "currency": "USD",
            "amount": "0.29504089"
        }
    ],
    "error": "",
    "status": "confirmed"
}
```
{% endtab %}
{% endtabs %}

## Futures Callback

{% tabs %}
{% tab title="Buy BTC for ETH" %}
```text
{
   "id":2688873,
   "type":"deposit_exchange",
   "crypto_address":{
      "id":384708,
      "currency":"ETH",
      "convert_to":"BTC",
      "address":"0x4b41a526d3d12de36bdf969e7b70fd0bd2e0d263",
      "tag":null,
      "foreign_id":"hfjs781"
   },
   "currency_sent":{
      "currency":"ETH",
      "amount":"0.11129846"
   },
   "currency_received":{
      "currency":"BTC",
      "amount":"0.00300000",
      "amount_minus_fee":"0.00299944"
   },
   "transactions":[
      {
         "id":717555,
         "currency":"ETH",
         "transaction_type":"blockchain",
         "type":"deposit",
         "address":"0x4b41a526d3d12de36bdf969e7b70fd0bd2e0d263",
         "tag":null,
         "amount":"0.11129846",
         "txid":"0x19f8094e12dfc6cb14910d6057269d10f39dfdc7c8b0d0e22b789c3e5d03b9e5",
         "confirmations":"13"
      },
      {
         "id":717556,
         "currency":"ETH",
         "currency_to":"BTC",
         "transaction_type":"exchange",
         "type":"exchange",
         "amount":"0.11129846",
         "amount_to":"0.00300000"
      }
   ],
   "fees":[
      {
         "type":"transfer",
         "currency":"BTC",
         "amount":"0.00000056"
      },
      {
         "type":"exchange",
         "currency":"ETH",
         "amount":"0.00667791"
      }
   ],
   "error":"",
   "status":"confirmed",
   "futures_id":95,
   "transaction_id":2688873
}
```
{% endtab %}
{% endtabs %}

## Callbacks with other statuses

{% tabs %}
{% tab title="Not confirmed" %}
```text
{
    "id": 2686579,
    "type": "deposit",
    "crypto_address": {
        "id": 381711,
        "currency": "BTC",
        "address": "2N9zXNdiT8ucZp7zZSrucqYGCD6xYF8F3di",
        "tag": null,
        "foreign_id": "991904"
    },
    "currency_sent": {
        "currency": "BTC",
        "amount": "0.01000000"
    },
    "currency_received": {
        "currency": "BTC",
        "amount": "0.01000000",
        "amount_minus_fee": "0.01000000"
    },
    "transactions": [
        {
            "id": 714680,
            "currency": "BTC",
            "transaction_type": "blockchain",
            "type": "deposit",
            "address": "2N9zXNdiT8ucZp7zZSrucqYGCD6xYF8F3di",
            "tag": null,
            "amount": "0.01000000",
            "txid": "998c4d9bb7145aafd88658b292f41fe05973c217f7adcd6052bcafe2309e7e02",
            "confirmations": "0"
        }
    ],
    "fees": [],
    "error": "",
    "status": "not_confirmed"
}
```
{% endtab %}

{% tab title="Cancelled" %}
```text
{
    "id": 2686580,
    "foreign_id": "123",
    "type": "withdrawal",
    "crypto_address": {
        "id": 382362,
        "currency": "ETH",
        "address": "12345",
        "tag": null
    },
    "transactions": [
        {
            "id": 714681,
            "currency": "ETH",
            "transaction_type": "blockchain",
            "type": "withdrawal",
            "address": "12345",
            "tag": null,
            "amount": "1.00000000",
            "txid": null,
            "confirmations": "0"
        }
    ],
    "fees": [],
    "error": "Invalid params: expected a hex-encoded hash with 0x prefix.",
    "status": "cancelled"
}
```
{% endtab %}
{% endtabs %}

