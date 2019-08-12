---
layout: default
permalink: "readium-lsd-specification"
title: "Readium LSD specification"
---
# Readium License Status Document 1.0

*Copyright 2018, Readium Foundation. All Rights Reserved.*

**This version:** 

* [https://readium.org/lcp-specs/lsd.html](https://readium.org/lcp-specs/lsd.html)

**Previous version:** 

* [https://readium.org/technical/readium-lsd-specification/](https://readium.org/technical/readium-lsd-specification/)

**Document Revision:** 1.4

## 1. Overview

### 1.1 Purpose and Scope
*This section is informative*

This specification, License Status Document (1.0.1), defines a document for representing the status of a DRM license along with interactions that might affect its status. These interactions put additional responsibilities on both Content Providers and Reading Systems.

One of the primary objectives is to support lending in public libraries where a user may have the ability to renew a time-limited loan or return one before it expires.

### 1.2. Terminology

This specification uses the following EPUB and Licensed Content Protection [[LCP](#normative-references)] terms:

<dl>
  <dt>Publication</dt>
  <dd>A logical document entity consisting of a set of interrelated resources and packaged in an EPUB Container, as defined by the EPUB 3 specifications.</dd>

  <dt>User</dt>
  <dd>An individual that consumes an EPUB Publication using an EPUB Reading System.</dd>

  <dt>EPUB Reading System (or Reading System)</dt>
  <dd>A system that processes EPUB Publications for presentation to a User in a manner conformant with the EPUB 3 specifications.</dd>

</dl>

Additionally, it uses the following terms:

<dl>
  <dt>Content Provider</dt>
  <dd>The authority that grants the License Document and the Publication to the User, usually a Web Server.</dd>

  <dt>License Document</dt>
  <dd>Document that contains references to the various keys and restrictions that are applied by a specific DRM on a Publication.</dd>

  <dt>Status Document</dt>
  <dd>Document that contains information about the history of a License Document, along with its current status and available interactions.</dd>

</dl>

### 1.3. Example

*This section is informative*

In the following example, the Status Document indicates that the License has been registered for a single device and may be renewed or returned.

```json
{
  "id": "234-5435-3453-345354",
  "status": "active",
  "message": "Your license is currently active and has been used on one device.",

  "updated": {
    "license": "2016-08-05T00:00:00Z",
    "status": "2016-08-08T00:00:00Z"
  },

  "links": [
    {
      "rel": "license",
      "href": "https://example.org/license/35d9b2d6",
      "type": "application/vnd.readium.lcp.license.v1.0+json",
      "profile": "http://readium.org/lcp/basic-profile"
    },
    {
      "rel": "register",
      "href": "https://example.org/license/35d9b2d6/register{?id,name}",
      "type": "application/vnd.readium.license.status.v1.0+json",
      "templated": true
    },
    {
      "rel": "return", 
      "href": "https://example.org/license/35d9b2d6/return{?id,name}",
      "type": "application/vnd.readium.license.status.v1.0+json",
      "templated": true
    },
    {
      "rel": "renew",
      "href": "https://example.org/license/35d9b2d6/renew",
      "type": "text/html"
    },
    {
      "rel": "renew",
      "href": "https://example.org/license/35d9b2d6/renew{?end,id,name}",
      "type": "application/vnd.readium.license.status.v1.0+json",
      "templated": true
    }
  ],
  
  "potential_rights": {
    "end": "2014-09-13T00:00:00Z"
  },
  
  "events": [
    {
      "type": "register", 
      "name": "eBook App (Android)", 
      "timestamp": "2016-07-14T00:00:00Z", 
      "id": "709e1380-3528-11e5-a2cb-0800200c9a66"
    }
  ]
}
```

### 1.4 Conformance Statements

The keywords <b class="rfc">must</b>, <b class="rfc">must not</b>, <b class="rfc">required</b>, <b class="rfc">shall</b>, <b class="rfc">shall not</b>, <b class="rfc">should</b>, <b class="rfc">should not</b>, <b class="rfc">recommended</b>, <b class="rfc">may</b>, and <b class="rfc">optional</b> in this document are to be interpreted as described in [[RFC2119](#normative-references)].

All sections of this specification are normative except where identified by the informative status label "This section is informative". The application of informative status to sections and appendices applies to all child content and subsections they may contain.

All examples in this specification are informative.

## 2. Status Document

### 2.1. Content Conformance

A Status Document <b class="rfc">must</b> meet all of the following criteria:

* It <b class="rfc">must</b> meet the conformance constraints for JSON documents as defined in [JSON](#normative-references).
* It <b class="rfc">must</b> parse as a single JSON object.
* It <b class="rfc">must</b> be encoded using UTF-8.

Access to a Status Document <b class="rfc">must not</b> require any form of authentication. 

A Status Document <b class="rfc">should not</b> be served over an insecure connection. 

The MIME media type for a Status Document is `application/vnd.readium.license.status.v1.0+json` and HTTP servers <b class="rfc">must</b> set the “Content-Type” header appropriately.

### 2.2. Core Information

A Status Document <b class="rfc">must</b> include the following information:

| Key | Semantics | Format |
| --- | --------- | ------ |
| `id` | Unique identifier for the License Document associated to the Status Document | String |
| `status` | Current status of the License | Controlled vocabulary, as defined in section 2.3 |
| `message` | A message meant to be displayed to the User regarding the current status of the license | String |

All messages provided in the message key <b class="rfc">should</b> be localized based on the HTTP `Accept-Language` header included in the request for the Status Document.

### 2.3. Status of a License

The `status` field succinctly describes the current status of the license. 
The following values are allowed:

| Value | Semantics |
| ----- | --------- |
| `ready` | The License Document is available, but the user hasn't accessed the License and/or Status Document yet. |
| `active` | The license is active, and a device has been successfully registered for this license.<br /> This is the default value if the License Document does not contain a registration link, or a registration mechanism through the license itself. |
| `revoked` | The license is no longer active, it has been invalidated by the Issuer. |
| `returned` | The license is no longer active, it has been invalidated by the User. |
| `cancelled` | The license is no longer active because it was cancelled prior to activation. |
| `expired` | The license is no longer active because it has expired. |

### 2.4. Timestamps

A Status Document <b class="rfc">must</b> include an `updated` object where the following keys and associated timestamps are provided:

| Key | Semantics | Format |
| --- | --------- | ------ |
| `license` | Time and Date when the License Document was last updated. | ISO 8601  |
| `status` | Time and Date when the Status Document was last updated. | ISO 8601 | 

### 2.5. Links

A Status Document <b class="rfc">must</b> include a `links` object. It contains an array of Link Objects where each object <b class="rfc">may</b> contain the following keys:

| Key | Semantics | Format | Required? |
| --- | --------- | ------ | --------- |
| `href` | Link location. | URI or URI Template | Yes |
| `rel` | Link relationship to the document. | List of well-known relation values, URIs for extensions | Yes |
| `title` | Title of the link. | String | No |
| `type` | Expected MIME media type value for the external resources. | MIME media type | No, but highly recommended |
| `templated` | Indicates that the href is a URI Template. | Boolean | No, default value is `false` |
| `profile` | Expected profile used to identify the external resource. | URI | No |


A Status Document <b class="rfc">must</b> include at least one link where the relation is set to `license`.

| Relation | Semantics | Templated? | Required? | HTTP Verb |
| -------- | --------- | ---------- | --------- | --------- |
| `license` | Location of the License Document associated to the Status Document. | No | Yes | GET |


### 2.6. Potential Rights

A Status Document <b class="rfc">may</b> provide a list of potential changes allowed for a License Document using the `potential_rights` object.

This specification defines the following expression of potential rights:

| Key | Semantics | Format |
| --- | --------- | ------ |
| `end` | Time and Date when the license ends. | ISO 8601  |

### 2.7. Events

A Status Document <b class="rfc">may</b> provide an ordered list of events related to the change in status of a License Document.

These events are documented in an `events` object where the following keys are used to describe an event:

| Key | Semantics | Format |
| --- | --------- | ------ |
| `type` | Identifies the type of event. | See the enumeration below. |
| `name` | Name of the client, as provided by the client during an interaction. | String |
| `id` | Identifies the client, as provided by the client during an interaction. | String |
| `timestamp` | Time and date when the event occurred. | ISO 8601 |


The following `type` values are allowed:

| Value | Semantics |
| ----- | --------- |
| `register` | Signals a successful registration event by a device. |
| `renew` | Signals a successful renew event. |
| `return` | Signals a successful return event. |
| `revoke` | Signals a revocation event. |
| `cancel` | Signals a cancellation event. |

## 3. Interactions

In addition to a standardized document, the LSD Specifications also defines a number of interactions that are all exposed through the `links` object.

### 3.1. Handling Errors

To provide a consistent behavior for both clients and end users, all servers must handle errors using the Problem Details JSON object as defined in [[RFC7807](#normative-references)] with the following additional requirements:

* The server <b class="rfc">must</b> return a `title` and a `type` in the Problem Details JSON object
* The server <b class="rfc">should</b> attempt to localize both `title` and `detail` based on the `Accept-Language` header sent by the client

Each interaction in this specification provides a number of Failure Types that are returned to the client if the interaction fails.

### 3.2. Checking the Status of a License

A client <b class="rfc">should</b> on a regular basis retrieve a Status Document in order to update a License Document.

If the `license` timestamp in the `updated` object of the Status Document is more recent than the timestamp contained in the local copy of the License Document, the client <b class="rfc">must</b> download the License Document again and replace its previous copy with the new one.

If the `links` object contains more than one link with a relation set to `license`, the client <b class="rfc">must</b> select one of the Link Objects based on the value of their `profile` property. The selected Link Object <b class="rfc">must</b> correspond to the highest profile value the client can handle. The order of the links is of no importance.  

If the client does not find a profile it can handle in the previous set, it <b class="rfc">must</b> consider that no updated license is available.

If the `status` value of a Status Document contradicts the corresponding up-to-date License Document, the License Document takes precedence.

If the Status Document is unavailable or if the client is unable to obtain an internet connection, it <b class="rfc">must not</b> block the user from accessing the Publication tied to the License Document.

### 3.3. Registering a Device

Registration is meant to provide the server with a hint of how many clients have interacted with a License Document.

When a client opens a License Document for the first time and gets access to its associated Status Document:

* It <b class="rfc">must</b> attempt to register itself using the link exposed in the Status Document
* It <b class="rfc">must not</b> block the user from accessing the Publication associated to the License Document if the registration fails
* It <b class="rfc">must</b> attempt to register itself again if it couldn't do so the first time that the License Document was opened

During the registration, the client <b class="rfc">must</b> always try to send the same unique identifier for a specific device, no matter which Status Document it interacts with. Any further interaction with a provider <b class="rfc">should</b> use the same identifier/name. The client <b class="rfc">should</b> consider user privacy when generating a unique identifier, for example by generating a random string during software installation. To prevent user tracking across providers, the client <b class="rfc">may</b> generate device unique ids for each provider.

If a provider uses registration to monitor license abuse, the provider <b class="rfc">should</b> take care to prevent forged registrations.

| Relation | Semantics | Templated? | Required? | HTTP Verb |
| -------- | --------- | ---------- | --------- | --------- |
| `register` | Associate a new device with the License | Yes | No | POST |


| Parameter | Format | Semantics | Required? |
| --------- | ------ | --------- | --------- |
| `id` | String | A unique identifier for the device. | Yes |
| `name` | String | Human readable name for the device. | Yes |

**Expected Behavior**

| Server side behavior | HTTP Status Code | Client side behavior |
| -------------------- | ---------------- | -------------------- |
| The server registers the device identified by `id` and returns an updated Status Document. <br />The server <b class="rfc">must</b> update the timestamp of the Status Document contained in the status key of the `updated` object. <br />If the status was previously set to ready, it <b class="rfc">must</b> be updated by the server to active instead. <br />The server <b class="rfc">may</b> also add a new event in the `events` object of the Status Document. | 200 | The client <b class="rfc">must not</b> attempt to register the device again. |

**Failure Modes**

| Type | HTTP Status Code | Title |
| ---- | ---------------- | ----- |
| http://readium.org/license-status-document/error/registration | 400 | Your device could not be registered properly. |
| http://readium.org/license-status-document/error/server | 5xx | An unexpected error has occurred. |

*An example of a simple activation link.*

```json
{
  "links": [
    {
      "rel": "register", 
      "href": "https://example.org/license/aaa-bbbb-ccc/register{?id,name}",
      "type": "application/vnd.readium.license.status.v1.0+json",
      "templated": true
    }
  ]
}
```

### 3.4. Returning a Publication

Returning a publication is meant primarily for library use cases, where a patron can return a publication early in order to get access to a new loan.

If returning is unsuccessful, the client <b class="rfc">should</b> attempt to return the License again at a later time without necessarily asking the user again.

| Relation | Semantics | Templated? | Required? | HTTP Verb |
| -------- | --------- | ---------- | --------- | --------- |
| `return` | Ask for the License to be immediately invalidated. | Yes | No | PUT |


| Parameter | Format | Semantics | Required? |
| --------- | ------ | --------- | --------- |
| `id` | String | A unique identifier for the device. | No |
| `name` | String | Human readable name for the device. | No |

**Expected Behavior**

| Server side behavior | HTTP Status Code | Client side behavior |
| -------------------- | ---------------- | -------------------- |  
| The server <b class="rfc">must</b> return an updated Status Document.<br /> The Status Document <b class="rfc">must</b> contain a `status` with its value set to "returned" if the status was previously set to "active" or "cancelled" if the status was previously set to "ready".<br /> The server <b class="rfc">must</b> update the timestamp of both License and Status Document contained in the `status` and `license` keys of the `updated` object. <br /> The server <b class="rfc">may</b> also add a new event in the `events` object of the Status Document. | 200 | The client <b class="rfc">must</b> download an updated Status Document.<br /> The client <b class="rfc">must not</b> allow the user to open the publication anymore. <br /> The client <b class="rfc">should not</b> attempt to return the License anymore. |


**Failure Modes**

| Type | HTTP Status Code | Title |
| ---- | ---------------- | ----- |
| http://readium.org/license-status-document/error/return | 400 | Your publication could not be returned properly. |
| http://readium.org/license-status-document/error/return/already | 403 | Your publication has already been returned before. |
| http://readium.org/license-status-document/error/return/expired | 403 | Your publication has already expired. |
| http://readium.org/license-status-document/error/server | 5xx | An unexpected error has occurred. |

*An example of a simple return link.*

```json
{
  "links": [
    {
      "rel": "return",
      "href": "https://example.org/license/aaa-bbbb-ccc/return{?id,name}",
      "type": "application/vnd.readium.license.status.v1.0+json",
      "templated": true
    }
  ]
}
```


### 3.5. Renewing a License

Renewing a License is also meant primarily for library use cases, where a patron can renew a loan for an extended period of time.

| Relation | Semantics | Required? |
| -------- | --------- | --------- |
| `renew` | Extends the expiration date of a license into the future | No |

| Media Type | Semantics | Templated? | HTTP Verb |
| ---------- | --------- | ---------- | --------- |
| `text/html` | A URL where human interactions will be required. Returns an HTML page. These interactions <b class="rfc">should</b> ultimately result in the extension of the term of a License Document. | No | GET |
| `application/vnd.readium.license.status.v1.0+json` | A URL where the License Document can be programmatically renewed. Returns a Status Document. | Yes | PUT |

These parameters are strictly for the case where the License Document is programmatically renewed.


| Parameter | Format | Semantics | Required? |
| --------- | ------ | --------- | --------- |
| `id` | String | A unique identifier for the device. | No |
| `name` | String | Human readable name for the device. | No |
| `end` | ISO 8601 | A new expiration date for the license.  | No |

**Expected Behavior**

| Server side behavior | HTTP Status Code | Client side behavior |
| -------------------- | ---------------- | -------------------- | 
| The server <b class="rfc">must</b> return an updated Status Document. <br /> The server <b class="rfc">must</b> update the timestamp of both License and Status Document contained in the `status` and `license` keys of the `updated` object.<br /> The server <b class="rfc">may</b> also add a new event in the `events` object of the Status Document. | 200 | The client <b class="rfc">must</b> download an updated Status Document.<br />The client <b class="rfc">may</b> attempt to renew the License again later. |

**Failure Modes**

| Type | HTTP Status Code | Title |
| ---- | ---------------- | ----- |
| http://readium.org/license-status-document/error/renew | 4xx | Your publication could not be renewed properly. |
| http://readium.org/license-status-document/error/renew/date | 403 | Incorrect renewal period, your publication could not be renewed. |
| http://readium.org/license-status-document/error/server | 5xx | An unexpected error has occurred. |

*An example with two renewal links: one meant for human interactions (HTML) and another for clients that can send the proper information to the server (returns a Status Document).*

```json
{
  "links": [
    {
      "rel": "renew",
      "href": "https://example.org/license/aaa-bbbb-ccc/renew",
      "type": "text/html"
    },
    {
      "rel": "renew"
      "href": "https://example.org/license/aaa-bbbb-ccc/renew{?end,id,name}",
      "type": "application/vnd.readium.license.status.v1.0+json",
      "templated": true
    }
  ]
}
```
## 4. Relationship to Readium Licensed Content Protection 1.0

### 4.1. Introduction
*This section is informative*

This specification defines a way for a Content Provider to connect an LCP License Document [LCP](#normative-references)] to a Status Document describing the current status of the underlying license.

It does not add any structural elements to the LCP License Document, but the link relations it defines and the potential rights it enumerates <b class="rfc">may</b> appear in an LCP License Document.

### 4.2. The `status` Relation

This specification defines one new link relation which may be used in the `links` object of an LCP License Document.

| Relation | Semantics | Required? |
| -------- | --------- | --------- |
| `status` | Location of the Status Document associated with this License Document. | No |

If a link with the relation status is not present in the `links` object, the client <b class="rfc">must</b> process the LCP License Document according to the core LCP specification. 

If a link with the relation status is present in the `links` object, the client <b class="rfc">must</b> process the Status Document according to the extended rules listed in [section 3](#3-interactions) of this specification.

The representation of the URI mentioned in the status link <b class="rfc">must</b> be a valid Status Document.

## Normative references

* [JSON] [The application/json Media Type for JavaScript Object Notation (JSON)](https://www.ietf.org/rfc/rfc4627).
* [LCP] [Readium Licensed Content Protection](http://readium.github.io/readium-lcp-specification).
* [RFC2119] [Key words for use in RFCs to Indicate Requirement Levels](https://tools.ietf.org/html/rfc2119).
* [RFC78707] [JSON Problem Details](https://tools.ietf.org/html/rfc7807).
* [URI-Template] [URI Template](https://tools.ietf.org/html/rfc6570).

## Apendix A. JSON Schema

A JSON Schema for LSD 1.0 is available under version control at [https://github.com/readium/lcp-specs/tree/master/schema](https://github.com/readium/lcp-specs/tree/master/schema)

For the purpose of validating a status document, use the following JSON Schema resource:
[https://readium.org/lcp-specs/schema/status.schema.json](https://readium.org/lcp-specs/schema/status.schema.json)


<style>
.rfc {
    color: #d55;
    font-variant: small-caps;
    font-style: normal;
    font-weight: normal;
}
</style>
