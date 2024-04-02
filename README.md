# Readium LCP Specifications

## Introduction to LCP

LCP stands for “Licensed Content Protection”.

Readium LCP is a Digital Rights Management (DRM) technology developed *by* the publishing industry, *for* the publishing industry. It is a vendor-neutral, interoperable and distributed DRM solution, created and promoted by Readium members and managed by [EDRLab](https://www.edrlab.org). 

LCP defines a simple passphrase-based authentication method, which is minimally intrusive for end users AND fulfills the requirements of public libraries, booksellers and publishers regarding content protection. 
 
LCP is designed for privacy and GDPR compliance (required for EU adoption) since no third-party is collecting user data.

Client applications can be developed on mobile devices, desktop computers and e-readers. Browser based applications cannot integrate LCP, as such applications cannot handle DRM details safely enough. 

LCP is free from transaction costs. The only mandatory cost for using the DRM is a yearly certification fee for each server or client application supporting LCP.

LCP is built using standard & best practice technology for content encryption:

* AES 256 bits encryption for content and content keys
* SHA-256 for user passphrases
* RSA with SHA-256 for signing licenses.

## Supported Formats

Different publication formats can be protected by LCP; this includes: 

* EPUB 2 and EPUB 3, including with embedded audio and video content
* PDF documents
* Readium Packaged Web Publications (audiobooks and DiViNa)

## Specifications

* [Licensed Content Protection 1.0](releases/lcp/latest.md) defines the structure of a license document .
* [License Status Document 1.0](releases/lsd/latest.md) defines a document representing the status of a license along with interactions that might affect its status.


## Technical Notes

The following technical notes are currently in draft: 

* [LCP for PDF](notes/lcp-for-pdf.md) defines how PDF documents are protected by LCP. 
* [LCP for Audiobooks](notes/lcp-for-audiobooks.md) defines how Audiobooks using the Readium Packaged Web Publication format are protected by LCP. 
* [LCP Profile Upgrade](notes/lcp-profile-upgrade.md) defines a mechanism for seamlessly transitioning an operational LCP ecosystem to the most recent profile revision, whilst ensuring forward compatibility for outdated client software, and for non-updatable user devices.
* [LCP Automatic Key Retrieval](notes/lcp-key-retrieval.md) defines how hashed passphrases can be automatically retrieved from a server by authentified users. 

## Registries

Registries are live documents listing controlled values associated with the specifications. They are available [here](registries/).


## Requirements

It is often useful to get access to the requirements which prelude to technical specifications. 

The requirements and use-cases for the LCP technology have been expressed in 2012 and can still be found on the [IDPF website, EPUB content protection](http://idpf.org/epub-content-protection).

Other requirement papers are:

* [LCP Profile Upgrade Requirements](notes/lcp-profile-upgrade-requirements). 

## Software

A large [open-source codebase]({{site.url}}/readium-lcp-codebase) has been developed as part of the Readium project by EDRLab. This includes a full featured License Server, DRM libraries compliant with different Readium toolkits (Readium SDK, Readium Mobile / Desktop), plus a lightweight library which can be integrated on any device.

## More Information

More information about LCP is found on the [EDRLab web site](https://www.edrlab.org/readium-lcp/) and via email (contact at edrlab.org).