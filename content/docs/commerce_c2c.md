---
title: CLiQQ Commerce C2C API
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

### Use Case

You operate a marketplace and you want to provide the seller the option to drop off at the nearest 7-Eleven and the buyer to pay and pickup at their nearest 7-Eleven.

![](/C2C.PNG)

##### Status Flow

![C2C_Flow](/C2CStatus.PNG)

**Status**|**Trigger**|**Description**
-----|-----|-----
CREATED/ CONFIRMED|Merchant API transaction creation|Transaction has been created in ECMS
CANCELLED|Merchant API transaction cancellation|Transaction has been cancelled in ECMS
FOR DROP OFF|Kiosk printing|Shipping label was already printed in the kiosk
DROPPED OFF|POS transaction|Item has been dropped off at store
FOR PULL OUT|ECMS job|Item has been added in the APO
ON VEHICLE TO WAREHOUSE|Trucker scanning|Item has been loaded in trucker and on its way to CDI
ARRIVED AT WAREHOUSE|Trucker scanning|Item has arrived in the warehouse
DELIVERED TO WAREHOUSE|CDI scanning|Item has been received in CDI
FOR DELIVERY|ECMS job|Item has been loaded to trucker and on its way to store
IN-TRANSIT|CDI scanning|Item has been dispatched
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
DELIVERED TO WAREHOUSE|CDI scanning|Item has been received in CDI
FOR DELIVERY|ECMS job|Item has been loaded to trucker and on its way to drop off store location
IN-TRANSIT|CDI scanning|Item has been dispatched
DELIVERED TO WAREHOUSE\_REGIONAL|Regional scanning|Item has been received by regional
IN-TRANSIT\_REGIONAL|Regional scanning|Item has been dispatched by regional
ON VEHICLE TO STORE|Trucker scanning|Item has been loaded to trucker and on its way to store
ARRIVED AT STORE|Trucker scanning|Item has arrived at the drop off store location
DELIVERED TO STORE|SBS receiving|Store has received the item
CLAIMED|POS transaction|Seller has claimed the item
FOR PULL OUT|ECMS job|Item was unclaimed by seller
PULLED OUT FROM STORE|SBS Returns|Item was returned to trucker by store
ON VEHICLE TO WAREHOUSE|Trucker scanning|Item was loaded into the truck and on its way back to the DC
ARRIVED AT WAREHOUSE|Trucker scanning|Item has arrived at the DC
FOR RETURN TO MERCHANT|CDI scanning|Item was received back at warehouse
RETURNED TO MERCHANT|CDI scanning|Item was returned to merchant during delivery

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

* An package may only stay in the store for 7 days before it is pulled out and returned back to the distribution center for delivery to buyer or for waiting for merchant return.

## API Calls

### Authentication

#### Authentication/ Create Access Token

Authentication to the API is done via OAuth2.0. Each client application, this includes the POS (via 7-Connect), CLiQQ kiosks, CLiQQ app, and admin system will be given its own client id and client secret to be stored securely inside the application.

```
POST https://<server path>/accounts/oauth2/token

URL Parameters

client_id = {client id}
client_secret = {client secret}
response_type = "token"
grant_type = "client_credentials"

Response
{
  "refresh_token":"788b771fed934aa5cac0da2b2e0c17cf",
  "token_type":"bearer",
  "access_token":"Ym9ic2Vzc2lvbjE6czNjcmV0",
  "expires_in":7200
}
```

#### Refresh Access Token

Access Tokens expire for security purposes. To get a new access token, you will need to use the refresh token. As discussed above, access token comes with a refresh token and an expires_in value(time expressed in seconds). The API expects the client to use this expires_in value to track if the access token it is holding is already expired. Send an HTTP POST request to /oauth2/token path to get a refresh token.

```
POST https://<server path>/accounts/oauth2/token

URL Parameters

client_id = {client id}
client_secret = {client secret}
refresh_token = {refresh_token}
response_type = "token"
grant_type = "refresh_token"

Response
{
  "refresh_token":"788b771fed934aa5cac0da2b2e0c17cf",
  "token_type":"bearer",
  "access_token":"Ym9ic2Vzc2lvbjE6czNjcmV0",
  "expires_in":7200
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
    "packageType":"C2C"
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