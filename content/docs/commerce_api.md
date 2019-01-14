---
title: CLiQQ Commerce APIs
description: "Use our API to send a package for customer pickup at store or drop off a package at store."
date: "2017-04-10T16:43:08+01:00"
draft: false
toc: false
bref: "Use our API to send a package for customer pickup at store or drop off a package at store"
---

### Map Service

Enter the mapservice here.

### Notification Webhook

You will need to implement the following webhook if you want to receive status updates from ECMS as your shipment goes through the logistics process.

Status codes include DELIVERED TO WAREHOUSE, IN-TRANSIT, CLAIMED. Please refer to the status code table for the service.

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