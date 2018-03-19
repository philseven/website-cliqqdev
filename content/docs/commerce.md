---
title: CLiQQ Commerce API
description: "Use our API to send a package for customer pickup at store or drop off a package at store."
date: "2017-04-10T16:43:08+01:00"
draft: false
toc: false
bref: "Use our API to send a package for customer pickup at store or drop off a package at store"
---

### Use Case

1. You have an e-commerce site and you want to provide the customer with the option to pay and pickup the order at the nearest 7-Eleven.

2. You operate a marketplace and you want to provide the seller the option to drop off at the nearest 7-Eleven and the buyer to pay and pickup at their nearest 7-Eleven.

### Technical Notes

* This API is open to selected partners.
* A package sent through the system is limited to maximum of volume of 27,000 cubic cm (30cm x 30cm x 30cm) or 10kg.
* Delivery is to the 7-Eleven central distribution center in Pasig.

### Package Fulfillment (Prepaid) - Payment made at e-commerce site

* Ordering
    * Customer orders at e-commerce site
    * Customer chooses to pay at e-commerce site (e.g credit card)
    * Customer chooses which 7-Eleven branch to claim
    * E-commerce site calls ECMS to request shipping label once payment is done
    * ECMS replies with tracking number and CLAIM CODE

### Package Fulfillment (COD) - Payment made at 7-Eleven

* Ordering
    * Customer orders thru e-commerce site
    * Customer chooses which 7-Eleven branch to pay and claim
    * E-commerce site calls ECMS to post/ create order
    * ECMS replies with “PAY & CLAIM”/ REFERENCE CODE and tracking number

* Payment and Claiming
    * Customer pays transaction
    * Clerk scans REFERENCE CODE
    * POS sends transaction details to 7-CONNECT
    * Clerk scans shipping label
    * Clerk collects money from customer
    * POS releases Acknowledgement Receipt
    * Clerk releases package

### 7-Eleven as Returns Platform

* Customer inputs UID/Order Number/Waybill Number at CLiQQ Kiosk
* ECMS generates new tracking number for the package
* CLiQQ kiosk dispenses RETURN CODE 
* Clerk scans RETURN CODE
* POS releases Acknowledgement Receipt
* ECMS sends notification to e-commerce site
* ECMS includes package list to be pulled out by trucker

### Definition of Terms

* ECMS - 7-Eleven e-commerce management system
* CDI - 7-Eleven warehouse
* Tracking Number - ECMS generated number paired with each package/ order
* PAY CODE - 7-Connect reference number for payment
* CLAIM CODE - 7-Connect reference number for claiming
* PAY & CLAIM/ REFERENCE/ COD CODE - 7-Connect reference number for claiming for Cash on Delivery
* RETURN CODE - 7-Connect reference number for customer returns
* APO - Authority to Pull-out document

## API Calls

### Request Shipping label

This will be used to initiate a shipment instruction for an order placed at your website. If the customer prepays for the order at the website, an amount of 0 will be passed, otherwise an amount will be specified for COD shipments. A tracking number and a shipping label will be returned so that the order can be packaged and labeled by the merchant prior to delivery to the distribution center. A Claim Code will also be returned for the merchant to give the customer.

```
POST http://demo.cliqq.net:8086/ecms/api/v1/shipments

Header: Content-Type application/json
Authorization: Bearer Ym9ic2Vzc2slvbjE6czNjcmV0

Request
{
    "merchantCode": "001",
    "merchant":"Merchant A",
    "mobileNumber":"09123456789",
    "merchantReference":"AXDY43322222524544",
    "deliveryLocationId":"0166",
    "deliveryDate":"2015-08-02 17:55:59",
    "orderDate":"2015-08-01",
    "shipping_weight":"0.5" # in kg
    "shipping_length":"12.3" # in cm
    "shipping_width":"12.3" # in cm
    "shipping_height":"12.3" # in cm
    "description": "item description"
    "amount":"200.00",
    "paymentType":"COD"
}
Response
{
  "trackingNumber": "30310533261428120",
  "shippingLabel": "https://labels.cliqq.net/ksd9234jd9023490234.pdf",
  "claimCode": "9917-6789-0023"
}
```

### Request Return Transaction

This will be used to initiate a return instruction.

```
POST http://demo.cliqq.net:8086/ecms/api/v1/returns

Header: Content-Type application/json
Authorization: Bearer Ym9ic2sVzc2lvbjE6cdzNjcmV0

Request
{
    "merchantCode": "001",
    "merchant":"Merchant A",
    "mobileNumber":"09123456789",
    "merchantReference":"AXDY43322222524544",
    "type":"RETURN"
}
Response
{
  "trackingNumber": "30310533261428120"
}
```

### Request Package Dropoff and Delivery to Store

This will be used initiate a package dropoff and delivery either to store or door.

```
POST http://demo.cliqq.net:8086/ecms/api/v1/dropoff

Header: Content-Type application/json
Authorization: Bearer Ym9ic2sVzc2lvbjE6cdzNjcdmV0

Request (if deliver to another store)
{
    "merchantCode": "001",
    "mobileNumber":"09123456789",
    "merchantReference":"AXDY43322222524544",
    "deliveryType":"STORE",
    "amountCollect":"0.00", # If COD, specify amount to collect
    "dest": {
        "deliveryLocationId":"0166",
    }
}

Request (if deliver to door)
{
    "merchantCode": "001",
    "mobileNumber":"09123456789",
    "merchantReference":"AXDY43322222524544",
    "deliveryType":"DOOR",
    "amountCollect":"0.00", # If COD, specify amount to collect
    "dest": {
        "shippingAddress":"xxx xxx xxx xxx xxx",
    }
}

Response
{
  "trackingNumber": "30310533261428120",
  "amountDue": "0.00" # Shipping fee due from customer
}
```

### Notification Webhook

You will need to implement the following webhook if you want to receive status updates from ECMS as your shipment goes through the logistics process.

Status codes include DELIVERED TO WAREHOUSE, IN-TRANSIT, CLAIMED. The full list to be posted.

```
POST <YOUR_NOTIFICATION_HANDLER_URL>

Request
{
    "trackingNumber":"30310533261428120"
    "referenceId""30310533261428120",
    "location":"0234",
    "status":"DELIVERED TO WAREHOUSE",
    "timestamp"::"2015-08-02 17:55:59"
}

Response
200
```