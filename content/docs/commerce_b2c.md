---
title: CLiQQ Commerce B2C API
description: "Use our API to send a package for customer pickup at store or drop off a package at store."
date: "2017-04-10T16:43:08+01:00"
draft: false
toc: false
bref: "Use our API to send a package for customer pickup at store or drop off a package at store"
---

### Definition of Terms

* CDI - 7-Eleven warehouse
* DC - Distribution center/ warehouse
* Tracking Number - ECMS generated number paired with each package/ order
* CLAIM CODE - 7-Connect reference number for claiming
* RETURN CODE - 7-Connect reference number for customer returns
* APO - Authority to Pull-out document

### Use Cases

#### 1. Merchant Dropoff at DC
You have an e-commerce site/ marketplace and you want to provide the customer with the option to pay and pickup the order at the nearest 7-Eleven. In this integration, 7-Eleven will serve as a collection point for your orders. You or your seller will deliver the orders to 7E warehouse and then delivers the items to the store location chosen by the customer/ buyer.

![Merchant Drop off](/B2C_Merchant_Delivery.PNG)

##### Status Flow

![Merchant Drop_off](/Merchant_Dropoff.PNG)

**Status**|**Trigger**|**Description**
-----|-----|-----
CREATED/ CONFIRMED|Merchant API transaction creation|Transaction has been created in ECMS
CANCELLED|Merchant API transaction cancellation|Transaction has been cancelled in ECMS
DELIVERED TO WAREHOUSE|DC scanning|Item has been received in the warehouse
FOR DELIVERY|ECMS job|Item has been added in DR
IN-TRANSIT|DC scanning|Item has been dispatched
DELIVERED TO WAREHOUSE\_REGIONAL|Regional scanning|Item has been received by regional
IN-TRANSIT\_REGIONAL|Regional scanning|Item has been dispatched by regional
ON VEHICLE TO STORE|Trucker scanning|Item has been loaded to trucker and on its way to store
ARRIVED AT STORE|Trucker scanning|Item has arrived at the store
DELIVERED TO STORE|SBS receiving|Store has received the item
CLAIMED|POS transaction|Buyer has claimed the item
FOR PULL OUT|ECMS job|Item was unclaimed
PULLED OUT FROM STORE|SBS Returns|Item was returned to trucker by store
ON VEHICLE TO WAREHOUSE|Trucker scanning|Item was loaded into the truck and on its way back to the DC
ARRIVED AT WAREHOUSE|Trucker scanning|Item has arrived at the DC
FOR RETURN TO MERCHANT|DC scanning|Item was received back at warehouse
RETURNED TO MERCHANT|DC scanning|Item was returned to merchant during delivery


#### 2. DC Pickup from Merchant
You operate a marketplace and you want the buyer to pay and pickup at their nearest 7-Eleven. This is only open to high-volume sellers. Packages will be picked up at the seller location. In this integration, 7-Eleven will also serve as a collection point for your orders. 7E will pick up these orders and then delivers the items to the store location chosen by the buyer.

![Merchant Pickup](/B2C_Merchant_Pickup.PNG)

##### Status Flow

![Merchant_Pickup](/Merchant_Pickup.PNG)

**Status**|**Trigger**|**Description**
-----|-----|-----
CREATED/ CONFIRMED|Merchant API transaction creation|Transaction has been created in ECMS
CANCELLED|Merchant API transaction cancellation|Transaction has been cancelled in ECMS
FOR PICK UP|ECMS job|Transaction has been added in the pickup document
REJECTED|Trucker scanning|Item was not received or was not handed over to trucker
ON VEHICLE TO WAREHOUSE|Trucker scanning|Item has been loaded in trucker and on its way to CDI
ARRIVED AT WAREHOUSE|Trucker scanning|Item has arrived in the warehouse
DELIVERED TO WAREHOUSE|DC scanning|Item has been received in the warehouse
FOR DELIVERY|ECMS job|Item has been added in DR
IN-TRANSIT|DC scanning|Item has been dispatched
DELIVERED TO WAREHOUSE\_REGIONAL|Regional scanning|Item has been received by regional
IN-TRANSIT\_REGIONAL|Regional scanning|Item has been dispatched by regional
ON VEHICLE TO STORE|Trucker scanning|Item has been loaded to trucker and on its way to store
ARRIVED AT STORE|Trucker scanning|Item has arrived at the store
DELIVERED TO STORE|SBS receiving|Store has received the item
CLAIMED|POS transaction|Buyer has claimed the item
FOR PULL OUT|ECMS job|Item was unclaimed
PULLED OUT FROM STORE|SBS Returns|Item was returned to trucker by store
ON VEHICLE TO WAREHOUSE|Trucker scanning|Item was loaded into the truck and on its way back to the DC
ARRIVED AT WAREHOUSE|Trucker scanning|Item has arrived at the DC
FOR RETURN TO MERCHANT|DC scanning|Item was received back at warehouse
RETURNED TO MERCHANT|DC scanning|Item was staged/ stored for returns 
ON VEHICLE TO MERCHANT|Trucker scanning|Item was loaded in the trucker and on its way back to seller
ARRIVED AT MERCHANT|Trucker scanning|Item was returned to seller

### Technical Notes

* This API is open to selected partners.
* Refer to the [Store and Webhooks API](/docs/commerce_api/) for information on how to get the latest store listing and receiving status updates from the platform.
* A package sent through the system is limited to maximum of volume of 27,000 cubic cm (30cm x 30cm x 30cm) or 10kg.
* A maximum cash value of PHP 4,000 per package is accepted.
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


## API Calls

### Request Shipment

This will be used to initiate a shipment instruction for an order placed at your website. If the customer prepays for the order at the website, an amount of 0 will be passed, otherwise an amount will be specified for COD shipments and COD as the payment type. A tracking number and a shipping label will be returned so that the order can be packaged and labeled by the merchant prior to delivery or pickup by the distribution center. A Claim Code will also be returned for the merchant to give the customer.

```
POST http://apidemo.cliqq.net:8086/ecms/api/v1/shipments

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
    "pickupDate":"2015-08-03",
    "shipping_weight":"0.5" # in kg
    "shipping_length":"12.3" # in cm
    "shipping_width":"12.3" # in cm
    "shipping_height":"12.3" # in cm
    "description": "item description"
    "amount":"200.00",
    "paymentType":"COD",
    "packageType":"For Pickup"
    "address": {
        "address1":"xxx xxx xxx xxx xxx",
        "address2":"xxx xxx xxx xxx xxx",
        "city":"xxx xxx xxx xxx xxx",
        "province":"xxx xxx xxx xxx xxx"
    }
}

Response
{
  "trackingNumber": "30310533261428120",
  "shippingLabel": "https://labels.cliqq.net/ksd9234jd9023490234.pdf",
  "claimCode": "9917-6789-0023"
}
```

### Cancel Shipment

This will be used to cancel a shipment instruction for an order placed at your website. A cancelled order would be excluded from the orders to be picked up or delivered.

```
POST http://apidemo.cliqq.net:8086/ecms/api/v1/cancelShipments

Header: Content-Type application/json
Authorization: Bearer Ym9ic2Vzc2slvbjE6czNjcmV0

Request
{
    "trackingNumber":"30310533261428120",
    "reason":"inventory status"
}

Response
{
    "errorCode":"200",
    "message":"Shipment has been cancelled."
}
```


## Settlement

* Payments received from the customer will be transferred every Friday to the merchant's designated bank account for all transactions made from Thu prior week to Wed. (Current supported bank is BPI.)
* The shipping fees will be likewise need to be transferred to the Philippine Seven Corporation bank account following the same schedule.
