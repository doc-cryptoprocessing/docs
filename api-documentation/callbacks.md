# Callbacks

Asynchronous payments are very popular with cryptocurrencies. In such deposit flow you have to generate an address and pass it to your client. This will be his deposit address which he will be reusing during his lifetime with you. In such payment flow we will notify you about any incoming payments with callbacks.

You must provide callback url from your side during onboarding, and upon processing payments, CoinsPaid will send you notifications in JSON format with all required information about transaction such as amount and status.

Callback retry schedule:  
  
1, 5, 10, 15, 20, 30, 60, 90, 120, 150, 180, 210, 240 minutes.

[API V1 callbacks specification](v1.md#callbacks)

[API V2 callbacks specification](v2.md#callbacks)

