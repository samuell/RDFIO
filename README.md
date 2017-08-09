RDFIO Extension for Semantic MediaWiki
======================================

[![Build Status](https://img.shields.io/circleci/project/github/rdfio/RDFIO.svg)](https://circleci.com/gh/rdfio/RDFIO)
[![Test Coverage](https://img.shields.io/codecov/c/github/rdfio/RDFIO/master.svg)](https://codecov.io/gh/rdfio/RDFIO)
[![Code Climate Rating](https://img.shields.io/codeclimate/github/rdfio/RDFIO.svg)](https://codeclimate.com/github/rdfio/RDFIO)
[![Code Climate Issues](https://img.shields.io/codeclimate/issues/github/rdfio/RDFIO.svg)](https://codeclimate.com/github/rdfio/RDFIO/issues)
[![Codacy Grade](https://api.codacy.com/project/badge/Grade/60604793d171476e92e997b71aca20a2)](https://www.codacy.com/app/samuel-lampa/RDFIO)
[![Latest Stable Version](https://img.shields.io/packagist/v/rdfio/rdfio.svg)](https://packagist.org/packages/rdfio/rdfio)
[![Packagist Download Count](https://img.shields.io/packagist/dt/rdfio/rdfio.svg)](https://packagist.org/packages/rdfio/rdfio)
[![Licence](https://img.shields.io/packagist/l/rdfio/rdfio.svg)]()

![Screenshot of the SPARQL Endpoint shipped with RDFIO](http://i.imgur.com/PMMIHZ4.png)

Introduction
------------

This extension extends the RDF import and export functionality in Semantic
MediaWiki by providing import of arbitrary RDF triples (not only OWL
ontologies, as before (see about [Ontology import](http://semantic-mediawiki.org/wiki/Help:Ontology_import),
and a SPARQL endpoint that allows write operations.

Technically, RDFIO implements the PHP/MySQL based triple store (and its
accompanying SPARQL Endpoint) provided by the [ARC2](http://arc.semsol.org/)
library. For updating wiki pages with new triples on import/sparql update, the
WOM extension is used.

The RDF import stores the original URI of all imported RDF entities (in
a special property), which can later be used by the SPARQL endpoint,
instead of SMW's internal URIs, which thus allows to expose the imported
RDF data "in its original formats", with its original URIs. This allows
to use SMW as a collaborative RDF editor, in workflows together with
other semantic tools, from which it is then possible to "export,
collaboratively edit, and import again", to/from SMW.

This extensions was initially developed as part of a
[Google Summer of Code 2010 project](http://www.mediawiki.org/wiki/User:SHL/GSoC2010),
and further extended as part of a [FOSS OPW 2014 project](https://www.mediawiki.org/wiki/Extension:RDFIO/Template_matching_for_RDFIO).

- Caution! *This extension is not yet ready for production use! Use it on your own risk!*

Installation
------------

**Please note:** RDFIO currently needs SMW 2.4.x. SMW 2.5.x won't work yet!

### Vagrant box

The absolutely simplest way, is to use the [RDFIO Vagrant
box](https://github.com/rdfio/rdfio-vagrantbox), which will automatically set
up MediaWiki, SemanticMediaWiki and RDFIO in a virtual machine in under 20
minutes.

### Install semi-manually using composer

1. Provided you have the PHP package manager
   [Composer](https://getcomposer.org/) installed (See [this page](https://getcomposer.org/doc/00-intro.md)
   for install instructions), you should now be able to install RDFIO via
   packagist.org, like so:

   ```bash
   cd <wiki_folder>
   composer require rdfio/rdfio --update-no-dev
   ```

2.  After installing RDFIO using composer, only one manual step is required,
   namely to go to the `Special:RDFIOAdmin` page on your wiki, and hit the "setup"
   button, to initialize the MySQL tables needed by the ARC2 library that RDFIO
   builds upon.
   
#### Optional but recommended steps

* Edit the MediaWiki:Sidebar page and add the following wiki snippet, as an extra menu (I use to place it before just the "* SEARCH" line), which will give you links to the main functionality with RDFIO from the main links in the left sidebar on the wiki:

   ```
   * Semantic Tools
   ** Special:RDFIOAdmin|RDFIO Admin
   ** Special:RDFImport|RDF Import
   ** Special:SPARQLEndpoint|SPARQL Endpoint
   ** Special:SPARQLImport|SPARQL Import
   ```

* Create the article "MediaWiki:Smw_uri_blacklist" and make sure it is empty (you might need to add some nonsense content like `{{{<!--empty-->}}}`).

#### Test that it works

* Access the **SPARQL endpoint** at http://[url-to-your-wiki]/Special:SPARQLEndpoint
* Access the **RDF Import page** at http://[url-to-your-wiki]/Special:RDFImport
* Access the **SPARQL Import page** at http://[url-to-your-wiki]/Special:SPARQLImport
* Optionally, if you want to really see that it works, try adding some semantic data to wiki pages, and then check the database (using phpMyAdmin e.g.) to see if you get some triples in the table named `arc2store_triple`.

### Additional configuration

These are some configuration options that you might want to adjust to your specific use case. That goes into your `LocalSettings.php` file. Find below a template with the default options, which you can start from, add to your `LocalSettings.php` file and modify to your liking:

```php
# ---------------------------------------------------------------
#  RDFIO Configuration
# ---------------------------------------------------------------
# An associative array with base uris as keys and corresponding 
# prefixes as the items. Example:
# array( 
#       "http://example.org/someOntology#" => "ont1",
#       "http://example.org/anotherOntology#" => "ont2"
#      );
# $rogBaseURIs = array();
# ---------------------------------------------------------------
# Query by /Output Equivalent URIs SPARQL Endpoint 
# (overrides settings in HTML Form)
# 
# $rogQueryByEquivURI = false;
# $rogOutputEquivURIs = false;
#
# $rogTitleProperties = array(
#  'http://semantic-mediawiki.org/swivt/1.0#page',
#  'http://www.w3.org/2000/01/rdf-schema#label',
#  'http://purl.org/dc/elements/1.1/title',
#  'http://www.w3.org/2004/02/skos/core#preferredLabel',
#  'http://xmlns.com/foaf/0.1/name',
#  'http://www.nmrshiftdb.org/onto#spectrumId'
# );
# ---------------------------------------------------------------
# Allow edit operations via SPARQL from remote services
#
# $rogAllowRemoteEdit = false;
# ---------------------------------------------------------------
```

Dependencies
------------

- [MediaWiki](mediawiki.org) (Tested with 1.27-1.29)
- [Semantic MediaWiki Extension](http://www.mediawiki.org/wiki/Extension:Semantic_MediaWiki) 2.4.x (2.5.x won't work yet!)
- [The ARC2 RDF library for PHP](https://github.com/semsol/arc2) Latest version on github should work

Known limitations
-----------------

- RDFIO does not yet support all the features of [SMW's vocabulary import](https://www.semantic-mediawiki.org/wiki/Help:Import_vocabulary).

Bugs, new feature request and contact information
-------------------------------------------------

Please reports bugs and feature requests in the
[issue tracker](https://github.com/rdfio/RDFIO/issues) here on Github.

Links
-----

- [RDFIO's page on MediaWiki.org](https://www.mediawiki.org/wiki/Extension:RDFIO)
- [RDFIO project page on pharmb.io (where it is partly developed)](https://pharmb.io/project/rdfio/)
