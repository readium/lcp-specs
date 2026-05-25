# Readium LCP for PDF 1.0

**Revision: 2**

*Copyright 2020, Readium Foundation. All Rights Reserved.*

Contributors: 
Laurent Le Meur (EDRLab),
Hadrien Gardeur (De Marque)

## Requirements

* LCP for PDF is based on the [PDF Profile](https://readium.org/webpub-manifest/profiles/pdf.html) of the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) and follows its requirements for packaging.
* Every package <strong class="rfc">must</strong> use:
  * the `.lcpdf` extension.
  * the `application/pdf+lcp` media type.
* An embedded LCP License Document <strong class="rfc">must</strong> be located at `license.lcpl`.
* A `numberOfPages` property <strong class="rfc">must</strong> be present in the `metadata` section of the manifest. It is used by Reading Systems to evaluate user's progression in the publication. 
* A cover <strong class="rfc">should</strong> be referenced in the `resources` section of the manifest and, if present, <strong class="rfc">must not</strong> be encrypted.
* The encrypted PDF document referenced in the `readingOrder` <strong class="rfc">must not</strong> be compressed in the package.
* A `type` property <strong class="rfc">must</strong> be set for the PDF document, its value <strong class="rfc">must</strong> by `application/pdf`. 
* A `size`property <strong class="rfc">should</strong> be set for the PDF docmuent. It is the size in bytes of the  PDF document before compression and encryption.
* An `encrypted` <strong class="rfc">must</strong> be set in `properties` for the PDF document, where:
  * `scheme` <strong class="rfc">must</strong> be set to `http://readium.org/2014/01/lcp`.
  * `algorithm` <strong class="rfc">must</strong> be set; it indicates the encryption algorithm applied to the document, currently "http://www.w3.org/2001/04/xmlenc#aes256-cbc".


  
## Example

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  "metadata": {
    "@type": "http://schema.org/Book",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/pdf",
    "title": "Readium Web Publication Manifest",
    "author": "Readium Community",
    "numberOfPages": 102

  },
  "readingOrder": [
    {
      "href": "publication.pdf",
      "type": "application/pdf",
      "size": 8523576,
      "properties": {
        "encrypted": {
          "scheme": "http://readium.org/2014/01/lcp",
          "algorithm": "http://www.w3.org/2001/04/xmlenc#aes256-cbc"
        }
      }
    }
  ],
  "resources": [
    {
      "href": "cover.jpg",
      "type": "image/jpeg",
      "rel": "cover" 
    }
  ]
}
```

# Demo

An example where the PDF is not (yet) encrypted is available at: [http://readium.org/lcp-specs/examples/lcpdf/rwpm.lcpdf](http://readium.org/lcp-specs/examples/lcpdf/rwpm.lcpdf)

# JSON Schema

A JSON Schema for the manifest of an LCPDF package is available under version control at: [https://github.com/readium/lcp-specs/blob/master/schema/lcpdf.schema.json](https://github.com/readium/lcp-specs/blob/master/schema/lcpdf.schema.json)

For validating a manifest, use the following reference: [https://readium.org/lcp-specs/schema/lcpdf.schema.json](https://readium.org/lcp-specs/schema/lcpdf.schema.json)

  
<style>
.rfc {
    color: #d55;
    font-variant: small-caps;
    font-style: normal;
    font-weight: normal;
}
</style>