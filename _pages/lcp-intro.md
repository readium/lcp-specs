---
layout: default
permalink: "index"
title: "Introduction to Readium LCP"
---

# Introduction to LCP

LCP stands for “Licensed Content Protection”.

Readium LCP is a Digital Rights Management (DRM) technology developed *by* the publishing industry, *for* the publishing industry. It is a vendor-neutral, interoperable and distributed DRM solution, created and promoted by Readium members and managed by [EDRLab](https://www.edrlab.org). 

LCP defines a simple passphrase-based authentication method, which is minimally intrusive for end users AND fulfills the requirements of public libraries, booksellers and publishers regarding content protection. 
 
LCP is designed for privacy and GDPR compliance (required for EU adoption) since no third-party is collecting user data.

Client applications can be developed on mobile devices, desktop computers and e-readers. Browser based applications cannot integrate LCP, as such applications cannot handle DRM details safely enough. 

LCP is free from transaction costs. The only mandatory cost for using the DRM is a yearly certification fee for each server or client application supporting LCP.

## EPUB, PDF, Audiobooks, DiViNa

Different publication formats can be protected by LCP; this includes: 

* EPUB 2 and EPUB 3, including with embedded audio and video content
* PDF documents
* Readium Packaged Web Publications (audiobooks and DiViNa)

## Specifications

* [Licensed Content Protection 1.0](/lcp-specs/readium-lcp-specification) defines the structure of a license document .
* [License Status Document 1.0](/lcp-specs/readium-lsd-specification) defines a document representing the status of a license along with interactions that might affect its status.
* [LCP Profile Upgrade](/lcp-specs/lcp-profile-upgrade) defines a mechanism for seamlessly transitioning an operational LCP ecosystem to the most recent profile revision, whilst ensuring forward compatibility for outdated client software, and for non-updatable user devices.

## Draft specifications

The following specifications are currently drafts: 

* [LCP for PDF](/lcp-specs/lcp-for-pdf) defines how PDF documents are protected by LCP. 
* [LCP for Audiobooks](/lcp-specs/lcp-for-audiobooks) defines how Audiobooks using the Readium Packaged Web Publication format are protected by LCP. 

## Registries

Registries are live documents listing controlled values associated with the specifications. They are available [here](/lcp-specs/registries/).

## Software

A large [open-source codebase](/lcp-specs/readium-lcp-codebase/) has been developed as part of the Readium project by EDRLab. This includes a full featured License Server, DRM libraries compliant with different Readium toolkits (Readium SDK, Readium Mobile / Desktop), plus a lightweight library which can be integrated on any device.

## ISO Standardization

Readium LCP has been submitted to ISO standarization by Japan, South Korea and France. The work is in progress and LCP should become an international standard by Q4 2020.

## Core Technology

Readium LCP is built using standard & best practice technology for content encryption:

* AES 256 bits encryption for content and content keys
* SHA-256 for user passphrases
* RSA with SHA-256 for signing licenses.

## Requirements

It is often useful to get access to the requirements which prelude to technical specifications. 

The requirements and use-cases for the LCP technology have been expressed in 2012 and can still be found on the [IDPF website, EPUB content protection](http://idpf.org/epub-content-protection).

Other requirement papers are:

* [LCP Profile Upgrade Requirements](/lcp-specs/lcp-profile-upgrade-requirements). 


## More Information

More information about LCP is found on the [EDRLab web site](https://www.edrlab.org/readium-lcp/) and via email (contact at edrlab.org).