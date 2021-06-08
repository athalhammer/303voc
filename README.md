# <https://w3id.org/303> - a rdf:Property

## Motivation

When you run `curl` on a linked data resource, there is currently no guarantee that the redirect chain actually contains a description of the requested real-world resource. Partially, this is because we may enter somewhere in the middle of a redirect chain. For example as follows:
```
      curl -s https://www.wikidata.org/entity/Q1 -ILH "Accept: text/turtle" | grep -P "HTTP|location"
        HTTP/2 303 
        location: https://www.wikidata.org/wiki/Special:EntityData/Q1
        HTTP/2 303 
        location: https://www.wikidata.org/wiki/Special:EntityData/Q1.ttl
        HTTP/2 200 
```
Note that the 303 redirects point to <https://www.wikidata.org/wiki/Special:EntityData/Q1> and <https://www.wikidata.org/wiki/Special:EntityData/Q1.ttl> respectively. We indeed find information on the first of the two documents but we can do not retrieve any link to the described real-world resource.
```
      curl -s http://www.wikidata.org/entity/Q1 -LH "accept: text/turtle" \
              | grep -P "https://www.wikidata.org/wiki/Special:EntityData|data:Q1" -A 8
        @prefix data: <https://www.wikidata.org/wiki/Special:EntityData/> .
        @prefix s: <http://www.wikidata.org/entity/statement/> .
        @prefix ref: <http://www.wikidata.org/reference/> .
        @prefix v: <http://www.wikidata.org/value/> .
        @prefix wdt: <http://www.wikidata.org/prop/direct/> .
        @prefix wdtn: <http://www.wikidata.org/prop/direct-normalized/> .
        @prefix p: <http://www.wikidata.org/prop/> .
        @prefix ps: <http://www.wikidata.org/prop/statement/> .
        @prefix psv: <http://www.wikidata.org/prop/statement/value/> .
        --
        data:Q1 a schema:Dataset ;
          schema:about wd:Q1 ;
          cc:license <http://creativecommons.org/publicdomain/zero/1.0/> ;
          schema:softwareVersion "1.0.0" ;
          schema:version "1435353896"^^xsd:integer ;
          schema:dateModified "2021-06-05T06:17:15Z"^^xsd:dateTime ;
          wikibase:statements "102"^^xsd:integer ;
          wikibase:sitelinks "203"^^xsd:integer ;
          wikibase:identifiers "49"^^xsd:integer .
```

The described real-world entity is, in fact, <http://www.wikidata.org/entity/Q1> (note: in contrast to the original request without httpS). It does not show up in the redirect chain nor do we have any semantic links from the resources that do. A Semantic Web agent can't retrieve further information from that point.

## Proposal
We explicitly insert a <https://w3id.org/303> triple as follows:

```
  <http://www.wikidata.org/entity/Q1> <https://w3id.org/303> <https://www.wikidata.org/wiki/Special:EntityData/Q1> .
```
It provides pointers to the
  1. canonical URI for the described real-world resource 
  2. the (preferred) canonical URI of the RDF document describing it

in the subject position (1) and object postion (2) respectively. A Semantic Web agent that understands the meaning of this property can do the following:

>In any retrieved RDF document (that supports the property) a Semantic Web agent immediately finds pointers to the described real-world resource(s) and the canonical URI of the document; even without knowing any of them before and also without analyzing the redirect chain.


## Best Practices
1. Only one such relation SHOULD be defined per RDF document. Exceptions may be ontologies (have not yet thought that fully through).
2. The URIs in the subject position and object position MUST be different from another.
3. The predicate is __not__ intended as a replacement for a 303 redirect. It is more of a metadata element that states canonical/preferred URIs for both, the URI of the real-world resource and the URI of the document describing it (nothing to see here, move along "httpRange-14 folks").
4. The stated canonical URIs MUST be used to describe the real-world resource and the document respectively in the RDF document.
