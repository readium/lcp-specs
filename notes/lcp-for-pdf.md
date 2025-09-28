# Readium LCP for PDF 1.0

**Revision: 1**

*Copyright 2020, Readium Foundation. All Rights Reserved.*

Contributors: 
Laurent Le Meur (EDRLab),
Hadrien Gardeur (De Marque)

## Requirements

* LCP for PDF is based on the [PDF Profile](https://readium.org/webpub-manifest/profiles/pdf.html) of the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) and follows its requirements for packaging.
* Every LCP for PDF package <strong class="rfc">must</strong> use:
  * the `.lcpdf` extension.
  * the `application/pdf+lcp` media type.
* A `numberOfPages` property <strong class="rfc">must</strong> be present in the `metadata` section of the manifest. It is used by Reading Systems to evaluate user's progression in the publication. 
* PDF documents referenced in the `readingOrder` <strong class="rfc">must not</strong> be compressed in the package.
* A cover <strong class="rfc">should</strong> be referenced in the `resources` section of the manifest and, if present, <strong class="rfc">must not</strong> be encrypted.
* An embedded LCP License Document <strong class="rfc">must</strong> be located at `license.lcpl`.
* Every encrypted resource <strong class="rfc">must</strong> indicate in the `properties` of its Link Object an `encrypted` element where:
  * `scheme` <strong class="rfc">must</strong> be set to `http://readium.org/2014/01/lcp`.
  * `profile` and `algorithm` <strong class="rfc">must</strong> be set properly based on the LCP Encryption Profile of the License.

  
## Example

```json
{
  "@context": [
    "https://readium.org/webpub-manifest/context.jsonld", 
    "https://readium.org/webpub-manifest/contexts/epub/context.jsonld"
  ],
  "metadata": {
    "identifier": "https://readium.org/webpub-manifest/",
    "title": "Readium Web Publication Manifest",
    "author": "Readium Community",
    "editor": "Hadrien Gardeur",
    "description": "The Readium Web Publication Manifest is a JSON-based document meant to represent and distribute publications over HTTPS."
  },
  "readingOrder": [
    {
      "href": "rwpm.pdf",
      "type": "application/pdf",
      "properties": {
        "encrypted": {
          "scheme": "http://readium.org/2014/01/lcp",
          "profile": "http://readium.org/lcp/basic-profile",
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