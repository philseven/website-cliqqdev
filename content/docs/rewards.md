---
title: CLiQQ Rewards API
description: "Reward your users with CLiQQ points"
date: "2017-04-10T16:43:08+01:00"
draft: false
toc: false
bref: "Reward your users with CLiQQ points"
---

### CLiQQ Rewards Sales Integration API v1.0.1

## Overview

CLiQQ Rewards API is an XML-based API that allows authorized partners to submit sales information for loyalty system processing.

### Use Case

Whenever the customer buys from your store, reward them with CLiQQ points that can be used at 7-Eleven by redeeming from the monthly reward catalog or converted to CLiQQ PAY credits for buying anything at the store.

### SalesCapture Request

To submit data to the API, a client must first create a well formed XML payload containing the following information:

* salesTransactionID: unique across all stores varchar(255) 
* receiptNumber: varchar(255)
* terminalId: varchar(255)
* storeId: varchar(255)
* cashierPartyId: varchar(255)
* customerPartyId: varchar(255)
* transactionDate: datetime format YYYY-MM-DDThh:mm:ss 
* grandTotal amount: decimal(19,2)
* salesItemList
  * salesItemId: int 
  * productId: varchar(255) 
  * unitPrice: decimal(19,2) 
  * quantity: decimal(19,2)

### Sample Request:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<sales>
  <salesTransactionId>0007-125644-1-HTTHPZ1E</salesTransactionId>
  <receiptNumber>125644</receiptNumber>
  <terminalId>701</terminalId>
  <storeId>0007</storeId>
  <cashierPartyId>111611</cashierPartyId>
  <customerPartyId>9901187548600</customerPartyId>
  <transactionDate>2014-04-10T11:32:41</transactionDate>
  <grandTotal>
    <amount>30.00</amount>
  </grandTotal>
  <salesItemList>
    <salesItem>
      <salesItemId>1</salesItemId>
      <productId>24020021</productId>
      <unitPrice>
        <amount>7.00</amount>
      </unitPrice>
      <quantity>
        <value>2.00</value>
      </quantity>
    </salesItem>
    <salesItem>
      <salesItemId>2</salesItemId>
      <productId>51010053</productId>
      <unitPrice>
        <amount>16.00</amount>
      </unitPrice>
      <quantity>
        <value>1.00</value>
      </quantity>
    </salesItem>
  </salesItemList>
</sales>
```

The API client should send the XML document to the capture URL via an HTTP POST request.

Capture URL for test environment:

`http://test.apollo.com.ph:8084/RedemptionHost/[partnerID]/transaction`

* The API client should set a header key:Content-Type to the value:text/xml
* The API client should set the appropriate header for HTTP Basic Authentication.
* For example with a username of "Aladdin" and a password of "OpenSesame", then the field's value is the base64-encoding of Aladdin:OpenSesame, or QWxhZGRpbjpPcGVuU2VzYW1l. Then the Authorization header will appear as:
    * HTTP Header: `Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`

Sample curl command to POST the XML contents of sample.xml file to the API endpoint:

```
curl -H "Content-type: text/xml" \
-H "Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==" \
-X POST \
-d @sample.xml http://test.apollo.com.ph:8084/RedemptionHost/[partnerID]/transaction
```

### SalesCapture Responses

Submitting data to the API may return one of the following responses:

* File Accepted Response

    This response is returned when the loyalty system verifies that the card number provided is activated.

    The loyalty system will then process the submitted data for awarding of points, stars or vouchers.

    Sample File Accepted Response:

    ```   
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <salesCaptureResponse>
    <responseCode>200</responseCode>
    <traceNumber>0007-125644-1-HTTHPZ1E</traceNumber>
    <message>
        Your reward is being processed. Please expect an SMS to your mobile number 09xx-xxxxxxx
        CLiQQ Rewards Hotline
        (02) 485 7889 (0917) 714 7190 (0922) 879 2134
        </message>
    </salesCaptureResponse>
    ```

* Card Not Activated Response

    This response is returned when the loyalty system determines that the card number provided is not yet activated.

    Sample Card Not Activated Response:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <salesCaptureResponse>
    <responseCode>412</responseCode>
    <traceNumber>0007-125644-1-HTTHPZ1E</traceNumber>
    <message>
        Please register your card number using your mobile phone
        so we can send you your reward voucher.
        Text the following: CARDNUMBER/LASTNAME/ FIRSTNAME to 09189090711
        CLiQQ Rewards Hotline
        (02) 485 7889 (0917) 714 7190 (0922) 879 2134
        </message>
    </salesCaptureResponse>
    ```