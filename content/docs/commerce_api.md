---
title: CLiQQ Commerce Support APIs
description: "Use our API to send a package for customer pickup at store or drop off a package at store."
date: "2017-04-10T16:43:08+01:00"
draft: false
toc: false
bref: "Use our API to send a package for customer pickup at store or drop off a package at store"
---

### Map Service

7-Eleven opens an average of 1 store per day and may schedule temporary store closures for renovation. We recommend using one of the following methods to get the most current listing.

A. Download the current list of stores at the following URL. The update frequency of this csv file is at 06:00 daily. Use this to populate your own store selector.

[http://s3.philseven.com/public/ecms_stores.csv](http://s3.philseven.com/public/ecms_stores.csv)

B. Post to our store locator website [https://mapservice.cliqq.net/](https://mapservice.cliqq.net/) A callback URL may be appended to this URL so that the selected store can be returned back to your application.

The following map shows the current store density in Metro Manila so you may want to limit results to be within a 3 kilometer radius.

{{< figure src="/img/map-metromanila.png" title="Map of Metro Manila" >}}


### Notification Webhook

You will need to implement the following webhook if you want to receive status updates from ECMS as your shipment goes through the logistics process.

Please refer to the status table for your use case.

```
POST <YOUR_NOTIFICATION_HANDLER_URL>

Request
{
    "trackingNumber":"30310533261428120"
    "merchantReference""30310533261428120",
    "status":"REJECTED",
    "reason":"No merchant",
    "location":"0234",
    "timestamp"::"2015-08-02 17:55:59"
}

Response
200
```