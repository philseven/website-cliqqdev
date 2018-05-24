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

### Store List

7-Eleven opens an average of 1 store per day and may schedule temporary store closures for renovation. We recommend using one of the following methods to get the most current listing.

A. Download the current list of stores at the following URL. The update frequency of this csv file is at 06:00 daily. Use this to populate your own store selector.

[http://s3.philseven.com/public/ecms_stores.csv](http://s3.philseven.com/public/ecms_stores.csv)

B. Post to our store locator website [https://mapservice.cliqq.net/](https://mapservice.cliqq.net/) A callback URL may be appended to this URL so that the selected store can be returned back to your application.

The following map shows the current store density in Metro Manila so you may want to limit results to be within a 3 kilometer radius.

{{< figure src="/img/map-metromanila.png" title="Map of Metro Manila" >}}

### Technical Notes

* This API is open to selected partners.
* A package sent through the system is limited to maximum of volume of 27,000 cubic cm (30cm x 30cm x 30cm) or 10kg.
* A maximum cash value of PHP 2,500 per package is accepted.
* The tracking number provided by the API is 18 digits. Use EAN-128C as the barcode format when printing on a shipping label.
* Delivery is to the [7-Eleven central distribution center](https://goo.gl/maps/UtG1TEyQRRw) in Pasig.
* The delivery cutoff to the distribution center is at **T+0** 12:00 noon. The following table shows when the package will be ready for customer pickup at the store.

| Zone | Ready for Claiming at Store |
| ---- | --------------------------- |
| Greater Metro Manila | **T+1** 08:00 |
| Rest of Luzon | **T+3** 08:00 |
| Visayas | **T+5** 08:00 |
| Mindanao | **T+10** 08:00 |

* An package may only stay in the store for 7 days before it is pulled out and returned back to the distribution center waiting for merchant return.

### Settlement

* Payments received from the customer will be transferred every Friday to the merchant's designated bank account for all transactions made from Thu prior week to Wed. (Current supported bank is BPI.)
* The shipping fees will be likewise need to be transferred to the Philippine Seven Corporation bank account following the same schedule.

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

Request (STORE or DOOR)
{
    "merchantCode": "001",
    "mobileNumber":"09123456789",
    "merchantReference":"AXDY43322222524544",
    "deliveryDate":"2015-08-02 17:55:59",
    "orderDate":"2015-08-01",
    "shipping_weight":"0.5" # in kg
    "shipping_length":"12.3" # in cm
    "shipping_width":"12.3" # in cm
    "shipping_height":"12.3" # in cm
    "description": "item description"
    "amount":"200.00",
    "shipmentPayor": "MERCHANT" | "SELLER",
    "dropoffLocationId":"0166",
    "paymentType":"COD",
    "deliveryType":"STORE" | "DOOR",
    "dest": {
        "deliveryLocationId":"0166"
    } OR
    "dest": {
        "address1":"xxx xxx xxx xxx xxx",
        "address2":"xxx xxx xxx xxx xxx",
        "city":"xxx xxx xxx xxx xxx",
        "province":"xxx xxx xxx xxx xxx"
    }
}

If shipmentPayor is MERCHANT, the returnCode amount is zero where seller does not pay upon dropoff and fee is collected from the merchant.

Shipment fee is calculated based on origin, destination and delivery type. Upon dropoff, the shipment fee is recalculated based on actual dropoff location. If the shipment fee does not match the original shipment fee, then transaction is declined.

Response
{
  "bookingReference": "AYW78",
  "trackingNumber": "30310533261428120",
  "returnCode": "1822-1234-2345",
  "claimCode": "1822-3134-7365",
  "amountDue": "0.00" 
  "shippingFee": "30.00"
}
```

### Notification Webhook

You will need to implement the following webhook if you want to receive status updates from ECMS as your shipment goes through the logistics process.

Status codes include DELIVERED TO WAREHOUSE, IN-TRANSIT, CLAIMED. The full list to be posted.

**Deliver to Store Status Flow*

* DELIVERED TO WAREHOUSE (Package is received by DC)
* FOR DELIVERY (Package is for dispatch)
* IN-TRANSIT (Package loaded to truck)
* DELIVERED TO STORE (Package is received by store)
* CLAIMED (Package is claimed by customer)

**Returns Status Flow**

* FOR RETURN (Customer created a return label)
* RETURNED (Customer hands over package to counter)
* FOR PULL OUT (DC instructs truck to pull out, package is at store or transit)
* FOR RETURN TO MERCHANT (Package is received at DC)
* RETURNED TO MERCHANT (Package is dispatch from the DC)

**C2C Status Flow**

* FOR DROPOFF (Customer created a return label)
* DROPPED OFF (Customer hands over package to counter)
* FOR PULL OUT (DC instructs truck to pull out, package is at store or transit)
* DELIVERED TO WAREHOUSE (Package is received by DC)
* FOR DELIVERY (Package is for dispatch)
* IN-TRANSIT (Package loaded to truck)
* DELIVERED TO STORE (Package is received by store)
* CLAIMED (Package is claimed by customer)

```
POST <YOUR_NOTIFICATION_HANDLER_URL>

Request
{
    "trackingNumber":"30310533261428120"
    "merchantReference""30310533261428120",
    "status":"DELIVERED TO WAREHOUSE",
    "location":"0234",
    "timestamp"::"2015-08-02 17:55:59"
}

Response
200
```