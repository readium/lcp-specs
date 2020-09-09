# Readium LCP Profile upgrade - Requirements

August 2018

By: Laurent Le Meur, EDRLab

## Introduction

The European Digital Reading Lab (EDRLab) is an international, non-profit development lab, busy working on the deployment of an open, interoperable and accessible digital publishing ecosystem in Europe.

We develop technology to accelerate the adoption of EPUB and Open Web technologies by the digital publishing industry. We are the European headquarter of the Readium Foundation, an international non-profit membership organization which manages open-source Reading Software, and an active member of the World Wide Web Consortium (W3C).

Readium LCP is and interoperable, non-proprietary, cost effective and user-friendly Digital Rights Management technology that is emerging at a global scale. 
The specification of Readium LCP is open and the DRM is embeddable in any reading application, based or not on Readium reading software. After two years of development of specification, open-source library development and promotion, the DRM finally hits the market in 2018 in several countries, including France, Germany, Canada, Japan, India.

EDRLab is in charge of managing the Readium LCP Network; its main responsibility is to handle the maintenance and secure transfer of Confidential Information to Solution Providers willing to integrate the Readium LCP Network, and to certify the compliance of Reading Systems and License Servers with Readium LCP before they integrate the Readium LCP Network.

EDRLab is also in charge of the evolution of the Readium LCP specification. This document details how the specification will evolve to allow for a smooth evolution of the crypto material that protects the LCP ecosystem against hackers. 

## Rationale

No DRM is guaranteed to be safe against hackers. Every well-known DRM solution for ebooks - Amazon DRM, Apple iTunes DRM, Adobe ADEPT DRM etc. - has been abused, with leaked / reverse-engineered sensitive crypto information freely available as illegitimate content protection removal software, known as “de-DRM”.

Usually, hacks don’t need any update because the DRM mechanisms put in place by large companies don’t evolve in time. This is a comfortable situation for hackers, would don’t like to have to spend lots of time on a regular basis to find their way in complex and obfuscated code. 

The two main vectors of attack against Readium LCP will certainly be:

* Theft from a trusted partner of the secret crypto material that centralizes the LCP decryption codebase; reuse of this information from an application which takes as inputs the hash of the user passphrase plus some encrypted content, and outputs clear content.
* Extraction of the secret crypto material inside the binary code of one of the multiple applications supporting Readium LCP; development of a software which uses this material, using as a base the open-source LCP client software provided by EDRLab. 

To protect the DRM from the second attack vector, EDRLab has obfuscated the source code of the Readium LCP software module, using a commercial product. A step by step discovery of the decryption code is therefore very difficult. 

Both attacks still require that the end user of the LCP hacking software has acquired a license for a publication and therefore possesses the corresponding passphrase.

The objective of this proposal is to make hackers’ life much less comfortable by allowing a modification of the crypto material on which Readium LCP is based, on a regular basis (e.g. every two years, or if we see that a major breach of the crypto material has spread around the world).
Another hypothetical reason for updating the LCP crypto material would be to adopt the latest cryptographic industry standards, in order to protect the integrity of the LCP ecosystem in the long run.

This protection will be based on the evolution of the LCP profile. An LCP profile consists in root certificates, cipher algorithms and confidential cryptographic material.  

Readium LCP already supports this notion of profile and two profiles are currently used in the ecosystem: a “basic” profile detailed in the LCP specification and used for test applications only; A “1.0” profile, given only to trusted partners and used only for production-grade applications. This proposal will therefore make the notion of LCP profile a useful brick of the LCP ecosystem. 

## Requirements

**Req 1**: The solution MUST provide a mechanism for periodically and seamlessly transitioning an operational LCP ecosystem from a profile revision to the next.

**Req 2**: The solution MUST ensure backward compatibility for updated client software handling licenses from outdated license servers. 

**Req 3**: The solution MUST ensure forward compatibility for outdated client software (and for non-updatable user devices) handling licenses from updated license servers.

**Req 4**: The solution MUST allow LCP license server software, which is globally distributed, to be upgraded to the most recent profile revision in a large time span.

**Req 5**: The solution MUST allow the deprecation of the oldest profile revisions after some years. 

**Req 6**: The solution MUST NOT require a new encryption of the complete database of publications distributed by a license provider at the time a profile update occurs. 


## Who was involved in this project

The following EDRLab team members and several members of the association are involved in this project:

* Laurent Le Meur: CTO of EDRLab, involved in every EDRLab project, including Readium LCP, Readium Mobile, Readium Desktop, Readium Web, Web Publications.
Daniel Weck: senior developer at EDRLab; working on Readium LCP, Readium Mobile, Readium Desktop, Readium Web.
* Hadrien Gardeur: CEO of Feedbooks, a French distributor of EPUB ebooks and developer of mobile reading applications; co-editor of the LCP specification and implementer of Readium LCP.
* Rémi Bauzac: CTO of TEA (The Ebook Alternative), a French solution provider for the publishing industry and member of EDRLab; implementer of Readium LCP.
* Stefaan Nemegger: lead developer at ePagine, a French solution provider for the publishing industry and member of EDRLab; implementer of Readium LCP.
