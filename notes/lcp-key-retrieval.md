# Readium LCP Automatic Key Retrieval 1.0

**Revision: 3**

*Copyright 2020, Readium Foundation. All Rights Reserved.*

Contributors: 
Laurent Le Meur (EDRLab),
Hadrien Gardeur (De Marque),
Rémi Bauzac (Vivlio),
Daniel Weck (EDRLab),
Stefaan Nemegger (ePagine),
Jean-Philippe Bougie (De Marque)

## Overview

### Purpose and scope

*This section is informative*

LCP is a passphrase based mechanism. The simplicity of the solution is based on the fact that users can access a protected publication from any compliant device, even without network access, by entering the passphrase associated with an LCP license they have acquired. LCP compliant reading systems keep in cache a hash of all passphrases previously entered by their user, and thus rarely request such passphrase. Despite this optimization and the passphrase hint displayed when the passphrase is requested, it may happen that users don’t remember this passphrase and must get support in order to access their protected publications. 

However, in many situations, a user is already authenticated on a Provider server when he acquires an LCP license: this is the case when the user is browsing his personal bookshelf via an OPDS feed. In such a case, wouldn’t it be good for the user to be able to have his passphrase retrieved automatically from the server database, cached in his client application and used automatically to access the publication?

The goal of this project is to provide such a functionality in an open and standard way. By integrating this functionality, public libraries and booksellers who decide to offer such comfort to their users will reach a level of seamless integration currently mainly provided by proprietary platforms.

Note that this advanced feature does not free LCP license providers to communicate to their users a passphrase they can enter manually on any LCP compliant Client application, helped in this by a clear passphrase hint.

## Terminology

*Authorization Server*

Server issuing Access Tokens to Reading Systems after successfully authenticating a User and obtaining authorization to access a given resource.

*Access Token*

Unique string sent back to a Reading System by an Authorization Server after authentication of a User.

*Encryption Profile*

Set of encryption algorithms used in a specific Protected Publication and associated Licence Document.

*License Document*

Document that contains references to the various keys, links to related external resources, rights and restrictions that are applied to the Protected Publication, and user information.

*Passphrase Hash*

Value obtained by hashing a User Passphrase using the algorithm defined in the specific Encryption Profile referenced in a License Document.

*Personal Bookshelf*

List of publications an authentified User has already acquired.

*Protected Publication*

Publication that has been protected according to the Readium LCP specification.

*Provider*

Entity that delivers License Documents and Protected Publications to Users.

*User*

Individual that consumes a Publication using a Reading System.

*User Key*

Result of a transform applied to a User Passphrase, used to decrypt the Content Key and selected user information fields contained in a License Document.

*User Passphrase*

String of text entered by the user and used to generate the User Key. 

## Conformance Statements

The keywords *must, must not, required, shall, shall not, should, should not, recommended, may* and *optional* in this document are to be interpreted as described in [RFC2119].

All sections of this specification are normative except where identified by the informative status label “This section is informative”. The application of informative status to sections and appendices applies to all child content and subsections they may contain.

All examples in this specification are informative.

## Means of authentication of the user

*This section is informative*

Two types of access-restricted resources are particularly interesting in the scope of this specification:

* Links to the personal bookshelf of a User in an OPDS Catalog.
* Acquisition links in individual entries of an OPDS Catalog. 

Fetching access-restricted resources requires user authentication. This specification does not require a specific user authentication method: the sole requirement is the provision of an Access Token as the result of user authentication. 

The following sub-sections describe recommended authentication methods. 

### Authentication for OPDS

The [Authentication for OPDS specification](https://drafts.opds.io/authentication-for-opds-1.0) defines a standard way for OPDS Catalog providers and clients to:

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

### Vanilla OAuth 2

[RFC 6749](https://tools.ietf.org/html/rfc6749) specifies how different types of applications can get an Access Token from an authorization Server using one of four Authorization Grant types.

There is no discovery mechanism in the RFC 6749, therefore there may be interoperability issues when deploying many different reading applications accessing many different content servers.

Restrictions may be added to the use of OAuth 2 in subsequent versions of this specification. 

## Providing LCP licenses as part of an OPDS feed

The following sample corresponds to an e-lending situation. The client application retrieves a feed in which each entry includes an acquisition link. Because the ebook may not be immediately available, the server will respond to a "borrow" action by returning an OPDS entry. This structure provides additional indirect acquisition links: the user will get first an LCP license, and will then acquire an EPUB file. 

If the ebook is immediately available, the OPDS entry returned by the server must be structured as described in the following section. If the ebook is reserved, the OPDS entry must contain an "unavailable" status. 

### Using OPDS 1

``` xml
<feed ...>
  <entry ...>
    <title>A Journey into the Center of the Earth</title>
    <link href="https://example.com/10333/borrow" rel="http://opds-spec.org/acquisition/borrow" type="application/atom+xml;type=entry;profile=opds-catalog">
     <opds:indirectAcquisition type="application/vnd.readium.lcp.license.v1.0+json"/>
         <opds:indirectAcquisition type="application/epub+zip"/>
     </opds:indirectAcquisition>
      <opds:availability status="available"/>
    </link>
  </entry>
</feed>
```

### Using OPDS 2

``` json
{
  "metadata": { ... },
  "links": { ... },
  "publications": [
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
          "rel": "http://opds-spec.org/acquisition/borrow",
          "href": "https://example.com/opds-publication/9780000000002",
          "type": "application/opds-publication+json",
          "properties": {
            "indirectAcquisition": [
              {"type": "application/vnd.readium.lcp.license.v1.0+json",
              "child":[
                {"type": "application/epub+zip"}
                ]
              }
            ]
          }
        }
      ]
    }
  ]
}
```

## Including a hashed passphrase in an OPDS Publication

Such entry must be provided to an authenticated user only. Unauthentified users directed to a OPDS entry defined as below will be required to authenticate. 

In the following sample, the link of the OPDS entry corresponds to the direct acquisition of an LCP license. The "indirectAcquisition" property describes the type of publication attached to the license. If the hash of the user passphrase is added as described below, the client application will store this value and use it to open the ebook; the user will not have to enter his passphrase manually.    

### Using OPDS 1

#### The lcp:hashed_passphrase element

This specification defines the XML namespace `http://readium.org/lcp-specs/ns`. 
This namespace must be declared. Its usual namespace prefix is `lcp`.

The &lt;hashed_passphrase> element is defined in this namespace as a child of the &lt;link> element defined in the “atom” namespace; it represents the hex-encoded value of the hashed passphrase.

#### Sample

``` xml
<entry xmlns="http://www.w3.org/2005/Atom" xmlns:lcp="http://readium.org/lcp-specs/ns">
  ...
  <link rel="http://opds-spec.org/acquisition"
      href="https://example.com/license.lcpl"
      type="application/vnd.readium.lcp.license.v1.0+json">
    <lcp:hashed_passphrase>+usAylGL6nyxGn7zH7YYO0ibG26tt5K+xkoDs/b/gKg=</lcp:hashed_passphrase> 
    <opds:indirectAcquisition type="application/epub+zip"/>
 </link>
</entry>
```

### Using OPDS 2 (or any protocol that uses Readium Web Publication Manifests)

#### The lcp_hashed_passphrase element

The lcp_hashed_passphrase element represents the base64-encoded value of the hashed passphrase. 

It is implemented as a property of an OPDS 2 Acquisition Link which references an LCP License or Protected Publication. Such a link is found inside an OPDS entry.

#### Sample 

``` json
{
  "metadata": {
    "@type": "http://schema.org/Book",
    "identifier": "urn:isbn:9780000000002",
    "title": "A Journey into the Center of the Earth",
  },
  "links": [
    {
      "rel": "http://opds-spec.org/acquisition",
      "href": "https://example.com/license.lcpl",
      "type": "application/vnd.readium.lcp.license.v1.0+json",
      "properties": {
        "lcp_hashed_passphrase": "+usAylGL6nyxGn7zH7YYO0ibG26tt5K+xkoDs/b/gKg=",
        "indirectAcquisition": [{"type": "application/epub+zip"}]
      }
    }
  ]
}
```

Note about the computation of the base64-encoded value of the hashed passphrase: from the hashed value of the passphrase, expressed as an hex-encoded string, calculate a byte array (32-bytes / 256-bits binary buffer); for instance, "4981AA..." becomes [49, 81, 170, ...]. The expected value is the Base64 encoding of this byte array. Note that a base64 conversion is usually implicitly applied to byte arrays when converted to json structures.


## Including a link to an OPDS Publication inside an LCP license

The License Provider may decide to include in each LCP license a link to an OPDS Publication containing the hash of the user passphrase (as defined in a previous section). 

In any reading application able to process such information, at the time the user wants to open the corresponding ebook, he will have to go through the authentication phase. Once the user is authenticated, the client application will fetch the OPDS Publication, extract the passphrase hash, store it, and use it to open the ebook.  

### Sample LCP license with an embedded link to an OPDS Authentication Document 

``` json
{
  "id": "ef15e740-697f-11e3-949a-0800200c9a66",
  "issued": "2013-11-04T01:08:15+01:00",
  "updated": "2014-02-21T09:44:17+01:00",
  "provider": "https://www.imaginaryebookretailer.com",
  "encryption": { ... },
  "user_key": { ... },
  "links": [
    {
      "rel": "http://opds-spec.org/acquisition",
      "href": "https://example.com/opds-publication/9780000000002",
      "type": "application/opds-publication+json"
    }
  ]
}
```

The sequence diagram below is an illustration of this workflow. 

## Use Cases and Workflows

*This section is informative*

### Getting a hashed passphrase at acquisition time

A usual use case on a Publishing website is as follows:

1. A user browses a catalog on the website without being signed-in.
1. He chooses a publication and reads its presentation page. This page contains a “buy” or “borrow” button.
1. He hits the button.
1. A sign-in screen appears, the user enters his credentials and validates. 
1. A new screen appears where the user can "download" the protected publication he has acquired. 
1. During a certain time, every attempt to acquire a publication is immediately successful: the user may just have to confirm his acquisition before the protected publication is downloaded. 

This is achieved by providing to an unknown client an http 401 response to the “buy” or “borrow” action. 

If Authentication for OPDS is used, an Authentication Document is returned which contains one or more ways for the user to authenticate. A Refresh URL may also be returned at this step. 

Once authentified / authorized, the user receives an Access Token which is put in cache and immediately used as a parameter of the "download" link. The response to the “download” link is a OPDS Publication which contains a link to an LCP license and the hashed passphrase corresponding to this license.

Note that when Authentication for OPDS is used, the Authentication Document has an identifier and the Access Token and refresh URL can be associated with this id for future reuse. When a response to a call has an http 401 response with an Authentication Document, the client can use the Access Token associated with this id, or call the associated refresh URL if the Access Token has expired, thus avoiding a new user authentication step.

The client stores the hashed passphrase, fetches the license, then follows the standard LCP workflow, i.e. it validates the license structure, verifies its status, checks that a stored passphrase (any in pratice) corresponds to the license, downloads the encrypted content, embeds the license in the content and opens the ebook.

### Getting the hashed passphrases related to a personal OPDS bookshelf

A usual use case on an OPDS compliant reading app is as follows:

1. A user launches his reading app and selects his personal bookshelf.
1. A sign-in screen appears, the user enters his credentials and validates. The content of his personal bookshelf appears, with a “download” button on each entry. 
1. The user can immediately download one or more protected publications.
1. During a certain time, every access to his personal bookshelf is immediately successful.

This is achieved by providing to the client of the authentified / authorized user, inside each OPDS 1 or 2 entry / publication, a link to an LCP license plus the hashed passphrase corresponding to this license. Note that an OPDS 2 Publication is a simplified Readium Web Publication Manifest.

For each publication selected by the user, the download mechanism is identical to the one described in the previous use-case. Even if all hashed passphrases are usually identical in the feed (this is recommended, but not required), using one hashed passphrase per entry is a simpler solution to specify and implement. 

### Sequence diagrams

#### Automatic LCP key retrieval, use of Authentication for OPDS

![Automatic LCP key retrieval, use of authentication for OPDS](https://www.edrlab.org/public/readium-lcp/LCP-key-retrieval-authOPDS.png)

#### Automatic LCP key retrieval, reuse of the Authentication Document

![Automatic LCP key retrieval, reuse of the Authentication Document](https://www.edrlab.org/public/readium-lcp/LCP-key-retrieval-fromAuthDocLink.png)

## References

RFC 6749 - OAuth 2.0

[https://tools.ietf.org/html/rfc6749](https://tools.ietf.org/html/rfc6749)

The OAuth 2.0 Authorization Framework: Bearer Token Usage

[https://tools.ietf.org/html/rfc6750](https://tools.ietf.org/html/rfc6750)

OPDS 2.0

[https://drafts.opds.io/opds-2.0](https://drafts.opds.io/opds-2.0)

Authentication for OPDS

[https://drafts.opds.io/authentication-for-opds-1.0](https://drafts.opds.io/authentication-for-opds-1.0)


## Who was involved in this project

* Laurent Le Meur: CTO of EDRLab, involved in every EDRLab project, including Readium LCP, Readium Mobile, Readium Desktop, Readium Web, Web Publications.
* Daniel Weck: senior developer at EDRLab; working on Readium LCP, Readium Mobile, Readium Desktop, Readium Web.
* Hadrien Gardeur: Product manager at De Marque, a Canadian solution provider for the publishing industry and member of EDRLab; implementer of Readium LCP.
* Jean-Philippe Bougie: Project manager at De Marque.
* Rémi Bauzac: CTO of TEA (The Ebook Alternative), a French solution provider for the publishing industry and member of EDRLab; implementer of Readium LCP.
* Stefaan Nemegger: lead developer at ePagine, a French ebook distributor / bookseller and member of EDRLab; implementer of Readium LCP.

