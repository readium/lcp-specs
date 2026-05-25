# Readium LCP for Audiobooks 1.0

**Revision: 2**

*Copyright 2020, Readium Foundation. All Rights Reserved.*

Contributors: 
Laurent Le Meur (EDRLab),
Hadrien Gardeur (De Marque)

## Requirements

* LCP for Audiobooks is based on the [Audiobook Profile](https://readium.org/webpub-manifest/profiles/audiobook) of the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) and follows its requirements for packaging.
* Every package <strong class="rfc">must</strong> use:
  * the `.lcpau` extension
  * the `application/audiobook+lcp` media type
* An embedded LCP License Document <strong class="rfc">must</strong> be located at `license.lcpl`
* A cover <strong class="rfc">should</strong> be referenced in `resources` of `manifest.json` and, if present, <strong class="rfc">must not</strong> be encrypted.
* Encrypted audio resources referenced in the `readingOrder` <strong class="rfc">must not</strong> be compressed in the package.
* A `type` property <strong class="rfc">must</strong> be set for each audio resource. 
* A `bitrate`, `duration`, `size`property <strong class="rfc">should</strong> be set for each audio resource. The latter is the size in bytes of the audio resource before compression and encryption.
* An `encrypted` <strong class="rfc">must</strong> be set in `properties` for each audio resource, where:
  * `scheme` <strong class="rfc">must</strong> be set to `http://readium.org/2014/01/lcp`
  * `algorithm` <strong class="rfc">must</strong> be set; it indicates the encryption algorithm applied to the document, currently "http://www.w3.org/2001/04/xmlenc#aes256-cbc".

  
## Example

```json
{
  "@context": "http://readium.org/webpub-manifest/context.jsonld",
  
  "metadata": {
    "@type": "http://schema.org/Audiobook",
    "identifier": "urn:isbn:9780000000001",
    "title": "Moby-Dick",
    "author": "Herman Melville",
    "narrator": ["Joe Speaker", "Lucy Narrator"],
    "language": "en",
    "publisher": "Whale Publishing Ltd.",
    "published": "2016-02-01",
    "modified": "2016-02-18T10:32:18Z",
    "duration": 4320
  },

  "links": [
    {
      "rel": "self", 
      "href": "manifest.json", 
      "type": "application/audiobook+json"
    }
  ],

  "readingOrder": [
    {
      "href": "part1.mp3", 
      "type": "audio/mpeg", 
      "bitrate": 128, 
      "duration": 1980, 
      "title": "Part 1",
      "properties": {
        "encrypted": {
          "scheme": "http://readium.org/2014/01/lcp",
          "algorithm": "http://www.w3.org/2001/04/xmlenc#aes256-cbc"
        }
      }
    }, 
    {
      "href": "part2.mp3", 
      "type": "audio/mpeg", 
      "bitrate": 128, 
      "duration": 1200, 
      "title": "Part 2",
      "properties": {
        "encrypted": {
          "scheme": "http://readium.org/2014/01/lcp",
          "algorithm": "http://www.w3.org/2001/04/xmlenc#aes256-cbc"
        }
      }
    }, 
    {
      "href": "part3.mp3", 
      "type": "audio/mpeg", 
      "bitrate": 128, 
      "duration": 1140, 
      "title": "Part 3",
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
      "rel": "cover", 
      "href": "cover.jpeg", 
      "type": "image/jpeg", 
      "height": 300, 
      "width": 300
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