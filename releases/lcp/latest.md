# Readium Licensed Content Protection 1.0.4

*Copyright 2019, Readium Foundation. All Rights Reserved.*

**Latest version:** 

* [https://readium.org/lcp-specs/releases/lcp/latest](latest.md)

**This version:** 

* [https://readium.org/lcp-specs/releases/lcp/latest](latest.md)

**Previous version:** 

* [https://readium.org/lcp-specs/releases/lcp/lcp-1-0-3](lcp-1-0-3.md)

## 1. Overview

### 1.1 Purpose and Scope
*This section is informative*

In order to deliver a Publication to a User with specific rights and restrictions, a Content Provider may want to encrypt the Publication’s Resources and associate them with a specific license. 

This specification, Licensed Content Protection (LCP), defines a standard License Document and an Encryption Profile for encrypting resources in different publication formats (especially EPUB), and for securely delivering decryption keys to Reading Systems via licenses tailored to specific Users. It also defines a simple passphrase-based authentication method for Reading Systems to access encrypted resources and verify a license.

## 1.2. Terminology

### EPUB terms

This specification adopts [terms defined in the EPUB 3 family of specifications](https://www.w3.org/publishing/epub3/epub-spec.html#sec-terminology).  Important terms used include:

<dl>
  <dt>Publication</dt>
  <dd>A logical document entity consisting of a set of interrelated resources, packaged in an EPUB Container.</dd>

  <dt>Publication Resource (or Resource)</dt>
  <dd>A resource that contains content or instructions that contribute to the logic and rendering of the EPUB Publication. </dd>

  <dt>Package Document</dt>
  <dd>A Publication Resource carrying meta information about the EPUB Publication, provides a manifest of resources and defines the default reading order.</dd>

  <dt>EPUB Container (or Container)</dt>
  <dd>The ZIP-based packaging and distribution format for EPUB Publications defined in [<a href="#normative-references">OCF ZIP Container</a>]. </dd>

  <dt>EPUB Reading System (or Reading System)</dt>
  <dd>A system that processes EPUB Publications for presentation to a user in a manner conformant with the EPUB 3 specification.</dd>

  <dt>Codec</dt>
  <dd>Codec refers to content types that have intrinsic binary format qualities, such as video and audio media types which are 
  already designed for optimum compression, or which provide optimized streaming capabilities.</dd>

  <dt>Non-Codec</dt>
  <dd>Non-Codec refers to content types that benefit from compression due to the nature of their internal data structure, 
  such as file formats based on character strings (for example, HTML, CSS, etc.).</dd>

</dl>

### LCP terms

The following terms are defined by this specification:

<dl>
  <dt>Protected Publication</dt>
  <dd>A Publication that has been protected according to this specification.</dd>

  <dt>License Authority</dt>
  <dd>The entity which operates the Readium LCP ecosystem.</dd>

  <dt>License Document</dt>
  <dd>Document that contains references to the various keys, links to related external resources, rights and restrictions that are applied to the Protected Publication, and user information.</dd>

  <dt>Content Key</dt>
  <dd>Symmetric key used to encrypt the resources of the Protected Publication. In the License Document, this Content Key will be encrypted using the User Key.</dd>

  <dt>User</dt>
  <dd>An individual that consumes a Publication using a Reading System.</dd>

  <dt>User Passphrase</dt>
  <dd>A string of text entered by the user that is used to generate the User Key.</dd>

  <dt>User Key</dt>
  <dd>A hash of the User Passphrase that is used to decrypt the Content Key and selected user information fields.</dd>

  <dt>Encryption Profile</dt>
  <dd>A set of encryption algorithms used in a specific Protected Publication and associated Licence Document.</dd>

  <dt>Content Provider (or Provider)</dt>
  <dd>An entity that delivers LCP licenses for Protected Publications to Users.</dd>

  <dt>Provider Certificate</dt>
  <dd>A certificate that is included in the License Document to identify the Content Provider and validate the signature of the License Document.</dd>

  <dt>Root Certificate</dt>
  <dd>A certificate that is embedded in the Reading System in order to confirm that the Provider Certificate is valid.</dd>

</dl>

## 1.3. LCP Overview and Example
*This section is informative*

In order to encrypt Resources and expose its Encryption Profile, LCP relies on two different files:

* License Document (`META-INF/license.lcpl`)

* `META-INF/encryption.xml`

Both of these files are contained inside the EPUB Container, although the License Document can be transmitted initially outside the Container. Following the [[OCF](#normative-references)] specification, `META-INF/encryption.xml` identifies the Publication Resources that are protected and points to the Content Key needed to decrypt them. This Content Key is located inside the License Document and is itself encrypted using the third element of LCP, the User Key.  The User Key is generated by taking a hash of a User Passphrase. The User Key is used to decrypt the Content Key, which in turn is used to decrypt the Publication Resources.

The License Document may also contain links to external resources, information identifying the User, and information about what rights are conveyed to the User and which are not.  Rights information may include things like the time for which the license is valid, whether the book may be printed or copied, etc.  Finally, the License Document always includes a digital signature to prevent modification of any of its components.

*Figure 1: Relationships among the various components of LCP*

![LCP architecture]({{site.section}}images/architecture.png)

### Protecting the Publication

To protect a Publication, the Provider follows these steps:

1. Generate a unique Content Key for the Publication.

2. Store this Content Key for future use in licensing the Publication.

3. Encrypt each protected Resource using that key, after compression if applicable.

4. Add these protected Resources to the Container, replacing the unprotected versions.

5. Create a `META-INF/encryption.xml` document (as described in section 2.2) that includes an EncryptedData element for each protected Resource that contains:

    a. An EncryptionMethod element that lists the algorithm used

    b. A KeyInfo element with a RetrievalMethod child that points to the Content Key in the License Document

    c. A CipherData element that identifies the protected Resource

6. Add `META-INF/encryption.xml` to the Container.

The Publication is now protected (i.e., has become a Protected Publication) and is ready for licensing to one or more Users.

### Licensing the Publication

After a User requests a Protected Publication, the following steps are followed by the Provider to license the Protected Publication:

1. Generate the User Key by hashing the User Passphrase (as described in section 4.2). It is assumed that the User and their User Passphrase are already known to the Provider.

2. Encrypt the Content Key for the Protected Publication using the User Key.

3. Create a License Document (`META-INF/license.lcpl`) with the following contents (as described in section 3):

    a. A unique ID for this license

    b. The date the license was issued

    c. The URI that identifies the Content Provider

    d. The encrypted Content Key

    e. Information about the User Key

    f. Links to additional information stored outside of the Protected Publication and License Document (optional)

    g. Information on specific rights being granted to the User (optional)

    h. Information identifying the User (optional).  Some of the fields in this section may be encrypted using the User Key.

4. Generate a digital signature for the License Document data and add to the License Document.

There are then two different methods to deliver the License Document and Protected Publication to the User:

1. **License Document included inside Protected Publication:**  The Provider adds the License Document to the Protected Publication’s Container and delivers this to the User.

2. **License Document delivered separately:** The Provider includes a link from the License Document to the Protected Publication, and then delivers just the License Document to the User. The Reading System processing the License Document will retrieve the Protected Publication and add the License Document to the Container of this Protected Publication.

Whichever method is used, the Reading System will be presented with an EPUB Container that includes the Protected Publication and the License Document.

### Reading the Publication

In order to decrypt and render a Protected Publication, the User’s Reading System will follow these steps:

1. Verify the signature for the License Document.

2. Get the User Key (if already stored) or generate it by hashing the User Passphrase.

3. Decrypt the Content Key using the User Key.

4. Decrypt the protected Resources using the Content Key.

## 1.4 Conformance Statements

The keywords <b class="rfc">must</b>, <b class="rfc">must not</b>, <b class="rfc">required</b>, <b class="rfc">shall</b>, <b class="rfc">shall not</b>, <b class="rfc">should</b>, <b class="rfc">should not</b>, <b class="rfc">recommended</b>, <b class="rfc">may</b>, and <b class="rfc">optional</b> in this document are to be interpreted as described in [[RFC2119](#normative-references)].

All sections of this specification are normative except where identified by the informative status label "This section is informative". The application of informative status to sections and appendices applies to all child content and subsections they <b class="rfc">may</b> contain.

All examples in this specification are informative.

# 2. Publication

## 2.1. Introduction
*This section is informative*

LCP follows the [[OCF](#normative-references)] specification to identify encrypted Resources within the EPUB Container. Specifically, LCP uses `META-INF/encryption.xml` to indicate which Resources listed in the Package Document are encrypted and to reference the Content Key.

## 2.1. Encrypted Resources

In order to make sure that Protected Publications remain identifiable by Reading Systems, a certain number of Resources are prohibited from being encrypted.

LCP inherits the following list of files prohibited from being encrypted from the [[OCF](#normative-references)] specification:

* `mimetype`

* `META-INF/container.xml`

* `META-INF/encryption.xml`

* `META-INF/manifest.xml`

* `META-INF/metadata.xml`

* `META-INF/rights.xml`

* `META-INF/signatures.xml`

* EPUB `rootfiles` (the Package Document for any rendition)

In addition, this specification defines that the following files <b class="rfc">must not</b> be encrypted:

* `META-INF/license.lcpl`

* Navigation Documents referenced in any Package Document from the Publication (all Publication Resources listed in the Publication manifest with the ["nav" property](https://www.w3.org/publishing/epub32/epub-packages.html#sec-item-property-values))

* NCX documents referenced in any Package Document from the Publication (all Publication Resources listed in the Publication manifest with the media type "application/x-dtbncx+xml")

* Cover images (all Publication Resources listed in the Publication manifest with the ["cover-image" property](https://www.w3.org/publishing/epub32/epub-packages.html#sec-item-property-values))

## 2.2. Using `META-INF/encryption.xml` for LCP

As defined in the [[OCF](#normative-references)] specification, all encrypted Publication Resources <b class="rfc">must</b> be identified in the well-known file `META-INF/encryption.xml` using [[XML-ENC](#normative-references)]. 

Publication Resources with Non-Codec content types <b class="rfc">should</b> be compressed and the Deflate compression algorithm <b class="rfc">must</b> be used. This practice ensures that file entries stored in the Container have a smaller size.

Resources with Codec content types <b class="rfc">should</b> be stored without compression. In such case, compression would introduce unnecessary processing overhead at production time (especially with large resource files) and would impact audio/video playback performance at consumption time.

In Publications protected using LCP, there are additional requirements for determining the length of the full resource ahead of media playback, especially when using partial content requests:

Streams of data that are compressed before they are encrypted <b class="rfc">must</b> provide additional `EncryptionProperties` metadata to specify the size of the initial resource (i.e., before compression and encryption), as per the Compression XML element defined in [OCF, Compression and Encryption](https://www.w3.org/publishing/epub32/epub-ocf.html#sec-enc-compression). 
Streams of data that are not compressed before they are encrypted <b class="rfc">should</b> provide such additional `EncryptionProperties` metadata.

In Publications protected using LCP, the following XML syntax identifies the key used to encrypt Resources (the LCP Content Key):

1. The `ds:KeyInfo` element <b class="rfc">must</b> point to the Content Key using the `ds:RetrievalMethod` element.  

2. The `URI` attribute of `ds:RetrievalMethod` <b class="rfc">must</b> use a value of "`license.lcpl#/encryption/content_key`" to point to the encrypted Content Key stored in the License Document. This URI follows the [[JSON Pointer](#normative-references)] specification.

3. The `Type` attribute <b class="rfc">must</b> use a value of "`http://readium.org/2014/01/lcp#EncryptedContentKey`" to identify the target of the URI as an encrypted Content Key.

*Example1: An `image.jpeg` resource is encrypted using AES with the Content Key in `encryption.xml`.*

```xml
<encryption
    xmlns ="urn:oasis:names:tc:opendocument:xmlns:container"
    xmlns:enc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:ds="http://www.w3.org/2000/09/xmldsig#">

    <enc:EncryptedData Id="ED1">
        <enc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes256-cbc"/>
        <ds:KeyInfo>
            <ds:RetrievalMethod URI="license.lcpl#/encryption/content_key"
                Type="http://readium.org/2014/01/lcp#EncryptedContentKey"/>
        </ds:KeyInfo>
        <enc:CipherData>
            <enc:CipherReference URI="image.jpeg"/>
        </enc:CipherData>
        <enc:EncryptionProperties>
          <enc:EncryptionProperty xmlns:ns="http://www.idpf.org/2016/encryption#compression">
              <ns:Compression Method="8" OriginalLength="35000"/>
          </enc:EncryptionProperty>
      </enc:EncryptionProperties>
    </enc:EncryptedData>
</encryption>
```


# 3. License Document

## 3.1. Introduction
*This section is informative*

While `META-INF/encryption.xml` describes how the Resources are encrypted and where the encrypted Content Key is located, all of the other relevant information for LCP is stored in the License Document.

This specification defines the License Document’s syntax, location in the Container, media type, file extension and processing model.

## 3.2. Content Conformance

A License Document <b class="rfc">must</b> meet all of the following criteria:

Document properties

* It <b class="rfc">must</b> meet the conformance constraints for JSON documents as defined in [[JSON](#normative-references)].

* It <b class="rfc">must</b> be encoded using UTF-8.

File properties

* Its filename <b class="rfc">must</b> use the file extension `.lcpl`.

* Its MIME media type is `application/vnd.readium.lcp.license.v1.0+json`

* Its location in the Container <b class="rfc">must</b> be `META-INF/license.lcpl`

## 3.3. Core License Information

The License Document <b class="rfc">must</b> contain the following name/value pairs:

| Name | Value | Format |
| ---- | ----- | ------ |
| `id` | Unique identifier for the License | String |
| `issued` | Date and time when the license was first issued | ISO 8601 |
| `provider` | Unique identifier for the Provider | URI |

In addition, the License Document <b class="rfc">may</b> contain the following name/value pair:

| Name | Value | Format |
| ---- | ----- | ------ |
| `updated` | Date and time when the license was last updated | ISO 8601 |

## 3.4. Transmitting keys: The `encryption` object

In addition to Core License information, the License Document <b class="rfc">must</b> contain an `encryption` object with the following name/value pair:

| Name | Value | Format |
| ---- | ----- | ------ |
| `profile` | Identifies the Encryption Profile used by this LCP-protected Publication | URI |

The `encryption` object <b class="rfc">must</b> also contain the following two objects: `content_key` and `user_key`.

The `encryption/content_key` object contains the Content Key (encrypted using the User Key) used to encrypt the Publication Resources. It <b class="rfc">must</b> contain the following name/value pairs:

| Name | Value | Format |
| ---- | ----- | ------ |
| `encrypted_value` | Encrypted Content Key | Base 64 encoded octet sequence |
| `algorithm` | Algorithm used to encrypt the Content Key, identified using the URIs defined in [XML-ENC]. This <b class="rfc">must</b> match the Content Key encryption algorithm named in the Encryption Profile identified in `encryption/profile`. | URI |

The `encryption/user_key` object contains information regarding the User Key used to encrypt the Content Key. It <b class="rfc">must</b> contain the following name/value pairs:

| Name | Value | Format |
| ---- | ----- | ------ |
| `text_hint` | A hint to be displayed to the User to help them remember the User Passphrase | String |
| `algorithm` | Algorithm used to generate the User Key from the User Passphrase, identified using the URIs defined in [[XML-ENC]((#normative-references))]. This <b class="rfc">must</b> match the User Key hash algorithm named in the Encryption Profile identified in `encryption/profile`. | URI |
| `key_check` | The value of the License Document’s `id` field, encrypted using the User Key and the same algorithm identified for Content Key encryption in `encryption/content_key/algorithm`. This is used to verify that the Reading System has the correct User Key. | Base 64 encoded octet sequence |

*Example 2: Encryption information for a License Document that uses the base Encryption Profile for LCP 1.0.*

```json
{
  "id": "ef15e740-697f-11e3-949a-0800200c9a66",
  "issued": "2013-11-04T01:08:15+01:00",
  "updated": "2014-02-21T09:44:17+01:00",
  "provider": "https://www.imaginaryebookretailer.com",
  "encryption": {
    "profile": "http://readium.org/lcp/basic-profile",
    "content_key": {
      "encrypted_value": "/k8RpXqf4E2WEunCp76E8PjhS051NXwAXeTD1ioazYxCRGvHLAck/KQ3cCh5JxDmCK0nRLyAxs1X0aA3z55boQ==",
      "algorithm": "http://www.w3.org/2001/04/xmlenc#aes256-cbc"
    },
    "user_key": {
      "text_hint": "Enter your email address",
        "algorithm": "http://www.w3.org/2001/04/xmlenc#sha256",
        "key_check": "jJEjUDipHK3OjGt6kFq7dcOLZuicQFUYwQ+TYkAIWKm6Xv6kpHFhF7LOkUK/Owww"
    }
  },
  "links": "...",
  "rights": "...",
  "signature": "..."
}
```


## 3.5. Pointing to external resources: the `links` object

A License Document <b class="rfc">must</b> also contain a `links` object.  This is used to associate the License Document with resources that are not locally available.  

Each Link Object nested in `links` contains the link URI, a link relationship and an <b class="rfc">optional</b> set of other link properties.

### Link relationships

This specification introduces the following link relationships for each Link Object:

| Relation | Semantics | Required? |
| -------- | --------- | --------- |
| `hint` | Location where a Reading System can redirect a User looking for additional information about the User Passphrase | Yes |
| `publication` | Location where the Publication associated with the License Document can be downloaded | Yes |
| `self` | As defined in the IANA registry of link relations: "Conveys an identifier for the link's context." | No |
| `support` | Support resources for the user (either a website, an email or a telephone number) | No |

In addition to these link relations, this specification introduces the [LCP Link Relations Registry](#informative-references), in which all link relations defined in this specification are referenced.

Link relations <b class="rfc">may</b> also be extended for vendor-specific applications. Such links <b class="rfc">must</b> use a URI instead of a string to identify their link relations.

### Link Object

Each Link Object supports the following keys:

| Name | Value | Format | Required? |
| ---- | ----- | ------ | --------- |
| `href` | Location of the linked resources | URI or URI Template | Yes |
| `rel` | Link relationship to the document | List of well-known relation values, URIs for extensions | Yes |
| `title` | Title of the link | String | No |
| `type` | Expected MIME media type value for the external resources | MIME media type | No, but highly recommended |
| `templated` | Indicates that the href is a URI Template | Boolean | No, default value is "false" |
| `profile` | Expected profile used to identify the external resource | URI | No |
| `length` | Content length in octets | Integer | No |
| `hash` | SHA-256 hash of the resource | Base 64 encoded octet sequence | No |

Templated URIs follow the [[URI-Template]](#normative-references) specification.
 
*Example 3: A License Document that points to a publication, contains the location of a hint about its User Passphrase and uses an extension to provide authentication and recommandation services.*

```json
{
  "id": "ef15e740-697f-11e3-949a-0800200c9a66",
  "issued": "2013-11-04T01:08:15+01:00",
  "updated": "2014-02-21T09:44:17+01:00",
  "provider": "https://www.imaginaryebookretailer.com",
  "encryption": "...",
  "links": [
    { "rel": "publication",
      "href": "https://www.example.com/file.epub",
      "type": "application/epub+zip",
      "length": "264929",
      "hash": "8b752f93e5e73a3efff1c706c1c2e267dffc6ec01c382cbe2a6ca9bd57cc8378"
    },
    { "rel": "hint",
      "href": "https://www.example.com/passphraseHint?user_id=1234",
      "type": "text/html"
    },
    { "rel": "support",
      "href": "mailto:support@example.org"
    }, 
    { "rel": "support",
      "href": "tel:1800836482"
    },
    { "rel": "support",
      "href": "https://example.com/support",
      "type": "text/html"
    }, 
    { "rel": "https://mylcpextension.com/authentication",
      "href": "https://www.example.com/authenticateMe",
      "title": "Authentication service",
      "type": "application/vnd.myextension.authentication+json"
    },
    { "rel": "https://mylcpextension.com/book_recommendations",
      "href": "https://www.example.com/recommended/1", 
      "type": "text/html"
    },
    { "rel": "https://mylcpextension.com/book_recommendations",
      "href": "https://www.example.com/recommended/1.opds",
      "type": "application/atom+xml; profile=opds-catalog; kind=acquisition"}
  ], 
  "rights": "...",
  "signature": "..."
}
```


## 3.6. Identifying rights and restrictions: the `rights` object

The License Document <b class="rfc">may</b> also express a series of rights using the `rights` object.  The `rights` object <b class="rfc">may</b> include the following fields:

| Name | Value | Format | Default |
| ---- | ----- | ------ | ------- |
| `print` | Maximum number of pages that can be printed over the lifetime of the license | Integer | Unlimited |
| `copy` | Maximum number of characters that can be copied to the clipboard over the lifetime of the license | Integer | Unlimited |
| `start` | Date and time when the license begins | ISO 8601 | None (perpetual license) |
| `end` | Date and time when the license ends | ISO 8601 | None (perpetual license) |

<br/>
All name/value pairs using an integer as their data type (print and copy for this specification) <b class="rfc">must not</b> use leading zeroes in values (e.g., "9", not “09”).

For the `print` right, a page is defined as follows:

1. The page as defined in the Publication, if it is pre-paginated (fixed layout) OR

2. The page as defined by the [page-list nav element](https://www.w3.org/publishing/epub32/epub-packages.html#sec-nav-pagelist) of the [EPUB Navigation Document](https://www.w3.org/publishing/epub32/epub-packages.html#sec-package-nav-def), if this exists OR

3. 1024 Unicode characters for all other cases

The `copy` right only covers the ability to copy to the clipboard and is limited to text (no images, etc.).

In addition to these rights, this specification introduces the [LCP Rights Registry](#informative-references), in which all rights  defined in this specification are referenced.

The `rights` object <b class="rfc">may</b> be extended to include any number of implementor-specific rights. Each extension right <b class="rfc">must</b> be identified using a URI controlled by the implementor.

*Example 4:  License Document that grants a number of rights such as not allowing print at all, copying up to 2048 characters, tweeting (using a vendor extension) and a validity limited in time.*

```json
{
  "id": "ef15e740-697f-11e3-949a-0800200c9a66",
  "issued": "2013-11-04T01:08:15+01:00",
  "updated": "2014-02-21T09:44:17+01:00",
  "provider": "https://www.imaginaryebookretailer.com",
  "encryption": "...",
  "links": "...",
  "rights": {
    "print": 0,
    "copy": 2048,
    "start": "2013-11-04T01:08:15+01:00",
    "end": "2013-11-25T01:08:15+01:00",
    "https://www.imaginaryebookretailer.com/lcp/rights/tweet": true
  },
  "signature": "..."
}
```

## 3.7. Identifying the user: the `user` object

The License Document <b class="rfc">may</b> embed information about the user using the `user` object. The `user` object includes the following fields:

| Name | Value | Format | Required? |
| ---- | ----- | ------ | --------- |
| `id` | Unique identifier for the User at a specific Provider | String | No, but highly recommended |
| `email` | The User’s e-mail address | String | No |
| `name` | The User’s name | String | No |
| `encrypted` | A list of which user object values are encrypted in this License Document | Array of one or more strings matching the above names or an extension | Yes, if encryption is used for any field |

In addition to these user information, this specification introduces the [LCP User Fields Registry](#informative-references), in which all user fields defined in this specification are referenced.

As with rights, The `user` object <b class="rfc">may</b> be extended to include any number of implementor-specific fields. Each extension field <b class="rfc">must</b> be identified by a URI controlled by the implementor.

To protect private User data, any of these fields <b class="rfc">may</b> be encrypted, except for the `encrypted` field, which <b class="rfc">must</b> remain in plain text. If encrypted, the field values <b class="rfc">must</b> be encrypted using the User Key and the same encryption algorithm identified in the `encryption/content_key` object. The names of all encrypted fields <b class="rfc">must</b> be listed in the `encrypted` array.

*Example 5: License Document where an identifier, a provider and an email are provided for the user. There is also an extension to indicate the user’s preferred language. The email is encrypted.*

```json
{
  "id": "ef15e740-697f-11e3-949a-0800200c9a66",
  "issued": "2013-11-04T01:08:15+01:00",
  "updated": "2014-02-21T09:44:17+01:00",
  "provider": "https://www.imaginaryebookretailer.com",
  "encryption": "...",
  "links": "...",
  "rights": "...",
  "user": {
    "id": "d9f298a7-7f34-49e7-8aae-4378ecb1d597",
    "email": "EnCt2b8c6d2afd94ae4ed201b27049d8ce1afe31a90ceb8c6d2afd94ae4ed201b2704RjkaXRveAAarHwdlID1KCIwEmS",
    "encrypted": ["email"],
      "https://www.imaginaryebookretailer.com/lcp/user/language": "tlh"
  },
  "signature": "..."
}
```

## 3.8. Signing the license: the `signature` object

As described in [5. Signature and Public Key Infrastructure](#5-signature-and-public-key-infrastructure), the License Document includes a digital signature to validate that it has not been altered.  The License Document <b class="rfc">must</b> include information about the signature using the `signature` object. The `signature` object <b class="rfc">must</b> include the following fields:


| Name | Value | Format |
| ---- | ----- | ------ |
| `algorithm` | Algorithm used to calculate the signature, identified using the URIs given in [[XML-SIG](#normative-references)].  This <b class="rfc">must</b> match the signature algorithm named in the Encryption Profile identified in `encryption/profile` | URI |
| `certificate` | The Provider Certificate: an X509 certificate used by the Content Provider | Base 64 encoded DER certificate |
| `value` | Value of the signature | Base 64 encoded octet sequence |

For more information on how the signature and the certificate <b class="rfc">should</b> be calculated, encoded and processed, see [5. Signature and Public Key Infrastructure](#5-signature-and-public-key-infrastructure).

*Example 6: Signature based on the basic LCP encryption profile.*

```json
{
  "id": "ef15e740-697f-11e3-949a-0800200c9a66",
  "issued": "2013-11-04T01:08:15+01:00",
  "updated": "2014-02-21T09:44:17+01:00",
  "provider": "https://www.imaginaryebookretailer.com",
  "encryption": "...",
  "links": "...",
  "rights": "...",
  "user": "...",
  "signature": {
    "algorithm": "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256",
    "certificate": "MIIDEjCCAfoCCQDwMOjkYYOjPjANBgkqhkiG9w0BAQUFADBLMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTETMBEGA1UEBxMKRXZlcnl3aGVyZTESMBAGA1UEAxMJbG9jYWxob3N0MB4XDTE0MDEwMjIxMjYxNloXDTE1MDEwMjIxMjYxNlowSzELMAkGA1UEBhMCVVMxEzARBgNVBAgTCkNhbGlmb3JuaWExEzARBgNVBAcTCkV2ZXJ5d2hlcmUxEjAQBgNVBAMTCWxvY2FsaG9zdDCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAOpCRECG7icpf0H37kuAM7s42oqggBoikoTpo5yapy+s5eFSp8HSqwhIYgZ4SghNLkj3e652SALav7chyZ2vWvitZycY+aq50n5UTTxDvdwsC5ZNeTycuzVWZALKGhV7VUPEhtWZNm0gruntronNa8l2WS0aF7P5SbhJ65SDQGprFFaYOSyN6550P3kqaAO7tDddcA1cmuIIDRf8tOIIeMkBFk1Qf+lh+3uRP2wztOTECSMRxX/hIkCe5DRFDK2MuDUyc/iY8IbY0hMFFGw5J7MWOwZLBOaZHX+Lf5lOYByPbMH78O0dda6T+tLYAVzsmJdHJFtaRguCaJVtSXKQUAMCAwEAATANBgkqhkiG9w0BAQUFAAOCAQEAi9HIM+FMfqXsRUY0rGxLlw403f3YtAG/ohzt5i8DKiKUG3YAnwRbL/VzXLZaHru7XBC40wmKefKqoA0RHyNEddXgtY/aXzOlfTvp+xirop+D4DwJIbaj8/wHKWYGBucA/VgGY7JeSYYTUSuz2RoYtjPNRELIXN8A+D+nkJ3dxdFQ6jFfVfahN3nCIgRqRIOt1KaNI39CShccCaWJ5DeSASLXLPcEjrTi/pyDzC4kLF0VjHYlKT7lq5RkMO6GeC+7YFvJtAyssM2nqunA2lUgyQHb1q4Ih/dcYOACubtBwW0ITpHz8N7eO+r1dtH/BF4yxeWl6p5kGLvuPXNU21ThgA==",
    "value": "q/3IInic9c/EaJHyG1Kkqk5v1zlJNsiQBmxz4lykhyD3dA2jg2ZzrOenYU9GxP/xhe5H5Kt2WaJ/hnt8+GWrEx1QOwnNEij5CmIpZ63yRNKnFS5rSRnDMYmQT/fkUYco7BUi7MPPU6OFf4+kaToNWl8m/ZlMxDcS3BZnVhSEKzUNQn1f2y3sUcXjes7wHbImDc6dRthbL/E+assh5HEqakrDuA4lM8XNfukEYQJnivqhqMLOGM33RnS5nZKrPPK/c2F/vGjJffSrlX3W3Jlds0/MZ6wtVeKIugR06c56V6+qKsnMLAQJaeOxxBXmbFdAEyplP9irn4D9tQZKqbbMIw=="
  }
}
```

# 4. User Key

## 4.1. Introduction
*This section is informative*

In order for any symmetrically-encrypted message to work, there must be an agreed-upon key shared by the sender and receiver.  In LCP, this is the User Key. The Provider must have access to the User Key in order to secure the Content Key within the License Document. The Reading System must also have the User Key in order to decrypt the Content Key in the License Document.

LCP uses a passphrase model for sharing the User Key: in a simple implementation, when the Reading System receives a new License Document, it prompts the User for a passphrase to access the Content Key.  LCP defines the User Key as a hash of this User Passphrase. This passphrase can be anything at all: a User-defined password, a Provider-defined password, an e-mail address, a library card number, etc. 

## 4.2. Calculating the User Key

The User Passphrase <b class="rfc">must</b> be a UTF-8 encoded string. There are no restrictions on the length or content of the User Passphrase. There are no requirements for how it is to be created.

The User Key is the result of applying a hashing function on the User Passphrase, using the hash algorithm provided in the `encryption/user_key` object in the License Document. Processing of any kind, including whitespace escaping or normalization, <b class="rfc">shall not</b> be done on the User Passphrase before hashing.

## 4.3. Hints

In order to facilitate the entry of the passphrase by the User, the LCP License Document supports two ways for the Reading System to provide passphrase prompts or hints to the User:

1. The `user_key/text_hint` field is a simple prompt that can be displayed to the User when entering the User Passphrase (e.g., "Enter your Imaginary Book Retailer password").

2. The `links` object with `rel=hint` points to a location that provides passphrase hints or other assistance.

The content of `user_key/text_hint` and of the resource pointed to by the `links` object with `rel=hint` <b class="rfc">should</b> be human-readable, directed to the User, and <b class="rfc">should</b> help the User enter their passphrase.

## 4.4. Requirements for the User Key & User Passphrase

In order to simplify the process for accessing Protected Publications, the Provider <b class="rfc">should</b> use the same User Key for all licenses issued to the same User.

The security of LCP depends in large part on the security of the User Key and User Passphrase. Therefore, special care <b class="rfc">should</b> be taken to secure these throughout the licensing workflow:

1. Passphrases <b class="rfc">should</b> be sufficiently complex to prevent brute-force attacks.

2. Passphrases <b class="rfc">must not</b> be transmitted in plaintext. 

3. If the Provider needs to share User Key information among multiple systems, they <b class="rfc">should</b> transmit User Keys and not User Passphrases, and <b class="rfc">should</b> transmit this information over secure channels. 

4. User Passphrases <b class="rfc">must not</b> be stored by the Reading System; instead it <b class="rfc">should</b> only store User Keys.

# 5. Signature and Public Key Infrastructure

## 5.1. Introduction
*This section is informative*

Given the importance of the precise expression of various objects in the License Document, it is critical that the Reading System be able to verify that the content of the License Document is authentic and has not been altered. This is done via a digital signature that is verified via a Public Key Infrastructure as defined in [[X509](#normative-references)].

Calculating a signature is done on a byte stream, which is unique, while the License Document is a JSON document where multiple representations might lead to the same structure. Thus, to ensure a stable signature between the Reading System and the Content Provider, some transformations must be applied prior to signing the Document and verifying the signature.

The steps <b class="rfc">required</b> of the Provider to sign the License Document are:

1. The contents of the License Document (minus the signature object) are put in a canonical form: alphabetized with non-significant whitespace removed (see [Section 5.3](#53-canonical-form-of-the-license-document))

2. The signature value for the canonical form of the License Document is calculated following the algorithm identified in the Encryption Profile.  This typically will involve taking a hash of the data and encrypting it using the private key.

3. The signature value and the Provider Certificate are added to the License Document in the `signature` object, as described in [Section 3.8](#38-signing-the-license-the-signature-object).

The steps <b class="rfc">required</b> of the Reading System to validate the signature are:

1. Obtain the Root Certificate from the License Authority.  This certificate is embedded in the Reading System to validate all Provider Certificates.

2. Using this Root Certificate, validate the Provider Certificate that is included in the `signature` object. This prevents another entity who is not the Provider from forging License Documents. 

3. Strip the `signature` object from the License Document.

4. Put the remainder of the License Document contents into canonical form.

5. Calculate the hash of the canonical License Document data using the signature algorithm identified in the Encryption Profile.

6. Decrypt the signature value given in the signature object using the public key given in the Provider Certificate and confirm that it matches the calculated hash.  This confirms that the contents of the License Document have not been altered in transit.

Because the Provider Certificate is included in the License Document and the Root Certificate is embedded in the Reading System, the entire validation process can take place without any Internet connectivity.

To make sure that the Provider Certificate has not been revoked, the Reading System also checks a Certificate Revocation List maintained by the Readium Foundation.  In order to facilitate offline reading, the Reading System does not need to check the revocation list every time it processes a License Document.  It is only necessary that it updates the list regularly when an Internet connection is available (e.g., every time it downloads a new License or book).

## 5.2. Certificates

### 5.2.1 Provider Certificates

Content Providers <b class="rfc">must</b> have a Certificate in the [[X509](#normative-references)] v3 format issued and signed by the License Authority using the Root Certificate: this is referred to here as the Provider Certificate. The subject of the Provider Certificate <b class="rfc">should</b> represent the Content Provider. 

Content Providers <b class="rfc">must</b> distribute their Provider Certificate in any License Document they issue in the `signature/certificate` field. They also <b class="rfc">must</b> use the private key paired with their Provider Certificate’s public key to sign the License Document. For a License Document to be considered valid, the Provider Certificate <b class="rfc">must</b>  have been valid at the time the License Document was issued (as indicated by the `issued` field), and the Provider Certificate <b class="rfc">must not</b> have been revoked.

### 5.2.2 Root Certificate

Reading Systems <b class="rfc">must</b> obtain the Root Certificate in the [[X509](#normative-references)] v3 format from the License Authority, and <b class="rfc">should</b> keep it up to date. It <b class="rfc">must</b> be embedded in the Reading System for offline use.

## 5.3. Canonical form of the License Document

The canonical form of the License Document is used when calculating and validating the signature. To create the canonical form of the License Document, the following serialization rules must be followed:

1. Since it is product of the calculation, the `signature` object of the License Document <b class="rfc">must</b> be removed.

2. All object members (name/value pairs) of the License Document <b class="rfc">must</b>** **be sorted in lexicographical order of their names according to their representation in UTF-8 (United Character Set code point value). Note that this rule is recursive, so that members are sorted at all levels of object nesting.

3. Within arrays, the order of elements <b class="rfc">must not</b> be altered.

4. Numbers <b class="rfc">must not</b> include insignificant leading or trailing zeroes. Numbers that include a fraction part (non-integers) <b class="rfc">must</b> be expressed as a number, fraction, and exponent (normalized scientific notation) using an upper-case "E".

5. Strings <b class="rfc">must</b> use escaping only for those characters for which it is <b class="rfc">required</b> by [[JSON](#normative-references)]: backslash (\), double-quotation mark ("), and control characters (U+0000 through U+001F). When escaping control characters, the hexadecimal digits <b class="rfc">must</b> be upper case.

6. Non-significant whitespace (as defined in [[JSON](#normative-references)]) <b class="rfc">must</b> be removed. Whitespace found within strings <b class="rfc">must</b> be kept.

### 5.3.1. Example
*This section is informative*

For this example, we'll use a License Document which contains a link (hint) and basic user information.

```json
{
  "id": "ef15e740-697f-11e3-949a-0800200c9a66",
  "issued": "2013-11-04T01:08:15+01:00",
  "updated": "2014-02-21T09:44:17+01:00",
  "provider": "https://www.imaginaryebookretailer.com",
  "encryption": {
    "profile": "http://readium.org/lcp/basic-profile",
    "content_key": {
      "encrypted_value": "/k8RpXqf4E2WEunCp76E8PjhS051NXwAXeTD1ioazYxCRGvHLAck/KQ3cCh5JxDmCK0nRLyAxs1X0aA3z55boQ==",
      "algorithm": "http://www.w3.org/2001/04/xmlenc#aes256-cbc"
    },
    "user_key": {
      "text_hint": "Enter your email address",
        "algorithm": "http://www.w3.org/2001/04/xmlenc#sha256",
        "key_check": "jJEjUDipHK3OjGt6kFq7dcOLZuicQFUYwQ+TYkAIWKm6Xv6kpHFhF7LOkUK/Owww"
    }
  },
  "links": [
    { "rel": "hint", "href": "https://www.imaginaryebookretailer.com/lcp/hint", "type": "text/html"}
  ],
  "user": { "id": "d9f298a7-7f34-49e7-8aae-4378ecb1d597"}
}
```


First of all, let's sort this document. Every [[JSON](#normative-references)] object needs to be sorted:

```json
{
  "encryption": {
    "content_key": {
      "algorithm": "http://www.w3.org/2001/04/xmlenc#aes256-cbc",
      "encrypted_value": "/k8RpXqf4E2WEunCp76E8PjhS051NXwAXeTD1ioazYxCRGvHLAck/KQ3cCh5JxDmCK0nRLyAxs1X0aA3z55boQ=="
    },
    "profile": "http://readium.org/lcp/basic-profile",
    "user_key": {
      "algorithm": "http://www.w3.org/2001/04/xmlenc#sha256",
      "key_check": "jJEjUDipHK3OjGt6kFq7dcOLZuicQFUYwQ+TYkAIWKm6Xv6kpHFhF7LOkUK/Owww",
      "text_hint": "Enter your email address"
    }
  },
  "id": "ef15e740-697f-11e3-949a-0800200c9a66",
  "issued": "2013-11-04T01:08:15+01:00",
  "links": [
    {
      "rel": "hint",
      "href": "https://www.imaginaryebookretailer.com/lcp/hint",
      "type": "text/html"
    }
  ],
  "provider": "https://www.imaginaryebookretailer.com",
  "updated": "2014-02-21T09:44:17+01:00",
  "user": {"id": "d9f298a7-7f34-49e7-8aae-4378ecb1d597"}
}
```


Now that our document is sorted, we can strip all whitespaces and end of lines:

```json
{"encryption":{"content_key":{"algorithm":"http://www.w3.org/2001/04/xmlenc#aes256-cbc","encrypted_value":"/k8RpXqf4E2WEunCp76E8PjhS051NXwAXeTD1ioazYxCRGvHLAck/KQ3cCh5JxDmCK0nRLyAxs1X0aA3z55boQ=="},"profile":"http://readium.org/lcp/basic-profile","user_key":{"algorithm":"http://www.w3.org/2001/04/xmlenc#sha256","key_check":"jJEjUDipHK3OjGt6kFq7dcOLZuicQFUYwQ+TYkAIWKm6Xv6kpHFhF7LOkUK/Owww","text_hint":"Enter your email address"}},"id":"ef15e740-697f-11e3-949a-0800200c9a66","issued":"2013-11-04T01:08:15+01:00","links":[{"rel":"hint","href":"https://www.imaginaryebookretailer.com/lcp/hint","type":"text/html"}],"provider":"https://www.imaginaryebookretailer.com","updated":"2014-02-21T09:44:17+01:00","user":{"id":"d9f298a7-7f34-49e7-8aae-4378ecb1d597"}}
```


## 5.4. Generating the signature

In order to sign a License Document, the Content Provider <b class="rfc">must</b> go through the following steps in order:

1. The Content Provider <b class="rfc">must</b> create the canonical form of the License Document following the rules given in [5.3. Canonical form of the License Document](#53-canonical-form-of-the-license-document).

2. The Content Provider <b class="rfc">must</b> calculate the signature using this canonical form of the License Document and using the algorithm described in the `algorithm` field with the private key of the Provider Certificate.

3. The signature <b class="rfc">must</b> be embedded in the `value` field of the `signature` object using Base 64 encoding.

4. The Provider Certificate used to validate the signature <b class="rfc">must</b> be inserted in the `certificate` field. It <b class="rfc">must</b> use the DER notation and be encoded using Base 64.

### 5.4.1. Example
*This section is informative*

Given the License Document in its canonical form

```json
{"encryption":{"content_key":{"algorithm":"http://www.w3.org/2001/04/xmlenc#aes256-cbc","encrypted_value":"/k8RpXqf4E2WEunCp76E8PjhS051NXwAXeTD1ioazYxCRGvHLAck/KQ3cCh5JxDmCK0nRLyAxs1X0aA3z55boQ=="},"profile":"http://readium.org/lcp/basic-profile","user_key":{"algorithm":"http://www.w3.org/2001/04/xmlenc#sha256","key_check":"jJEjUDipHK3OjGt6kFq7dcOLZuicQFUYwQ+TYkAIWKm6Xv6kpHFhF7LOkUK/Owww","text_hint":"Enter your email address"}},"id":"ef15e740-697f-11e3-949a-0800200c9a66","issued":"2013-11-04T01:08:15+01:00","links":[{"rel":"hint","href":"https://www.imaginaryebookretailer.com/lcp/hint","type":"text/html"}],"provider":"https://www.imaginaryebookretailer.com","updated":"2014-02-21T09:44:17+01:00","user":{"id":"d9f298a7-7f34-49e7-8aae-4378ecb1d597"}}
```

Using the signature algorithm required by the Encryption Profile, a Content Provider must first hash the License Document, giving the following byte sequence, represented here in hexadecimal:

```
23c68442c7214ba294ddd1a2902756e9fe575116a88f36e55baf94590a90c2ad
```

This SHA-256 form must then be signed using the Content Provider’s Private Key, giving the following result, in Base 64:

```
q/3IInic9c/EaJHyG1Kkqk5v1zlJNsiQBmxz4lykhyD3dA2jg2ZzrOenYU9GxP/xhe5H5Kt2WaJ/hnt8+GWrEx1QOwnNEij5CmIpZ63yRNKnFS5rSRnDMYmQT/fkUYco7BUi7MPPU6OFf4+kaToNWl8m/ZlMxDcS3BZnVhSEKzUNQn1f2y3sUcXjes7wHbImDc6dRthbL/E+assh5HEqakrDuA4lM8XNfukEYQJnivqhqMLOGM33RnS5nZKrPPK/c2F/vGjJffSrlX3W3Jlds0/MZ6wtVeKIugR06c56V6+qKsnMLAQJaeOxxBXmbFdAEyplP9irn4D9tQZKqbbMIw==
```


With this signature and the certificate, a valid license <b class="rfc">may</b> be created:

```json
{
  "date": "2013-11-04T01:08:15+01:00",
  "encryption": {
    "content_key": {
      "algorithm": "http://www.w3.org/2001/04/xmlenc#aes256-cbc",
      "encrypted_value": "/k8RpXqf4E2WEunCp76E8PjhS051NXwAXeTD1ioazYxCRGvHLAck/KQ3cCh5JxDmCK0nRLyAxs1X0aA3z55boQ=="
    },
    "profile": "http://readium.org/lcp/basic-profile",
    "user_key": {
      "algorithm": "http://www.w3.org/2001/04/xmlenc#sha256",
      "text_hint": "Enter your email address"
    }
  },
  "id": "ef15e740-697f-11e3-949a-0800200c9a66",
  "links": [
    { "rel": "hint",
      "href": "https://www.imaginaryebookretailer.com/lcp/hint",
      "type": "text/html"
    }
  ],  
  "user": {"id": "d9f298a7-7f34-49e7-8aae-4378ecb1d597"},
  "signature": {
    "algorithm": "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256",
    "certificate": "MIIDEjCCAfoCCQDwMOjkYYOjPjANBgkqhkiG9w0BAQUFADBLMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTETMBEGA1UEBxMKRXZlcnl3aGVyZTESMBAGA1UEAxMJbG9jYWxob3N0MB4XDTE0MDEwMjIxMjYxNloXDTE1MDEwMjIxMjYxNlowSzELMAkGA1UEBhMCVVMxEzARBgNVBAgTCkNhbGlmb3JuaWExEzARBgNVBAcTCkV2ZXJ5d2hlcmUxEjAQBgNVBAMTCWxvY2FsaG9zdDCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAOpCRECG7icpf0H37kuAM7s42oqggBoikoTpo5yapy+s5eFSp8HSqwhIYgZ4SghNLkj3e652SALav7chyZ2vWvitZycY+aq50n5UTTxDvdwsC5ZNeTycuzVWZALKGhV7VUPEhtWZNm0gruntronNa8l2WS0aF7P5SbhJ65SDQGprFFaYOSyN6550P3kqaAO7tDddcA1cmuIIDRf8tOIIeMkBFk1Qf+lh+3uRP2wztOTECSMRxX/hIkCe5DRFDK2MuDUyc/iY8IbY0hMFFGw5J7MWOwZLBOaZHX+Lf5lOYByPbMH78O0dda6T+tLYAVzsmJdHJFtaRguCaJVtSXKQUAMCAwEAATANBgkqhkiG9w0BAQUFAAOCAQEAi9HIM+FMfqXsRUY0rGxLlw403f3YtAG/ohzt5i8DKiKUG3YAnwRbL/VzXLZaHru7XBC40wmKefKqoA0RHyNEddXgtY/aXzOlfTvp+xirop+D4DwJIbaj8/wHKWYGBucA/VgGY7JeSYYTUSuz2RoYtjPNRELIXN8A+D+nkJ3dxdFQ6jFfVfahN3nCIgRqRIOt1KaNI39CShccCaWJ5DeSASLXLPcEjrTi/pyDzC4kLF0VjHYlKT7lq5RkMO6GeC+7YFvJtAyssM2nqunA2lUgyQHb1q4Ih/dcYOACubtBwW0ITpHz8N7eO+r1dtH/BF4yxeWl6p5kGLvuPXNU21ThgA==",
    "value": "q/3IInic9c/EaJHyG1Kkqk5v1zlJNsiQBmxz4lykhyD3dA2jg2ZzrOenYU9GxP/xhe5H5Kt2WaJ/hnt8+GWrEx1QOwnNEij5CmIpZ63yRNKnFS5rSRnDMYmQT/fkUYco7BUi7MPPU6OFf4+kaToNWl8m/ZlMxDcS3BZnVhSEKzUNQn1f2y3sUcXjes7wHbImDc6dRthbL/E+assh5HEqakrDuA4lM8XNfukEYQJnivqhqMLOGM33RnS5nZKrPPK/c2F/vGjJffSrlX3W3Jlds0/MZ6wtVeKIugR06c56V6+qKsnMLAQJaeOxxBXmbFdAEyplP9irn4D9tQZKqbbMIw=="
  }
}
```

## 5.5. Validating the certificate and signature

#### **5.5.1. Validating the certificate**

1. It <b class="rfc">must</b> check that the Certificate was not expired when the License Document was last updated.

2. It <b class="rfc">must</b> validate the presence of the Provider Certificate in the root chain. To do so, it <b class="rfc">must</b> check the signature of the Provider Certificate using the public key of the Root Certificate.

3. It <b class="rfc">must</b> validate that the certificate was not revoked as defined in [[X509](#normative-references)]. If a network connection is available, it <b class="rfc">must</b> update its certificate revocation list before it checks the validity of the certificate.

#### **5.5.2. Validating the signature**

In order to validate the signature, the following steps <b class="rfc">must</b> be followed in order:

1. The Reading System <b class="rfc">must</b> extract and remove the signature from the License Document. 

2. It <b class="rfc">must</b> calculate the canonical form of the License Document following the rules as expressed in [5.3. Canonical form of the License Document](#53-canonical-form-of-the-license-document).

3. It <b class="rfc">must</b> recalculate the signature as defined in [5.4. Generating the signature](#54-generating-the-signature).

4. It <b class="rfc">must</b> verify that the calculated signature value is consistent with the one previously extracted from the License Document.

# 6. Encryption Profiles

## 6.1. Introduction
*This section is informative*

LCP is entirely based on standard encryption algorithms, as defined in [[XML-ENC](#normative-references)] and [[XML-SIG](#normative-references)]. In order to maintain maximum flexibility, no specific algorithms are mandated by this specification. Instead, the design of both `encryption.xml` and the License Document allow for the identification of encryption algorithms to be discovered by Reading Systems when presented with a Protected Publication.

In order to simplify this discovery process, the LCP 1.0 specification defines an Encryption Profile, which is the set of encryption algorithms used in a specific Protected Publication and associated Licence Document. Reading Systems that implement the algorithms identified in the Encryption Profile will be able to decrypt Protected Publications encoded using that Encryption Profile. For ease of discovery, the Encryption Profile is identified in the License Document.

This specification defines the Basic Encryption Profile 1.0, along with a list of associated algorithms extracted from [[XML-ENC](#normative-references)] or [[XML-SIG](#normative-references)]. All future official or vendor-specific extensions will also define such an Encryption Profile for easy identification by Reading Systems and publish such profiles in the [LCP Encryption Profiles Registry](#informative-references).

## 6.2. Encryption Profile Requirements

All Encryption Profiles <b class="rfc">must</b> identify algorithms for the following targets:  

1. Publication Resources

2. Content Key and User fields (if encrypted)

3. User Passphrase

4. Signature

All algorithms used in an Encryption Profile <b class="rfc">must</b> be defined in [[XML-ENC](#normative-references)] or [[XML-SIG](#normative-references)].

All Encryption Profiles <b class="rfc">must</b> use a URI to identify themselves in `profile` (contained in the `encryption` object of the License Document).

All Encryption Profiles <b class="rfc">must</b> be registered in the LCP Encryption Profiles registry, as explicitly explained in the registry.

## 6.3. Basic Encryption Profile 1.0

Basic Encryption Profile 1.0 is identified in the `encryption` object of the License Document using the URL `http://readium.org/lcp/basic-profile` for the `profile` attribute value.

The following algorithms are associated to the Basic Encryption Profile 1.0:

| Encryption target | Algorithm (name) | Algorithm (URI) | Identified in |
| ----------------- | ---------------- | --------------- | ------------- |
| Publication Resources | AES 256 bits CBC | [http://www.w3.org/2001/04/xmlenc#aes256-cbc](http://www.w3.org/2001/04/xmlenc#aes256-cbc) | `encryption.xml` |
| Content Key, User fields (if encrypted) | AES 256 bits CBC | [http://www.w3.org/2001/04/xmlenc#aes256-cbc](http://www.w3.org/2001/04/xmlenc#aes256-cbc) | License Document | 
| User Passphrase | SHA-256 | [http://www.w3.org/2001/04/xmlenc#sha256](http://www.w3.org/2001/04/xmlenc#sha256) | License Document |
| Signature | RSA with SHA-256 | [http://www.w3.org/2001/04/xmldsig-more#rsa-sha256](http://www.w3.org/2001/04/xmldsig-more#rsa-sha256) | License Document |


# 7. Reading System Behavior

## 7.1 Detecting LCP Protected Publications

Reading Systems can detect that a Publication is protected using LCP by either of these means:

1. The presence of the License Document (`META-INF/license.lcpl`)

2. The presence of a `ds:KeyInfo/ds:RetrievalMethod` element in `META-INF/encryption.xml` that references an LCP Content Key (i.e., `Type` attribute value is "`http://readium.org/2014/01/lcp#EncryptedContentKey`" )

If `encryption.xml` references an LCP Content Key but the License Document is missing, the Reading System <b class="rfc">should</b> report this to the User.

## 7.2. License Document processing

### Overall

In processing a License Document, Reading Systems <b class="rfc">must</b> ignore all name/value pairs they do not understand.

Reading Systems <b class="rfc">must not</b> store unencrypted versions of the Content Key and/or encrypted user fields.

### Validating the License Document

Reading Systems <b class="rfc">must</b>:

1. Validate the syntax and completeness of the License Document.

2. Validate the signature as defined in [5.5. Validating the certificate and signature](#55-validating-the-certificate-and-signature).

### Acquiring the Publication

When License Documents are delivered independently of the Protected Publication, Reading Systems <b class="rfc">must</b>:

1. Download the Protected Publication at the given URL given in `links/publication/href`.

A Reading System that will make the Protected Publication file accessible to the User <b class="rfc">must</b> add the License Document to the downloaded Protected Publication at `META-INF/license.lcpl`.

Reading Systems <b class="rfc">should</b> verify the integrity of the downloaded Protected Publication, if a hash is provided.

A Reading System <b class="rfc">should</b> report any failure to acquire the Protected Publication to the user.

## 7.3. User Key processing

Reading Systems <b class="rfc">must</b>:

* Show the text hint and URL when prompting the User for their User Passphrase

Reading Systems <b class="rfc">should</b>:

* Store the User Key, but in a secured manner

* Try previously stored User Keys before prompting the User to enter their User Passphrase for a new Protected Publication

Reading Systems <b class="rfc">may</b>:

* Use an alternate technique to discover and exchange the User Key in the background.

* Associate specific User Keys with specific Providers and User IDs in order to optimize the discovery of the correct User Key to process a License Document.

Reading Systems <b class="rfc">must not</b>:

* Store User Passphrase, only the User Key.

## 7.4. Signature Processing

Reading Systems <b class="rfc">must</b>:

* Validate the Signature and Provider Certificate as described in [5.5. Validating the certificate and signature](#55-validating-the-certificate-and-signature).

* Update the Certification Revocation List on a regular basis.

Reading Systems <b class="rfc">should</b>:

* Be able to update their Root Certificate.

Reading Systems <b class="rfc">may</b>:

* Choose to open a Publication even if their own Root Certificate is deprecated.

Reading Systems <b class="rfc">must not</b>:

* Open a Publication with an invalid Signature or Provider Certificate.

* Block a User from opening a Publication if a network connection is unavailable or the Certificate Revocation List can't be reached.

## 7.5. Publication Processing

Reading Systems <b class="rfc">must</b>:

* Respect all rights limitations given in the License Document.

Reading Systems <b class="rfc">may</b>:

* Delete a Protected Publication if its License expired.

Reading Systems <b class="rfc">must not</b>:

* Store unencrypted Publication Resources.

## Normative References

* [EPUB] [EPUB 3.2](https://www.w3.org/publishing/epub3/).
* [JSON] [The application/json Media Type for JavaScript Object Notation (JSON)](https://www.ietf.org/rfc/rfc4627).
* [JSON Pointer] [JavaScript Object Notation (JSON) Pointer](https://tools.ietf.org/html/rfc6901).
* [OCF] [Open Container Format 3.2](https://www.w3.org/publishing/epub32/epub-ocf.html).
* [RFC2119] [Key words for use in RFCs to Indicate Requirement Levels](https://tools.ietf.org/html/rfc2119).
* [URI-Template] [URI Template](https://tools.ietf.org/html/rfc6570).
* [X509] [Internet X.509 Public Key Infrastructure Certificate and Certificate Revocation List (CRL) Profile](https://tools.ietf.org/html/rfc5280).
* [XML-ENC] [XML Encryption Syntax and Processing Version 1.1](https://www.w3.org/TR/xmlenc-core1/).
* [XML-SIG] [XML Signature Syntax and Processing (Second Edition)](https://www.w3.org/TR/xmldsig-core/).

## Informative References

* [LCP Link Relations Registry](https://readium.org/lcp-specs/registries/relations)
* [LCP Rights Registry](https://readium.org/lcp-specs/registries/rights)
* [LCP User Fields Registry](https://readium.org/lcp-specs/registries/user)
* LCP Encryption Profiles Registry

## Apendix A. JSON Schema

A JSON Schema for LCP 1.0 is available under version control at [https://github.com/readium/lcp-specs/tree/master/schema](https://github.com/readium/lcp-specs/tree/master/schema)

For the purpose of validating a license, use the following JSON Schema resource:
[https://readium.org/lcp-specs/schema/license.schema.json](https://readium.org/lcp-specs/schema/license.schema.json)
