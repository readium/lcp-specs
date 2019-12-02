# Readium LCP Automatic Key Retrieval - Technical solution

**Revision: 1**

*Copyright 2019, Readium Foundation. All Rights Reserved.*

By: Laurent Le Meur (EDRLab) and Hadrien Gardeur (De Marque)

## Overview

### Purpose and scope

*This section is informative*

LCP is a passphrase based mechanism. The simplicity of the solution is based on the fact that users can access a protected publication from any compliant device, even without network access, by entering the passphrase associated with an LCP license they have acquired. LCP compliant reading system keep in cache a hash of all passphrases previously entered by their user, and thus rarely request such passphrase. Despite this optimization and the passphrase hint displayed when the passphrase is requested, it may happen that users don’t remember this passphrase and must get support in order to access their protected publications. 

However, in many situations, a user is already authenticated on a Provider server when he acquires an LCP license: this is always the case when the provider is a public library, and this is often the case when the provider is a bookseller. In such a case, wouldn’t it be good for the user to be able to have his passphrase hash retrieved automatically from the server database, put in cache in his client application and used automatically to access the publication?
The goal of this project is to provide such a functionality in an open and standard way. By integrating this functionality, public libraries and booksellers who decide to offer such comfort to their users will reach a level of seamless integration currently mainly provided by proprietary platforms.

Note that this advanced feature does not free LCP license providers to communicate to their users a passphrase they can enter manually on any LCP compliant Client application, helped in this by a clear passphrase hint.

## Means of authentication of the user

*This section is informative*

Two types of access-restricted resources are particularly interesting in the scope of this specification:

* Links to the personal bookshelf of a User (cf OPDS 1 or OPDS 2) in an OPDS Catalog.
* Acquisition links in individual entries of an OPDS Catalog. 

Fetching access-restricted resources require user authentication. This specification does not require a specific user authentication method: the sole requirement is the provision of an Access Token as the result of user authentication. 

The following sub-sections describe recommended authentication methods. 

### Authentication for OPDS

The Authentication for OPDS specification defines a standard way for OPDS Catalog providers and clients to:

* Provide relevant information for clients to display an authentication page,
* Expose how a client can authenticate using various Authentication Flows.
* Get an Access Token required for accessing restricted resources.

The discovery of the Authentication Flow(s) supported by a given Provider is achieved by the provision of an Authentication Document, i.e. a discovery document defined by the Authentication for OPDS specification and expressed as a JSON structure.

Such Authentication Document is returned to any client requesting an access-restricted ressource without providing a valid Access Token, along with a 401 Unauthorized HTTP error code.
By interpreting the information found in the Authentication Document, the client can display an authentication form to the user and conduct the selected Authentication Flow.

Access-restricted resources can be acquired after the User is properly authenticated and his action authorized during the period of validity of the Access Token.  

The multiplicity of authentication services found in the publishing industry requires a large flexibility on the client side. 

The version 1.0 of the Authentication for OPDS specification supports the following Authentication Flows: 

* HTTP Basic Authentication
* OAuth 2.0 Implicit Grant
* OAuth 2.0 Resource Owner Password Credentials Grant.

If HTTP Basic Authentication is used, each request sent by the authentified client to a resource URL which requires an authentication must contain a header field in the form of Authorization: Basic &lt;credentials>, where &lt;credentials> is the base64 encoding of the user id and password, joined by a colon. Basic Authentication is a weak protection, therefore Providers should utilize other authentication flows whenever possible.

If one of the supported OAuth 2.0 flows is used, an Access Token is returned to the Reading System after proper authentication and authorization. This token must then be inserted in the header field of each request sent by the authentified client to a resource URL which requires an authentication, in the form of Authorization: Bearer &lt;access-token>. 

### OAuth 2

RFC 6749 specifies how different types of applications can get an Access Token from an authorization Server using one of four Authization Grant types.

There is not discovery mechanism is the RFC 6749, therefore there may be interoperability issues for deploying many different reading applications accessing many different content servers.

Restrictions may be added to the use of OAuth 2 in subsequent versions of this specification. 

## Terminology

Authorization Server

A server issuing Access Tokens to Reading Systems after successfully authenticating a User and obtaining authorization to access a given resource.

*Access Token*

A unique string sent back to a Reading System by an Authorization Server after authentication of a User.

*Encryption Profile*

A set of encryption algorithms used in a specific Protected Publication and associated Licence Document.

*License Document*

Document that contains references to the various keys, links to related external resources, rights and restrictions that are applied to the Protected Publication, and user information.

*Passphrase Hash*

A value obtained by hashing a User Passphrase using the algorithm defined in the specific Encryption Profile referenced in a License Document.

*Protected Publication*

A Publication that has been protected according to the Readium LCP specification.

*Provider*

An entity that delivers License Documents and Protected Publications to Users.

*User*

An individual that consumes a Publication using a Reading System.

*User Key*

The result of a transform applied to a User Passphrase, used to decrypt the Content Key and selected user information fields contained in a License Document.

*User Passphrase*

A string of text entered by the user and used to generate the User Key. 

## Conformance Statements

The keywords *must, must not, required, shall, shall not, should, should not, recommended, may**, and optional* in this document are to be interpreted as described in [RFC2119].

All sections of this specification are normative except where identified by the informative status label “This section is informative”. The application of informative status to sections and appendices applies to all child content and subsections they may contain.

All examples in this specification are informative.

## Including a hashed passphrase in a JSON Publication Object

### The lcp_hashed_passphrase element

The lcp_hashed_passphrase element represents the hex-encoded value of the hashed passphrase. 

It is implemented as a property of an OPDS 2 Acquisition Link which references an LCP License or Protected Publication. Such a link is found inside an OPDS Publication Object, or more generically a Readium Web Publication Object.

### Sample of Readium Web Publication supporting an lcp_hashed_passphrase property

```
{
  "metadata": {
    "@type": "http://schema.org/Book",
    "identifier": "urn:isbn:9780000000002",
    "title": "A Journey into the Center of the Earth",
    "author": "Jules Verne",
    "language": "en"
  },
  "links": [
    {
      "rel": "http://opds-spec.org/acquisition/",
      "href": "https://example.com/license.lcpl",
      "type": "application/vnd.readium.lcp.license.v1.0+json",
      "properties": {
        "lcp_hashed_passphrase": "9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08"
      }
    }
  ]
}
```

## Including a hashed passphrase in an OPDS 1 Catalog

### The lcp:hashed_passphrase element

This specification defines the XML namespace http://readium.org/lcp-specs/ns. This namespace must be declared Its usual namespace prefix is lcp.

The &lt;hashed_passphrase> element is defined in this namespace as a child of the &lt;link> element defined in the “atom” namespace; it represents the hex-encoded value of the hashed passphrase.

### Sample of an OPDS 1 Link Object supporting an lcp:hashed_passphrase property

```
<entry xmlns="http://www.w3.org/2005/Atom">
  ...
  <link rel="http://opds-spec.org/acquisition/"
      href="https://example.com/license.lcpl"
      type="application/vnd.readium.lcp.license.v1.0+json">
    <lcp:hashed_passphrase>9f86d081884c7d6
      59a2feaa0c55ad015a3bf4f1b2b0b822cd15d
      6c15b0f00a08</lcp:hashed_passphrase> 
 </link>
</entry>
```

Note: line breaks in the hash value are for display purpose only.

## Use Cases and Workflows

### Getting a hashed passphrase at acquisition time

A usual use case on a Publishing Web site is as follows:

1. A user browses a catalog without being signed-in.
1. He chooses a publication and reads its presentation page. This page contains a “buy” or “borrow” button.
1. He selects the button.
1. A sign-in screen appears, the user enters his credentials and validates. 
1. A new screen appears where the user can download the protected publication he has acquired. 
1. During a certain time, every attempt to acquire a publication is immediately successful: the user may just have to confirm his acquisition before the protected publication is downloaded. 

This is achieved by providing to an unknown client an http 401 response to the “buy” or “borrow” action. When Authentication for OPDS is used, an Authentication Document is returned which contains one or more ways for the user to authenticate and get an Authorization Token.  

Once authentified / authorized the user receives, as a response to a “download” action, a JSON Publication Object which contains a link to an LCP license plus the hashed passphrase corresponding to this license.

The client stores the hashed passphrase, fetches the license, then moves to the standard LCP workflow, i.e. validates the license structure, verifies its status, checks that a stored passphrase (any in pratice) corresponds to the license, downloads the encrypted content, embeds the license in the content and opens the ebook.  

### Getting the hashed passphrases related to a personal OPDS bookshelf

A usual use case on a Publishing Web site is as follows:

1. A user enters the web site and signs-in.
1. He selects his personal bookshelf (i.e. the list of publications an authentified User has already acquired). On each entry a “download” button is shown. 
1. The user can immediately download one or more protected publications.
1. During a certain time, every attempt to acquire a publication is immediately successful.

This is achieved by providing to the client of the authentified / authorized user, inside each OPDS 1 or 2 entry, a link to an LCP license plus the hashed passphrase corresponding to this license.

For each publication selected by the user, the download mechanism is identical to the one described in the previous use-case. Even if all hashed passphrases are usually identical in the feed (this is recommended, but not required), using one hashed passphrase per entry is a simpler solution to specifiy and implement. 
Note that it is recommended for the client to put in cache the access token and optional refresh token for future use. When Authentication for OPDS is used, the Authentication Document has an identifier and the access and refresh tokens are associated with this id. When a call response is 401 with an Authentication Document, the client can send the access token associated with this id, or call the associated refresh URL if the access token has expired. 


## References

RFC 6749 - OAuth 2.0

https://tools.ietf.org/html/rfc6749

The OAuth 2.0 Authorization Framework: Bearer Token Usage

https://tools.ietf.org/html/rfc6750

OPDS 2.0

https://drafts.opds.io/opds-2.0

Authentication for OPDS

https://drafts.opds.io/authentication-for-opds-1.0

