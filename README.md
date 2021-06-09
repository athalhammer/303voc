# <https://w3id.org/303> - a rdf:Property

## Motivation
When a RDF document was successfully retrieved and the mime-type overlaps with the preferences specified in the HTTP "Accept" header typically the "crystal gazing" starts: There is a chain of 301, 302, and 303 redirects and, as the specification says, one of the URLs is the real-world resource and another is the URL of the document describing it. The real-world resource must be somewhere before the 303 redirect and the document URL somewhere afterwards. However, the following problems are quite common:
* No 303 redirects or multiple ones.
* Multiple 301/302 redirects before and/or after the 303 redirect.

This is even trickier if we get a URI from the middle of the redirect chain (e.g., due to a bad reference), such as <https://www.wikidata.org/entity/Q17027750>. The RDF comes back, it parses fine, we just don't exactly know the canonical URI of the resource we were looking for. For the Q17027750 resource this is a combination of taking the URI that is returned after the first 303 redirect and use it in combination with the `schema:about` property in the returned RDF document to derive that the canonical URI of the originally requested resource is <http://www.wikidata.org/entity/Q17027750> (http without s).

## Proposal
We explicitly insert a <https://w3id.org/303> triple as follows:

```
  <https://www.example.com/id/alice> <https://w3id.org/303> <https://www.example.com/doc/alice> .
```
It provides pointers to
  1. the canonical URI for the described real-world resource 
  2. the canonical URI of the RDF document describing it

in the subject position (1) and object postion (2) respectively. A Semantic Web agent that understands the meaning of this property can do the following:

>In any retrieved RDF document (that supports the property) a Semantic Web agent can immediately find pointers to the described real-world resource and the preferred/canonical URI of the document; even without knowing any of them before and also without analyzing the redirect chain.


## Best Practices
1. Only one such relation MUST be defined per RDF document.
2. The two URIs in the subject position and object position MUST be different from another.
3. The stated canonical URIs MUST be used to describe the real-world resource and the document respectively in the RDF document.
4. It can be used for both, hash and slash URIs. However, it MUST not be used in a contradictionary way to the [Cool URIs for the Semantic Web](https://www.w3.org/TR/cooluris/) spec.
5. The predicate is __not__ intended as a replacement for a 303 redirect. It is more of a metadata element that states canonical/preferred URIs for both, the URI of the real-world resource and the URI of the document describing it (nothing to see here, move along "httpRange-14 folks").
