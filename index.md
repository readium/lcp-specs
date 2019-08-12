---
layout: default
---

## Introduction

LCP stands for “Licensed Content Protection”.

Readium LCP is a Digital Rights Management (DRM) technology developed *by* the publishing industry, *for* the publishing industry. It is a vendor-neutral, interoperable and distributed DRM solution, created and promoted by Readium members and managed by [EDRLab](https://www.edrlab.org). 

LCP defines a simple passphrase-based authentication method, which is minimally intrusive for the end user, but still fulfills the needs of public libraries and publishers. 
 
LCP is designed for privacy and GDPR compliance (required for EU adoption) since no third-party is collecting data on behalf of the distributor.

Client applications can be developed on mobile devices, desktop computers and e-readers. But browser based applications cannot integrate LCP, as such applications cannot handle safely DRM details. 

## Formats

Different publication formats can be protected by LCP; this includes: 

* EPUB 2 and EPUB 3, including embedded audio and video content;
* PDF;
* Readium Packaged Web Publications (audiobooks and DiViNa).

## Core technology

Readium LCP is built using standard & best practice technology for content encryption:

* AES 256 bits encryption for content and content keys
* SHA-256 for user passphrases
* RSA with SHA-256 for signing licenses.

## ISO standardization

Readium LCP has been submitted to ISO standarization by Japan, South Korea and France. The work is in progress and LCP should become an international standard by Q4 2020.

## Specifications

* [Licensed Content Protection 1.0](readium-lcp-specification) defines a license document;
* [License Status Document 1.0](readium-lsd-specification) defines a document representing the status of a license along with interactions that might affect its status;

## Registries

Registries are live documents listing controlled values associated with the specifications. They are available [here](/registries/)

## Software

A large [open-source codebase](readium-lcp-codebase/) has been developed as part of the Readium project by EDRLab. This includes a full featured License Server, DRM libraries compliant with different Readium toolkits (Readium SDK, Readium Mobile / Desktop), plus a lightweight library which can be integrated on any device. 

## More information

The requirements and use-cases for the LCP technology have been expressed in 2012 and can still be found on the [IDPF website](http://idpf.org/epub-content-protection).

More information about LCP is found on the [EDRLab web site](https://www.edrlab.org/readium-lcp/).

Readium LCP is free from transaction costs. The only mandatory cost for using the DRM is a yearly certification fee for each server or client application supporting LCP. Please contact EDRLab for more information (contact at edrlab.org).

