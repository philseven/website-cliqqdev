---
title: CLiQQ Shop API
description: "Embed CLiQQ Shop in your app or send a package for customer pickup at store."
date: "2017-04-10T16:43:08+01:00"
draft: true
toc: false
bref: "Embed CLiQQ Shop in your app or send a package for customer pickup at store"
---

### Use Case

1. You have an e-commerce site and you want to provide the customer with the option to pay and pickup the order at the nearest 7-Eleven.

2. You operate a marketplace and you want to provide the seller the option to drop off at the nearest 7-Eleven and the buyer to pay and pickup at their nearest 7-Eleven.

3. You have an app and you want to offer your users with a list of items to purchase. You earn a commission for every sale made through your app.

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

Customer inputs UID/Order Number/Waybill Number at CLiQQ Kiosk
ECMS generates new tracking number for the package
CLiQQ dispenses RETURN CODE 
Clerk scans RETURN CODE
POS releases AR
ECMS sends notification to Z
ECMS includes package to APO

### Definition of Terms

* ECMS - 7-Eleven e-commerce management system
* CDI - 7-Eleven warehouse
* Tracking Number - ECMS generated number paired with each package/ order
* PAY CODE - 7-Connect reference number for payment
* CLAIM CODE - 7-Connect reference number for claiming
* PAY & CLAIM/ REFERENCE/ COD CODE - 7-Connect reference number for claiming for Cash on Delivery
* RETURN CODE - 7-Connect reference number for customer returns
* APO - Authority to Pull-out document
