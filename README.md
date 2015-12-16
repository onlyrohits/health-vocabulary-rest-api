HaVOC - Health Vocabulary REST API
==========================
<p>HaVoc is the most developer friendly API that gives you access to hundreds of source vocabularies in the UMLS and to retrieve synonyms for a given medical concept, its parent/children concepts and translations across different vocabularies, <a href="/faq" ><b>learn more</b></a> on what the API is used for.</p>
A swiss-army knife for all health/biomedical terminology/vocabulary related functions. Perform terminology queries on SNOMED-CT, LOINC, RXNORM or any of the 160 vocabularies in the [Unified Medical Language System](http://www.nlm.nih.gov/research/umls/). (Note: a separate download/license is required from the [NLM site](https://uts.nlm.nih.gov//license.html) to use this tool) 

Here is a [Demo Site](http://havoc.appliedinformaticsinc.com/?demo)

API operations
--------------
**NOTE: The required field are marked as “bold” in the API parameters**

**GET /concepts**

Search concepts by term and source terminology (SAB)

Parameters:

 - **term:** Any source medical term, e.g. diabetes 
 - sabs: A comma separated list of source vocabularies to restrict the concepts
 - partial: 1/0 (if partial=1 then term will be partial term matches
   will be returned. if partial =0 then all matches will be returned.
   default =0)

Returns:
A list of concept objects

Example: /concepts?term=diabetes

    [{
      cui: “CUI0001”,
      sab: [“MESH”,”SNOMEDCT”,”NCI”],
      terms: [“diabetes”, “diabetes type 1”...]
      pref_term: “diabetes”
    },
    ….
    ]

**GET /concepts_bulk**

Search concepts by terms in bulk and source terminology (SAB)

Parameters:

 - **terms:** A comma separate list of terms 
 - sabs: A comma separated list of source vocabularies to restrict the concepts 
 - partial: 1/0 (if partial=1 then term will be partial term matches will be returned. if partial =0 then all matches will be returned. default =0) delimiter:
   delimiter which separates the terms, default is comma(,)

Returns:
A list of concept objects

Example: /concepts?terms=diabetes:blood cancer&delimiter=:

    [{
      cui: “CUI0001”,
      sab: [“MESH”,”SNOMEDCT”,”NCI”],
      terms: [“diabetes”, “diabetes type 1”...]
      pref_term: “diabetes”
    },
    ….
    ]

**GET /concepts/:cui**

Get full details for a concept (specified by CUI)
Parameters:
**cui**: concept id

Returns
A Concept object

**GET /concepts/:cui/children**

Get all childrens for a given concept. By default it will get all childrens based on UMLS (REL=CHD) or the relationships can be restricted a given vocabulary

Parameters:

 - **cui:** concept id 
 - sab: The source vocabs to restrict the child definition. Currently supported: MeSH and SNOMEDCT 
 - explode: 1/0 - if
   set to 1 then get all children recursively. Currently supported
   vocabularies: MeSH

Returns:
A list of Concept objects


**GET /concepts/:cui/parents**

Get all parents for a given concept. By default it will get all parents based on UMLS (REL=PAR) or the relationships can be restricted a given vocabulary

Parameters:

 - **cui:** concept id 
 - sab: The source vocabs to restrict the child definition.  explode: 1/0 - if set to 1 then get all parents recursively. Currently supported vocabularies: MeSH

Returns:
A list of Concept objects


**GET /concepts_bulk/:cui,:cui,:cui/parents**

Get all parents for a given list of concepts. By default it will get all parents based on UMLS (REL=PAR,RB) or the relationships can be restricted a given vocabulary

Parameters:

 - **cui list:**: list of comma seperated cuis 
 - sab: The source vocabs to restrict the child definition.  
 - explode: 1/0 - if set to 1 then get all parents recursively. Currently supported vocabularies: MeSH

Returns:
A list of Concept objects for each provided CUI

Example: /concepts_bulk/C0011860,C0026764/parents?sab=MSH&explode=1

    {
    C0011860: [
    {
    cui: "C0014130",
    sabs: [
    "MSH",
    .....
    ],
    terms: [
    "Endocrine System Diseases",
    "diseases endocrine system",
    ..........
    ]
    },
    {
    cui: "C1135584",
    sabs: [
    "SRC",
    "MSH",
    .......
    ],
    terms: [
    "Medical Subject Headings",
    ..............
    ]
    }
    ]
    }


**GET /concepts/:cui/synonyms**

Get all synonym strings for a given Concept (:cui). Optionally, restrict the synonyms to a list of vocabularies.

Parameters:

 - **cui:** concept id 
 - sabs: A List of source vocabularies

Returns:
A list of strings, e.g. [“diabetes”, “diabetes type 2”,...]


**GET /rel/:sab/:code/:rel_type**

Get all relationships for a given concept. 

Parameters:

 - **sab:** The vocabulary abbreviation or short name, e.g. SNOMEDCT, LOINC. 
 - **code:** The value of code to be looked up, e.g. 111-ABC
   degree: Control the number of hops in the graph
 - **rel_type:** The
   relationship to be looked up, e.g. is_diagnosed_by,
   causative_agent_of.

Returns:
A list of Concept objects


**

**Code API**

**
The following set of APIs work with vocabulary codes

**GET /codes**

Search all codes 

Parameters:

 - **code:** Search for a given code  
 - sabs: Restrict the search a given set of vocabularies

Returns:
A list of Code objects

**GET /codes/:code/sabs/:sab**

Get details about a code.

Parameters:

 - **code:** Get details about a code  
 - sab: In a given source vocabulary

Returns:
A Code object

**GET /codes/:code1/sabs/:sab1/mapping/:sab2**

Get semantically equivalent mappings for code1 in sab1 in sab2

Parameters:

 - **code1:** Get details about a code 
 - **sab1:** In a given source vocabulary 
 - **sab2:** The target vocabulary to find a code in sab2

Returns:
A list of Code objects (one or more mappings)


* * *
Requirements
------------

- Python 2.7
- Django 1.4 or above
- MySQL or any other relational database supported by Django
- [UMLS Metathesaurus](http://www.nlm.nih.gov/research/umls/) Files in .SQL format.

* * *
Install/Set up
--------------

1. Check out the code into your local. 
2. Edit *settings.py( and update DATABASE settings to point to your local database
3. Create corresponding database tables, *python manage.py syncdb*
4. Load the MRCONSO.sql, MRREL.sql, MRSTY.sql (pronounced as Mr. Conso, Mr. Rel, Mr. Sty) into database from command line as follows: 

  mysql -u&lt;user&gt; -p&lt;password&gt; &lt;db_name&gt; &lt; MRCONSO.sql
 
  alter table MRCONSO add column id int auto_increment primary key;

  mysql -u&lt;user&gt; -p&lt;password&gt; &lt;db_name&gt; &lt; MRREL.sql
  
  alter table MRREL add column id int auto_increment primary key;
  
  mysql -u&lt;user&gt; -p&lt;password&gt; &lt;db_name&gt; &lt; MRSTY.sql
  
  alter table MRSTY add column id int auto_increment primary key;
  
5. Run the app, python manage.py runserver
6. For production environment, it is advised to host the app inside a [WSGI](https://docs.djangoproject.com/en/dev/howto/deployment/wsgi/) container.


TODOs
-----

- Currently, it does not have authentication or rate throttling mechanism. The API is currently designed for internal apps consumption. 
- Develop a commands.py to load the UMLS files into the database instead of load into local (coming soon in next major release)
- A memcached version to store the tables in memory instead of a relational database (coming soon in next major release)
- Create a fully expanded ISA hierarcy to support class based queries (coming soon in next major release)

Frequently Asked Questions
--------------------------
* Why did you create this API?

  It was one of late spring days in NYC, we were coding our patient portal app for the New York eHealth Collaborative Challenge. They provided us with oh-so-[SHIN-NY](http://digitalhealthaccelerator.com/shiny-api/) API, but it sent us raw CCDA files and we wanted to display patient friendly lab result names. So we coded up this API that was called by our Javascript client on the patient portal to display nicer lab names.

* How is it different from the [UMLS Terminology Services](https://uts.nlm.nih.gov/home.html)?

   Our goal with Health Vocabulary REST API was to create a developer friendly API that abstracts-away a lot of UMLS specific notions to support rapid app development. The UTS provides a full-fledged web services based interface. Additionally, this API server can be hosted and managed locally.

* Can I get support to get this API set up for us?

  Absolutely! Just shoot us an email at info@appliedinformaticsinc.com / chintan@trialx.com / nadeem@trialx.com 


* * *

LICENSE
-------
Copyright (c) 2015 by Applied Informatics Inc. Licensed under the [Apache 2.0](http://www.apache.org/licenses/LICENSE-2.0.html) license.
