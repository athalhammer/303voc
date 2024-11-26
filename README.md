# <https://w3id.org/x303> - a rdf:Property

## Motivation
When a RDF document was successfully retrieved and the mime-type overlaps with the preferences specified in the HTTP "Accept" header typically the "crystal gazing" starts: There is a chain of 301, 302, and 303 redirects and, as the specification says, one of the URLs identifies a real-world resource and another is the URL of the document describing it. The real-world resource must be somewhere before the 303 redirect and the document URL somewhere afterwards. However, the following problems are quite common:
* No 303 redirects or multiple ones.
* Multiple 301/302 redirects before and/or after the 303 redirect.

This is even trickier if we get a URL from the middle of the redirect chain (e.g., due to a "bad" reference), such as <https://www.wikidata.org/entity/Q17027750>. The RDF comes back, it parses fine, we just can't find any triple with the resource we originally called. For the `Q17027750` Wikidata resource this results in an exercise where we take the URL returned after the first 303 redirect and use it in combination with the `schema:about` property in the returned RDF document to derive that the URL of the real-world instance is, in fact, <http://www.wikidata.org/entity/Q17027750> (http without s).

__Side note:__ It is actually not really wrong to refer to https://www.wikidata.org/entity/Q17027750 instead of http://www.wikidata.org/entity/Q17027750 as resolving the latter URL results in a 301 (Moved Permanently) redirect to the former. However, in the end we don't find any triples with the former URL in the returned RDF document.

## Proposal
We explicitly insert a <https://w3id.org/x303> triple as follows:

```
  <https://www.example.com/id/alice> <https://w3id.org/x303> <https://www.example.com/doc/alice> .
```
It provides pointers to
  1. the described real-world resource 
  2. the RDF document describing it

in the subject position (1) and object postion (2) respectively. Now the following is possible:

>In any retrieved RDF document (that supports the property) a Semantic Web agent can immediately find pointers to the described real-world resource and the document describing it; even without knowing any of them before and also without analyzing the redirect chain.

A client may still check the redirect chain, for example, in order to verify that the source has "authority" to define the meaning of the given URIs.

## Best Practices
1. Only one such relation MUST be defined per RDF document.
2. The two URLs in the subject position and object position MUST be different from another.
3. The stated URLs MUST be used to describe the real-world resource and the document respectively in the returned RDF document.
4. It can be used for both, hash and slash URLs. However, it MUST not be used in a contradictionary way to the [Cool URIs for the Semantic Web](https://www.w3.org/TR/cooluris/) spec. In particular: Resolving the URI in the object position MUST lead to the document where the "303 triple" has been found. Resolving the URI in the subject position MUST involve a 303 redirect to the URI in the object position (at some point) if it is not a hash URI.
5. In contrast to "[toucan publishing](http://blog.iandavis.com/2010/11/04/is-303-really-necessary/)", the predicate is __not__ intended as a replacement for a 303 redirect. It is more of a metadata element that states URLs for both, the URI of the real-world resource and the URL of the document describing it (nothing to see here, move along "httpRange-14 folks").


## Changelog
* [2024-11-26] Replaced <https://w3id.org/303> in favor of <https://w3id.org/x303> that is compatible to XML qualified names (that can not start with a number).
