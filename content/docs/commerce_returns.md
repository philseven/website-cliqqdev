---
title: CLiQQ Commerce Returns API
description: "Use our API to send a package for customer pickup at store or drop off a package at store."
date: "2017-04-10T16:43:08+01:00"
draft: false
toc: false
bref: "Use our API to send a package for customer pickup at store or drop off a package at store"
---

### Use Case

You want to allow your customers to return items at their nearest 7-Eleven.

### 7-Eleven as Returns Platform

* Customer inputs UID/Order Number/Waybill Number at CLiQQ Kiosk
* Kiosk validates the entered reference based on the business rules below
* ECMS generates new tracking number for the package
* CLiQQ kiosk dispenses RETURN CODE 
* Clerk scans RETURN CODE
* POS releases Acknowledgement Receipt
* ECMS sends notification to e-commerce site
* ECMS includes package list to be pulled out by trucker

#### Returns Status Flow

![Returns](/Returns.PNG)

**Status**|**Trigger**|**Description**
-----|-----|-----
FOR RETURN|Kiosk transaction|Customer requests for return in the kiosk
RETURNED|POS transaction|Item was handed over to counter
FOR PULL OUT|ECMS job|Item was included in the APO list
PULLED OUT FROM STORE|SBS Returns|Item was returned to trucker by store
ON VEHICLE TO WAREHOUSE|Trucker scanning|Item was loaded into the truck and on its way to the DC
ARRIVED AT WAREHOUSE|Trucker scanning|Item has arrived at the DC
FOR RETURN TO MERCHANT|DC scanning|Item was received at warehouse
RETURNED TO MERCHANT|DC scanning|Item was staged/ stored for returns/ Item was returned to merchant during merchant's delivery
ON VEHICLE TO MERCHANT|Trucker scanning|Item was loaded in the trucker and on its way to merchant
ARRIVED AT MERCHANT|Trucker scanning|Item was handed over to merchant

### Business Rules

* Merchant submits valid return IDs to the platform
* The return IDs are valid only for X days
* When a return ID has been accepted at the counter, it is no longer valid for use in future returns

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
  "returnCode": "1822-1234-2345",
  "trackingNumber": "30310533261428120"
}
```


