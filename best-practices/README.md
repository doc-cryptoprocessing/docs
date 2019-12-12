# Integration guide

## Intro

When we are talking about cryptocurrencies as a payment method, we should keep in mind that it's payment workflow differs from classic payment methods like bank cards or e-wallets. As a result, one who are going to use this payment method in his web-service or application have to know small tips how to make the payment process comfortable and simple.

In this section we would like to share our knowledges how to work with cryptocurrencies efficiently. We also described there the most important information about implementation scenarios of Coinspad API.

## Deposits

Crypto deposits are similar to bank transfers. If one knows the recipient's bank account number, he can send him the money any time. This approach differs from classic payment methods, because the transaction could not be created before the money will be appeared on the bank account. The same way web-service can not expect that crypto deposit transaction will be initiated from the "payment form" every time, because in general there is no such term for cryptocurrencies. Unlike the classical approach, web-service should handle callbacks from payment provider with the information about received deposits. When web-service receives the first callback it creates the transaction. After the transaction will be confirmed by the payments provider the second callback will inform the web-service about the status of the transaction. Then web-service can provide the user with specific service.

{% hint style="warning" %}
#### Attention!

When user gets an address for the first time he is able to save it into his crypto wallet application. It is comfortable for the user because he doesn't need to go to "Cashier" section every time when he wants to make a deposit. That is why many users save the address in their crypto wallet application after the first deposit and then they don't need to go to the payment form every time.

As cryptocurrencies are based on the blockchain technologies, you can not delete the address if it is already has been created and has been written into the blockchain.

If the user knows this address he always can send the money there. Because it is not necessary  for the user to interact with the payment form when he would like to send the money.

Even if you show the users new addresses every time, they always can make the deposit to the older one. And they expect that they will see their money on your website. 
{% endhint %}

### Common deposit workflow

The most used scenario for receiving crypto is "deposit with exchange". Coinspaid provides web-services with on-the-fly exchange solution. It means that all funds that were received from the users can be automatically converted into fiat currencies in order to avoid cryptocurrencies fluctuations and exchange rate inconsistencies.

In case of regular deposits without exchange the workflow will be the same except of conversion operation.

> #### Algorithm
>
> 1. User has EUR balance on your site
> 2. User uses some kind of “deposit” option on your site and choose BTC as a payment method to use for the payment
> 3. Website shows a deposit BTC address to the user
> 4. User scans QR code or copy the address and goes to his crypto wallet application
> 5. When user sends BTC to this address, our system makes automatic exchange of received funds from BTC to EUR and your system gets the callback with the information about the deposit, including transaction's status, currencies pair, amount, fees and so on
> 6. You add the amount from the callback to user’s EUR balance

{% hint style="danger" %}
When you show a deposit address to a user, it is important to notice the user about exchange rates, deposit limits and chosen cyrptocurrency. Besides this you may want to warn the user that:

* all deposits below the limits or incorrect addresses will be lost

Here you can find the information about deposit and withdrawal limits:

* [Deposit and withdrawal limits](../confirmations-and-limits.md)

Sometime users make mistakes and send the money using specific cryptocurrency to the address from different blockchain. These funds will be lost in most of cases. The funds could be particularly restored according to "Crosschain recovery policy":

* [Crosschain recovery policy](../crosschain-recovery-policy.md)
{% endhint %}

In order to increase the conversion rate for crypto payments we have a special guide how you can develop your payment form and provide the user with the best experience of crypto payments. There are good recommendations how to build friendly and comfrotable interaction with your interface for the user. Here you can find how it can be implemented:

{% page-ref page="qr-codes-implementation.md" %}

{% hint style="info" %}
#### Hint 1

Usually our merchants generate all crypto addresses at the moment when a new user has been created. In order to do this you should use **"/v2/addresses/take"** method from our API.  
You can define what currency should be used for receiving the funds from the user. And if it necessary, into what fiat currency received funds should be converted.

In most cases it is useful to send us your user's ID as a **"foreign\_id"** parameter. We will link it to the address and will send in the callbacks. Such a way you will be able to understand what user made the deposit.
{% endhint %}

{% hint style="warning" %}
#### Attention

Some cryptocurrencies require additional parameter to be defined when user makes a deposit. For example, user have to input a velue into **"Memo"** field when he makes a deposit from his wallet application using **XRP, BNB** or some other cryptocurrencies as the payment method.

If it is necessary for specific currency, we send parameter **"tag"** in the response on **"/v2/addresses/take"** API call. If your system gets this parameter it have to show it's value to the user. In other case received funds will be lost.
{% endhint %}

{% hint style="info" %}
#### Hint 2

In order to get the information about current exchange rates for different currencies you can use **"/v2/currencies/pairs"** API method.

If you need to calculate exact amounts corresponding to current exchange rates try **"/v2/exchange/calculate"** method.
{% endhint %}

{% hint style="info" %}
#### Hint 3

In case you would like to hold exchange rate for 10 minutes in order to receive specified amount from the user after converting the funds you may want to use these methods: **"/v2/futures/rates"** + **"/v2/futures/confirm"**
{% endhint %}

{% hint style="info" %}
#### Hint 4

As we keep a small amount of incoming funds as our fees, we provide you with the information about fees in the callback.  
It is on your choice what amount you will add to user's balance: including fees or not.  
{% endhint %}

## Withdrawals

To the opposite of the deposits, sending cryprocurrencies to the users is similar to classic payment methods. User fill the form where he provides the address and the amount of the withdrawal. After that web-service creates the transaction and send a withdrawal request to the payment provider. When transaction is confirmed by payment provider web-service decreases user's balance on the website.

### Common withdrawal workflow

The most used scenario for sending crypto is "withdrawal with exchange". Coinspaid provides web-services with on-the-fly exchange solution. It means that the funds that is stored on merchant's fiat balances can be automatically converted into crypto currencies before been sent to the user's crypto wallet.

In case of regular withdrawals without exchange the workflow will be the same except of conversion operation.

> #### Algotihm
>
> 1. You have EUR balance in our system
> 2. User wants to get a withdrawal from his EUR balance on your site to his BTC wallet
> 3. User uses some kind of “withdrawal” option on your site, choose BTC as the currency to use for the payout and fill the payout form with his BTC Address where he wants to receive the money and the withdrawal amount
> 4. After that your system makes the request to our processing
> 5. Our system makes automatic exchange from EUR to BTC, sends money to user’s crypto address and your system gets the callback with transaction parameters, including transaction's status, currencies pair, amount, fees and so on
> 6. You decrease user's EUR balance for the transaction amount

{% hint style="info" %}
#### Hint 5

In order to make a withdrawal you should use **"/v2/withdrawal/crypto"** method from our API.  
You can define what currency should be used to send the funds. And if it necessary, from what fiat currency the funds should be converted before sending to the user.
{% endhint %}

{% hint style="warning" %}
#### Attention

Unlike "**/v2/addresses/take**" method, you should use unique "foreign\_id" every time you make a withdrawal request to our system. 
{% endhint %}

## Callbacks

Every time, when user makes a deposit or our system sends a withdrawal into the blockchain, you will receive the callback. Callback contains all important information about the transaction:

* status
* currency
* amount
* blockchain transaction's hash
* address
* fees
* number of confirmations
* ...and so on

{% hint style="warning" %}
#### Attention

As our system supports instant confirmations it means that some deposits will be confirmed in our system without confirmations in the blockchain. In this case you will receive the only one callback for the transaction. The parameter **"status**" will have the value  **"confirmed".** When we confirm the transaction we are ready to manage all the risks concerning to accepting real funds later. You should not be concerned about it.

In other case you will first receive the callback with **"not\_confirmed"** status. It will mean that we found the transaction in the mempool but we are not ready to guarantee that it will be received. When it happens you can create the transaction on your side but assign it status **"pending"**. After that you should wait for the second callback where the value of **"status"** parameter will be **"confirmed".**
{% endhint %}

{% hint style="warning" %}
#### Attention

You have to validate a signature of the callback and values of known parameters in order to avoid fraudulent activity.
{% endhint %}

In case of successful validation of the callback your system have to response with **HTTP Code: 200 OK.** No additional parameters are required in the response "body". 

Other way we will keep the callback in our sending queue and will continue the attempts according to schedule mentioned here: [Callbacks](../api-documentation/callbacks.md).

{% hint style="info" %}
#### Hint 6

You may always go to our backoffice interface and get all information about the specified callback from "Transaction info" section, including timings, count of attempts to send the callback, response and so on.

If you had some technical issues with your callback handler or long maintenance period there is a button in the "Transaction info" to reset the callback sending queue. Using this function you can resend the callback immediately.
{% endhint %}

{% hint style="warning" %}
#### Attention

There is a difference between deposit and withdrawal callbacks.

For the deposits, if user makes all deposits to the same address, you will receive the same values of **"foreign\_id"** and **"address"** parameters every time. Parameter **"address"** will show what address received money from the user. It is the address from our system.

For the withdrawals, you will receive different values of **"foreign\_id"** and **"address"** parameters in every callback. Parameter **"address"** in this case will show user's address where we sent money.
{% endhint %}

{% hint style="info" %}
#### Hint 7

When user sends you the same amount on the same address you will receive very similar callbacks. In order to understand whether you see several callbacks for the one transaction or there were more than one transaction, you can use **"ID"** parameter from the root element in the callback JSON. This parameter is unique for all transactions from our end.
{% endhint %}

{% hint style="info" %}
#### Hint 8

If a user complains that he made a deposit and doesn't see this deposit on his account we recommend you the following steps:

* check the transaction in our backoffice interface
* if it is confirmed, check "Transaction info" section to see what your system responses to our callbacks
* if we can't deliver you the callbacks, check your callback handler on your end
* if transaction is not confirmed in our system, go to blockchain explorer and check if this transaction has a minimum number of confirmations
* if it doesn't has the defined number of confirmations the user need to wait until minimum number of the confirmations will be reached
* if the transaction has the minimum number of the confirmations, please contact our support team 

Here you can find the information about the minimum number of the confirmations for each cryptocurrency:

* [Confirmations and limits](../confirmations-and-limits.md)
{% endhint %}



