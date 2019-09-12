# Confirmations and limits

Here you can find confirmation times for crypto deposits and min/max limits.

{% hint style="danger" %}
Please note that **min limits** for exchange operations must be **obtained via API** \(see [this API call](api-documentation/v2.md#get-list-of-exchangeable-currency-pairs)\) because they may vary dynamically. Limits for min deposits and withdrawals can [must be obtained via API](api-documentation/v2.md#get-list-of-supported-currencies) too, though they do not change often.
{% endhint %}

| Currency | Confirmations | Min deposit | Min withdrawal | Notes |
| :--- | :--- | :--- | :--- | :--- |
| ADA | 15 | 0.01 | 0.01 | [explorer](https://cardanoexplorer.com/) |
| BTC | 0/1 | 0.0001 | 0.001 | [explorer](https://www.blockchain.com/explorer) |
| BCH | 6 | 0.001 | 0.001 | [explorer](https://explorer.bitcoin.com/bch) |
| LTC | 6 | 0.01 | 0.01 | [explorer](https://live.blockcypher.com/ltc/) |
| DOGE | 6 | 0.01 | 0.01 | [explorer](https://live.blockcypher.com/doge/) |
| ETH | 25 | 0.01 | 0.01 | [explorer](http://etherscan.io/) |
| ETC | 25 | 0.01 | 0.01 | [explorer](https://gastracker.io/) |
| DASH | 6 | 0.01 | 0.01 | [explorer](https://live.blockcypher.com/dash/) |
| ZEC | 24 | 0.01 | 0.01 | [explorer](https://explorer.zcha.in/) |
| XMR | 10 | 0.01 | 0.01 | [explorer](https://moneroblocks.info/) |
| XRP | almost instant | 0.01 | 0.01 | [explorer](https://xrpcharts.ripple.com/#/) |
| NEO | 10 | 1 | 1 | [explorer](https://neotracker.io/) |
| USDT | 6 | 0.0001 | 0.001 | [explorer](https://omniexplorer.info/) |
| OMNIBTC | 6 | 0.001 | 0.001 | [explorer](https://www.blockchain.com/explorer) |
| ERC20 | 25 | 0.01 | 0.01 | [explorer](http://etherscan.io/) |

{% hint style="info" %}
Number of confirmations can be more than value in table in cases when several blocks are released in a short period of time.
{% endhint %}

