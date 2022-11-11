---
layout: default
permalink: "/readium-lcp-codebase"
title:  "Readium LCP Codebase"
---

# Readium LCP Codebase 

A large open-source codebase has been developed as part of the Readium project by [EDRLab](https://www.edrlab.org). This includes a full featured License Server, DRM libraries compliant with different Readium toolkits (Readium SDK, Readium Mobile / Desktop), plus a lightweight library which can be integrated on any device. 

## Readium LCP Server software

The multi-platform reference Readium LCP Server is written in Golang. It provides a command line utility for content encryption, a Readium LCP License Server and a License Status Server. The REST API allows for a seamless integration with front-end systems and offers immediate interaction with Readium LCP compliant Reading Systems.  

&gt; Download it on: [GitHub repository](https://github.com/readium/readium-lcp-server)

## Readium LCP Client libraries

### Kotlin library

The Readium LCP Kotlin library, targeting Android devices, is a module of the Readium Kotlin toolkit.

&gt; Download it on: [GitHub repository](https://github.com/readium/kotlin-toolkit/tree/main/readium/lcp)

### Swift library

The Readium LCP Swift library, targeting iOS devices, is a module of the Readium Swift toolkit.

&gt; Download it on: [GitHub repository](https://github.com/readium/swift-toolkit/tree/main/Sources/LCP)

### Node.js library

The Readium LCP Node.js library, targeting desktop computers, has its own repository.

&gt; Download it on: [GitHub repository](https://github.com/readium/r2-lcp-js)

### C++ library

This module is stable since 2017 and won't be maintained by the Readium core developers. 

The Readium LCP C++ module is deployed as a “content module” of the original Readium SDK. 

&gt; Download it on: [GitHub repository](https://github.com/readium/readium-lcp-client)

It contains a lightweight C++ library which can be (and has been) adapted to any C++ based reading system.  

&gt; Download it on: [GitHub repository](https://github.com/readium/readium-lcp-client/tree/master/src/lcp-client-lib)

## Be part of the Readium LCP Network
The interoperability and security of the Readium LCP ecosystem is guaranteed by EDRLab. 
This implies the integration in LCP compliant software of a specific encryption profile, different from the basic one defined in the LCP specification and provided as part of the open-source software.

Detailed information about this profile is provided to members of the Readium LCP Network only. For more information about how to be part of the Readium LCP Network, please [contact EDRLab](https://edrlab.org/edrlab/contact).

 

