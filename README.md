Solrclj
=======


A clojure client for [Apache Solr](http://lucene.apache.org/solr/).

[![Build Status](https://api.travis-ci.org/mlehman/solrclj.png)](http://api.travis-ci.org/mlehman/solrclj)

Examples
----------

### Creating the client

    (use 'solrclj)

    ;; Http Solr Example
    (def server (solr-server {:type :http
                              :host "localhost"}))

    ;; Http Multi-core Solr Example
    (def server (solr-server {:type :http
                              :core "books"
                              :host "localhost"}))

    ;; Embedded Solr Example
    (def server (solr-server {:type :embedded
                             :core "mycore"
                             :dir "/home-path"}))

### Querying

The first argument is the mandatory solr query parameter (q). Any additional solr parameters can be added using additional keyword arguments. Use a vector as the value to send multiple params. The query can use the [Solr Query Syntax](http://wiki.apache.org/solr/SolrQuerySyntax).

    ;; Simple Query
    (query server "la mancha")

    ;; Query with Solr Query Syntax
    (query server "author:Dickens AND year:[1843 TO 1848]")

    ;; Paging
    (query server "author:c*" :rows 100 :start 100)

    ;; Getting the documents from the response
    (first (get-in (query server "la mancha") [:response :docs]))
    ;=>  {:title "Don Quixote" :author "Miguel de Cervantes" :summary "..." }

    ;; Multiple Filter Queries
    (query server "la mancha" :fq ["language:es" "year:[* TO 1900]"])

### Other Optional Query Params

 - :q.op - the default query operation - "AND" or "OR"
 - :sort - sorts the documents - "year desc", "author asc, year asc"
 - :start - start index of documents
 - :rows - number of documents returned
 - :fq - filter query to restrict results - "language:en"
 - :fl - specify fields returned - "score, author, title"

### Faceting Query Results

    ;; Faceting
    (query server "*:*" :rows 0 :facet true :facet.field :author)

### Adding Documents

    ;; Adding a Document
    (add server {:title "Don Quixote" :author "Miguel de Cervantes" :summary "..." })

    ;; Adding Documents
    (apply add server seq-of-maps)

### Deleting Documents

    ;; Delete
    (delete-by-query server "*:*")

    ;; Commit
    (commit server)

### Core Administration

To enable dynamic core configuration, make sure the adminPath attribute is set in your solr.xml. If this attribute is absent, the CoreAdminHandler will not be available.

    (use 'solrclj.coreadmin)

    ;; Get the status
    (status server)

    ;; Create a new core
    (create server "my_core" "core_dir" core-config-file core-schema-file)

    ;; Reload a core
    (reload server "my_core")

Getting
-------

Modify your [Leiningen](http://github.com/technomancy/leiningen) dependencies to include Solrclj:

    :dependencies [[solrclj/solrclj "0.1.2"] ...]


References
----------

- [Apache Solr](http://lucene.apache.org/solr/)
- [Solr Common Parameters](http://wiki.apache.org/solr/CommonQueryParameters)
- [Solr Query Parameters](http://wiki.apache.org/solr/SearchHandler)
- [Solr Query Syntax](http://wiki.apache.org/solr/SolrQuerySyntax)
- [Lucene Query Syntax](http://lucene.apache.org/java/2_9_1/queryparsersyntax.html)
- [Solr Facet Parameters](http://wiki.apache.org/solr/SimpleFacetParameters)
- [Solrj](http://wiki.apache.org/solr/Solrj)
