---
layout: default 
title: Federal PKI Trust Store Management Script
collection: tools
permalink: tools/trust_store_management/
---

{% include alert-info.html content="Did you know the Federal Common Policy Root CA, often referred to as \"COMMON,\" is being removed from the Microsoft and Apple operating system trust stores? This change will impact PIV authentication processes. See the FPKI Guides' Announcements and Trust Stores pages for additional details and solutions." %} 

The Trust Store Management Script (TSMS) is a convenient way to update your agency's Microsoft and Apple trust stores (also called _certificate stores_) with the Certification Authority (CA) certificates for all known PIV/CAC issuers (both Federal PKI and DoD PKI). 

- [How Does This Work?](#how-does-this-work)
- [Application Requirements](#application-requirements)
- [Using the Script](#using-the-script)
     - [Download Script Package](#download-script-package)
     - [Verify Script Package SHA-256 Hash](#verify-script-package-sha-256-hash)
     - [Unpack and Verify Script Package Contents](#unpack-and-verify-script-package-contents)
     - [Run the Script](#run-the-script)
     - [Distribute or Install Script Output](#distribute-or-install-script-output)
- [FAQs](#faqs)

{% include alert-info.html content="This script is intended for Enterprise Administrators and Network Engineers." %}

{% include alert-warning.html content="Many systems rely on Certificate Revocation Lists or Online Certificate Status Protocol responses to validate certificates. After you install the CA certificates from the script output, you may need to adjust firewall rules to obtain certificate revocation statuses from the issuing CAs. If you have questions, please email us at fpki@gsa.gov." %}

<br>

## How Does This Work?
The Trust Store Management Script (TSMS) will help you to select and bundle CA certificates for all known PIV/CAC issuers. You can also [exclude specific certificates from the output](#run-the-script). The script outputs the selected certificates to a .p7b (Windows) or .mobileconfig Apple Configuration Profile (macOS) file.  

The script package contains three artifacts:

**1. targets.json**
* The **targets.json** configuration file lists all eligible CA certificates and a short list of attributes (e.g., subject, issuer, validity dates, serial number, install, etc.) for each.<!--I really don't think we need to define "JSON."--> 

* All CA certificates with an **INSTALL** attribute status of **TRUE** will be bundled into the output file. You can exclude a certificate from the output file by simply setting its **INSTALL** status to **FALSE.** 

<p align="center">
<b>
TARGETS.JSON - EXAMPLE OF CA CERTIFICATE ATTRIBUTES
</b>
</p>

```
  {
    "ID": "00001",
    "SUBJECT": "Betrusted Production SSP CA A1",
    "ISSUER": "Federal Common Policy CA",
    "VALIDFROM": "12/9/2010 19:55",
    "VALIDTO": "12/9/2020 19:49",
    "SERIAL": "19A",
    "FILENAME": "Betrusted_Production_SSP_CA_A1.cer",
    "THUMBPRINT": "0601bbdad5a28231bc9436750b4f3a484bab06c3",
    "INSTALL": "TRUE"
  },
```

**2. certLoader.py**
* The **certLoader.py** script reads the **targets.json** file and bundles all selected CA certificates.<!--We're explaining the FALSE/TRUE issue too many times. Described already twice above. Deleted those details given here. We explain this again below at "Run the Script." Trying to cut down on the real estate for the same info.-->

**3. id-fpki-common-auth** 
* This directory contains the actual CA certificates eligible for installation. These certificates assert the **id-fpki-common-auth** (2.16.840.1.101.3.2.1.3.13) policy object identifier (OID) needed for PIV authentication. (You can see detailed CA certificate information [here]({{ site.baseurl }}/tsmseligiblecacerts/){:target="_blank"}.)


## Application Requirements
<!--Logically, it seems that "Application Requirements" section should come before the "How Does This Work?" section. If someone can't use Python, then there's be no point in moving on to "How Does This Work?" unless out of curiousity or wondering if he/she could rewrite the script in another scripting language once he/she sees the concepts...?-->
* _Microsoft Windows_: _Python v3.x_ and _OpenSSL_ (**Note:** You'll need to set an OpenSSL environment path variable for the script to work properly.)<br> 
* _Apple macOS_: _Python v3.x_

## Using the Script 

### Download Script Package

Download the script installation package (.zip)&nbsp;&mdash;&nbsp;[Trust Store Management Script](../../tools/TSMS-V1/Trust_Store_Mangagement_Script_V1.zip){:target="_blank"}.

### Verify Script Package SHA-256 Hash

{% include alert-warning.html content="You should never open a .zip file without first verifying its SHA-256 hash." %} 

Verify that the SHA-256 hash of the .zip package matches this one:

   ```
    EC2A7159E42CCA958190E50B18C6B35009234FD23B160731245239A09B36751A
   ```

* **Microsoft Windows**:

    ```
    > certutil -hashfile [DOWNLOAD_LOCATION]\Trust_Store_Mangagement_Script_V1.zip SHA256
    ```
* **Apple macOS**:
    
    ```
    $ shasum -a 256 [DOWNLOAD_LOCATION]/Trust_Store_Mangagement_Script_V1.zip
    ```

### Unpack and Verify Script Package Contents

1. Double-click the .zip package to see the **Trust_Store_Mangagement_Script_V1** directory.

1. Unpack the directory to your Desktop. (If you selected another directory, update the path in **certLoader.py**.)

### Run the Script

1. In the **targets.json** file, view the default set of CA certificates. To exclude any certificates from the output file, change their **INSTALL** statuses to **FALSE**.

1. Run the script:

     - _Microsoft Windows_:

         ```
         > cd Desktop\Trust_Store_Mangagement_Script_V1
         > certLoader.py
         ```
     - _Apple macOS_:

         ```
         $ cd Desktop/Trust_Store_Mangagement_Script_V1
         $ certLoader.py
         ```
         
1. The command line window will display the certificate details as the script creates the output file.

1. The output file appears on your Desktop (or chosen location). Here are 2 example output files:

     * [Sample .p7b output file](../../tools/TSMS-V1/sample-tsmt-output.p7b){:target="_blank"}
     * [Sample .mobileconfig output file](../../tools/TSMS-V1/sample-tsmt-output.mobileconfig){:target="_blank"}

### Distribute or Install Script Output         
Use your agency's network configuration procedures to distribute or install your CA certificates bundle.

- _Microsoft Windows_&nbsp;&mdash;&nbsp;Use _certutil_ or a Group Policy Object to distribute the .p7b file to agency Microsoft domain controllers or share it via an agency intranet website.                     
- _Apple macOS and iOS_&nbsp;&mdash;&nbsp;Use your agency's configuration procedures to distribute the .mobileconfig file to agency Apple devices or share it via an agency intranet website.<!--Should we be including "iOS" here?-->

## FAQs

### How often will this script be updated?

Quarterly or as often as needed. 

### How can I give feedback or suggestions?

We welcome your feedback and contributions! Please comment or contribute through GSA's GitHub [FPKI Guides Issues](https://github.com/GSA/fpki-guides/issues){:target="_blank"} or email us at **fpki@gsa.gov**.

### Where can I get help?

We're here for you: email us at **fpki@gsa.gov**.