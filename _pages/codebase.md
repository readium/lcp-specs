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

### C++ library

The Readium LCP Client library, written in C++, is very easy to deploy as a “content module” in a Reading System based on the original Readium SDK. Its code may be adapted to any other Reading System implementation using the lightweight c++ library contained in the LCP Client library.

&gt; Download it on: [GitHub repository](https://github.com/readium/readium-lcp-client)

### Kotlin library

The R2 LCP Kotlin library, targeting Android devices, is a module of the Readium Mobile for Android toolkit.

&gt; Download it on: [GitHub repository](https://github.com/readium/r2-lcp-kotlin)

### Swift library

The R2 LCP Swift library, targeting iOS devices, is a module of the Readium Mobile for iOS toolkit.

&gt; Download it on: [GitHub repository](https://github.com/readium/r2-lcp-swift)

### Node.js library

The R2 LCP Node.js library, targeting desktop computers, is a module of the Readium Mobile for Desktop toolkit.

&gt; Download it on: [GitHub repository](https://github.com/readium/r2-lcp-js)

## Be part of the Readium LCP Network
The interoperability and security of the Readium LCP ecosystem is guaranteed by EDRLab. 
This implies the integration in LCP compliant software of a specific encryption profile, different from the basic one defined in the LCP specification and provided as part of the open-source software.

Detailed information about this profile is provided to members of the Readium LCP Network only. For more information about how to be part of the Readium LCP Network, please [contact EDRLab](https://edrlab.org/edrlab/contact).

 

