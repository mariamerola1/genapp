# General insurance application (GENAPP) for IBM CICS TS

## Description

The primary purpose of this repository is to demonstrate application modernization, by providing a **general insurance application (known as GENAPP) for IBM CICS Transaction Server for z/OS (CICS TS)**. The GENAPP application has been developed in-house by the **IBM Hursley CICS Development team** to help with their testing of new CICS releases.  
  
GENAPP is designed to exercise various components of CICS TS; containing functions to **insert, query, and delete policy information**. In its original form GENAPP is driven using **3270 input**, where the insurance policy information is stored in a **DB2 database**.  


## New in this release

-   Refreshed license terms to remove restriction on Limited Use Program for CICS TS V4 or later.

  
## Changes in previous release

-   Tested with and supported by CICS TS V5.3.
-   Customer Search GENAPP Extension using Liberty Profile has been updated to fully support CICS TS V5.2.
-   GENAPP Lite V5.1.0.2 supports fully SOAP and JSON web services in CICS TS V5.2.
-   GENAPP Lite removes the need for DB2 and uses only VSAM KSDS to hold customer and policy data.
-   Web service definition files are included for Customer Add and Customer Inquiry.
-   Sample Workload Simulator (WSIM) scripts and network definitions are included to drive the application.

## Details

 - **Contributors:** CICS Development (see below for details), IBM Hursley Park, UK   
 - **First released**: 08Feb12   
 - **Last changed**: 10Jul13   
 - **Current Version**: 5.1.0.1   
