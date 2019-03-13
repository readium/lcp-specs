# LCP for PDF

## Status of this Document

This document is an early draft.

## Requirements

* LCP for PDF is based on the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) and follows its requirements for packaging
* All LCP for PDF packages <strong class="rfc">must</strong> use:
  * the `.lcpdf` extension
  * the `application/pdf+lcp` media type
* The `readingOrder` specified in `manifest.json` <strong class="rfc">must</strong> strictly reference one or more PDF documents 
* PDF documents referenced in the `readingOrder` <strong class="rfc">must not</strong> be compressed in the package
* A cover <strong class="rfc">should</strong> be referenced in `resources` of `manifest.json` and <strong class="rfc">must not</strong> be encrypted
* An embedded LCP License Document <strong class="rfc">must</strong> be located at `license.lcpl`
* All encrypted resources <strong class="rfc">must</strong> indicate in the `properties` of their Link Object an `encrypted` element where:
  * `scheme` <strong class="rfc">must</strong> be set to `http://readium.org/2014/01/lcp`
  * `profile` and `algorithm` <strong class="rfc">must</strong> be set properly based on the LCP Encryption Profile of the License 

  
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

  
<style>
.rfc {
    color: #d55;
    font-variant: small-caps;
    font-style: normal;
    font-weight: normal;
}
</style>