---
title: 7-CONNECT Guide
description: "Integration Guide for 7-CONNECT"
date: "2018-03-19T00:00:00+08:00"
draft: false
toc: false
bref: "Integration Guide for 7-CONNECT"
---

### Use Case

7-CONNECT is 7-Eleven's platform for bills, tickets or e-commerce that enables cash payment convenience for your customers. Customers will be able to pay for online purchases at any 7-Eleven store.

A payment slip may be created from the website, mobile app or CLiQQ kiosk and presented to the cashier at the counter for payment. At the POS, a two-step process allows the merchant to (1) VERIFY if the payment transaction is still valid and (2) CONFIRM the payment acceptance.

7-CONNECT features the following:

* 24/7 cash payment at all 7-Eleven stores
* Increases your market beyond credit card holders
* Easier and faster than bank deposits
* Zero risk of fraud and chargebacks
* Real time notification of payment to your transaction systems

As a merchant, you will have access to new customers who do not have credit cards or who refuse to use them online. This opens up opportunities for you. There are no chargebacks or fraud to worry about. All completed transactions are guaranteed to be remitted to you.

### API Integration

* The 12 digit barcode must be numeric and rendered in Code-128-C format
* When displaying the payment slip on a mobile device, screen brightness must be set to maximum for best scanner readability.
* There will be two calls to your API:
  * **VERIFY** - This is is your chance to reject payment acceptance if you want to validate the account number, customer status, etc. You may opt to turn validation off. Your API must respond within 20 seconds, otherwise, the POS will reject the transaction.
  * **CONFIRM** - At this point, cash payment has been accepted and an API call to your endpoint will be initiated. If your service is down and your retry flag is enabled, the 7-CONNECT server will retry up to 10 times with an interval of 10 minutes.

<a href="https://dev.philseven.com/" class="button">View API</a>

### Settlement

A dashboard is available for you to view realtime status and download csv reports for posted transactions.

{{< figure src="01-login.png" width="480" class="text-center" caption="7-CONNECT Dashboard" >}}

A transaction can have one of the following status:

* **PENDING** - available for cash payment at store
* **EXPIRED** - payment will no longer be accepted at store
* **PAID** - payment has been accepted at store but not yet posted to merchant endpoint
* **POSTED** - payment has been accepted at store and posted to merchant endpoint

Only transactions with a POSTED status will be included in the funds to be transferred to the merchant's bank account.