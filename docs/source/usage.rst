=====
Usage
=====


We will now show you the basic structure of `CDA python` through the use of the most commands:

- ``columns()``: show all available columns in the table,
- ``unique_terms()``: for a given column show all unique terms,
- ``Q()``: Executes this query on the public CDA server,
- ``query()`` : allows you to write long form Q statments with out chaining, and
- ``Q.sql()``: allows you to enter SQL style queries.

**To begin we will first load all of the library and it's methods:**

>>> from cdapython import Q, columns, unique_terms, query

  
columns()
-----
``columns(version = 'all_v1', host = None, limit = 100, table = 'integration')``

displays all of the fields that can be queried using the ``Q`` or ``query`` (e.g. ethnicity, tumor stage, disease type, etc.)

**Parameters:**
   - version : str [Optional]
       - version allows you to select different version of SQL (BigQuery) tables to runs querys on; default = 'all_v1'
   - host : str [Optional]
       - host allows you to change the server in which you queries run; default = None (Board Institute)
   - limit : int [Optional]
       - limit allows you to set the number of values that ``columns`` returns; default = 100
   - table : str [Optional]
        table allows you to select with BigQuery table is being searched; default = 'integration'
**Returns:**
    list
**Example:**

>>> columns() # List column names eg:
['id',
 'identifier',
 'identifier.system',
 'identifier.value',
 'species',
 'sex',
 'race',
 'ethnicity',
 'days_to_birth',
 'subject_associated_project',
 'vital_status',
 'age_at_death',
 'cause_of_death',
 'File',
 'File.id',
 'File.identifier',
 'File.identifier.system',
 'File.identifier.value',
 'File.label',
 'File.data_category',
 'File.data_type',
 'File.file_format',
 'File.associated_project',
 'File.drs_uri',
 'File.byte_size',
 'File.checksum',
 'File.data_modality',
 'File.imaging_modality',
 'File.dbgap_accession_number',
 'ResearchSubject',
 'ResearchSubject.id',
 'ResearchSubject.identifier',
 'ResearchSubject.identifier.system',
 'ResearchSubject.identifier.value',
 'ResearchSubject.member_of_research_project',
 'ResearchSubject.primary_diagnosis_condition',
 'ResearchSubject.primary_diagnosis_site',
 'ResearchSubject.Diagnosis',
 'ResearchSubject.Diagnosis.id',
 'ResearchSubject.Diagnosis.identifier',
 'ResearchSubject.Diagnosis.identifier.system',
 'ResearchSubject.Diagnosis.identifier.value',
 'ResearchSubject.Diagnosis.primary_diagnosis',
 'ResearchSubject.Diagnosis.age_at_diagnosis',
 'ResearchSubject.Diagnosis.morphology',
 'ResearchSubject.Diagnosis.stage',
 'ResearchSubject.Diagnosis.grade',
 'ResearchSubject.Diagnosis.method_of_diagnosis',
 'ResearchSubject.Diagnosis.Treatment',
 'ResearchSubject.Diagnosis.Treatment.id',
 'ResearchSubject.Diagnosis.Treatment.identifier',
 'ResearchSubject.Diagnosis.Treatment.identifier.system',
 'ResearchSubject.Diagnosis.Treatment.identifier.value',
 'ResearchSubject.Diagnosis.Treatment.treatment_type',
 'ResearchSubject.Diagnosis.Treatment.treatment_outcome',
 'ResearchSubject.Diagnosis.Treatment.days_to_treatment_start',
 'ResearchSubject.Diagnosis.Treatment.days_to_treatment_end',
 'ResearchSubject.Diagnosis.Treatment.therapeutic_agent',
 'ResearchSubject.Diagnosis.Treatment.treatment_anatomic_site',
 'ResearchSubject.Diagnosis.Treatment.treatment_effect',
 'ResearchSubject.Diagnosis.Treatment.treatment_end_reason',
 'ResearchSubject.Diagnosis.Treatment.number_of_cycles',
 'ResearchSubject.File',
 'ResearchSubject.File.id',
 'ResearchSubject.File.identifier',
 'ResearchSubject.File.identifier.system',
 'ResearchSubject.File.identifier.value',
 'ResearchSubject.File.label',
 'ResearchSubject.File.data_category',
 'ResearchSubject.File.data_type',
 'ResearchSubject.File.file_format',
 'ResearchSubject.File.associated_project',
 'ResearchSubject.File.drs_uri',
 'ResearchSubject.File.byte_size',
 'ResearchSubject.File.checksum',
 'ResearchSubject.File.data_modality',
 'ResearchSubject.File.imaging_modality',
 'ResearchSubject.File.dbgap_accession_number',
 'ResearchSubject.Specimen',
 'ResearchSubject.Specimen.id',
 'ResearchSubject.Specimen.identifier',
 'ResearchSubject.Specimen.identifier.system',
 'ResearchSubject.Specimen.identifier.value',
 'ResearchSubject.Specimen.associated_project',
 'ResearchSubject.Specimen.age_at_collection',
 'ResearchSubject.Specimen.primary_disease_type',
 'ResearchSubject.Specimen.anatomical_site',
 'ResearchSubject.Specimen.source_material_type',
 'ResearchSubject.Specimen.specimen_type',
 'ResearchSubject.Specimen.derived_from_specimen',
 'ResearchSubject.Specimen.derived_from_subject',
 'ResearchSubject.Specimen.File',
 'ResearchSubject.Specimen.File.id',
 'ResearchSubject.Specimen.File.identifier',
 'ResearchSubject.Specimen.File.identifier.system',
 'ResearchSubject.Specimen.File.identifier.value',
 'ResearchSubject.Specimen.File.label',
 'ResearchSubject.Specimen.File.data_category',
 'ResearchSubject.Specimen.File.data_type',
 'ResearchSubject.Specimen.File.file_format']
 

All of the above fields are what describes the highest entity in the data structure hierarchy – ``Subject`` entity. The first thirteen fields represent ``Subject`` demographic information, while the ``ResearchSubject`` entity contains details that we are used to seeing within the nodes' ``Case`` record.

One of the contributions of the CDA is aggregated ``ResearchSubject`` information. This means that all ``ResearchSubject`` records coming from the same subject are now gathered under the Subject entity. As we know, certain specimens are studied in multiple projects (being part of a single data node or multiple nodes) as different ``ResearchSubject`` entries. Those ``ResearchSubject`` entries are collected as a list under the ``ResearchSubject`` entity. One example of this is the patient record with ``id = TCGA-13-1409`` which contains two ``ResearchSubject`` entries, one from GDC and the other from PDC, and three ``Subject`` entries, and additional entry for IDC.

.. note::

  Note that the ``ResearchSubject`` entity is a list of records, as many other entities above are. **There are certain considerations that should be made when  creating the queries by using the fields that come from lists, but more about that will follow in examples below**.

The names in the list may look familiar to you, but they may have been renamed or restructured in the CDA. For more information about the field name mappings you can look into :doc:`ETL` . A more direct way to explore and understand the fields is to use the ``unique_terms()`` function:
 
 
unique_terms()
-------
``unique_terms(col_name: str, system: str = '', limit: int = 100, host: Optional[str] = None, table: Optional[str] = None)``

displays all non-numeric values that can be searched in a query for a given column.

**Parameters:**
    - col_name : str
        - col_name is the value from the ``columns()`` that you would like a list of searchable terms from (e.g. 'ResearchSubject.primary_disease_site')
    - system : str [Optional]
        - system allows you to determine which data common you would like to search (GDC, PDC, or IDC; see :ref:`limit`)
    - limit : int [Optional]
        - limit allows you to set the number of values that ``columns`` returns; default = 100   
    - host : str [Optional]
        - host allows you to change the server in which you queries run; default = None (Board Institute)
    - table : str [Optional] 
        - table allows you to select with Big Query table is being searched; default = 'integration'
**Returns:**
    list
**Example:**



For each searchable field there are set values that can be searched (excluding numeric), to determine these values the ``unique_terms()`` command is used. For example if we were interested in searchable disease types at the ResearchSubject level were would type the following:

>>> unique_terms("ResearchSubject.primary_diagnosis_condition")
[None,
 'Acinar Cell Neoplasms',
 'Adenomas and Adenocarcinomas',
 'Adnexal and Skin Appendage Neoplasms',
 'Basal Cell Neoplasms',
 'Blood Vessel Tumors',
 'Breast Invasive Carcinoma',
 'Chromophobe Renal Cell Carcinoma',
 'Chronic Myeloproliferative Disorders',
 'Clear Cell Renal Cell Carcinoma',
 'Colon Adenocarcinoma',
...

.. note::
  The results of ``unique_terms()`` may not be the same a different level (Subject vs ResearchSubject vs Specimen), so ``unique_terms()`` most be searched at the same level you plan to run your query.

Additionally, you can specify a particular data node by using the ``system`` argument. For more information on data nodes/data commons see :ref:`ETL`.

>>> unique_terms("ResearchSubject.Specimen.source_material_type", system="PDC")
['Cell Lines',
 'Normal',
 'Normal Adjacent Tissue',
 'Not Reported',
 'Primary Tumor',
 'Solid Tissue Normal',
 'Tumor',
 'Xenograft Tissue']

.. warning::
 Some columns are array value or have complex values, and do not have ``unique_terms``. Arrays columns contain multiple values; an example of this would be ``File.identifier`` which as  comprised of ``system`` (which data common the information is from) and ``value`` (the id for a given file). Below is the list of column values that do have ``unique_terms``.

  
 - 'File',
 - 'File.identifier',
 - 'identifier',
 - 'ResearchSubject',
 - 'ResearchSubject.Diagnosis',
 - 'ResearchSubject.Diagnosis.Treatment',
 - 'ResearchSubject.Specimen',
 - 'ResearchSubject.Specimen.File',
 - 'ResearchSubject.Specimen.File.associated_project',
 - 'ResearchSubject.Specimen.File.identifier',
 - 'ResearchSubject.Specimen.identifier',
 - 'ResearchSubject.identifier',
 - 'subject_associated_project',
 - 'ResearchSubject.Diagnosis.identifier',
 - 'ResearchSubject.Diagnosis.Treatment.identifier',
 - 'ResearchSubject.File',
 - 'ResearchSubject.File.identifier'

Q()
----
``Q(query)``

Q lang is Language used to send query to the cda service

**Parameters:**
    - query : str
        - a query string containing a value from ``columns()`` with an comparison operator (=, !=, <, >) and a numeric/boolean/unique value form ``unique_terms``. 
**Returns:**
    cda python Q data type
    
``Q().run``

run(offset = 0, limit = 100, version = 'all_v2_1', host = None, dry_run = False, table = 'gdc-bq-sample.integration', async_call = False)

**Parameters:**
  - async_call : bool
    - async_call allows for 
  - table : str
    - table allows you to select which BigQuery table is being searched; default = ‘gdc-bq-sample.integration’
  - version : str
    - version allows you to select which version of the BigQuery table is being searched; default = ‘all_v2_1’
  - offset : int [optional] 
    - [description]. Defaults to 0.
  - limit : int, optional):
    - limit allows you to set the number of values that returns per page; default = 100
  - host : URL, [optional]
    - host allows you to change the server in which you queries run; default = None (Board Institute)
  - dry_run : bool, [optional] 
    - [description]. Defaults to False.

**Returns:**
    cda python Q data type
    
Q Comparison operators
+++++++

The following comparsion operators can be used with the `Q` or `query` command: 

+----------+---------------------------------------------------+---------------+
| operator |Description                                        |Q.sql required?|
+==========+===================================================+===============+
| =        | condition equals                                  |     no        |
+----------+---------------------------------------------------+---------------+
| !=       | condition is not equal                            |     no        |
+----------+---------------------------------------------------+---------------+
| <        | condition is less than                            |     no        |
+----------+---------------------------------------------------+---------------+
| >        | condition is greater than                         |     no        |
+----------+---------------------------------------------------+---------------+
| <=       | condition is less than or equal to                |     no        |
+----------+---------------------------------------------------+---------------+
| >=       | condition is less than or equal to                |     no        |
+----------+---------------------------------------------------+---------------+
| like     | similar to = but always wildcards ('%', '_', etc) |    yes        |
+----------+---------------------------------------------------+---------------+
| in       | compares to a set                                 |    yes        |
+----------+---------------------------------------------------+---------------+

additionally, more complex SQL can be used with the `Q.sql()`_ command. 

**Example:**

.. note::

  Any given part of a query is expressed as a string of three parts separated by spaces. **Therefore, there must be a space on both sides of the comparsion operator**. The first part of the query is interpreted as a **column name**, the second as a **comparator operator** and the third part as a **value**. If the value is a string, it needs to be put in double quotes.

Now, let's dive into the querying!

We can start by getting the record for ``id = TCGA-13-1409`` that we mentioned earlier:

>>> q = Q('id = "TCGA-13-1409"') # note the double quotes for the string value
>>> r = q.run()
>>> print(r)
Getting results from database
Total execution time: 1304 ms
QueryID: 243b307b-776b-4427-a8b3-eacb9a87b8d6
Query: SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1 WHERE (all_v2_1.id = 'TCGA-13-1409')
Offset: 0
Count: 1
Total Row Count: 1
More pages: False

We've discussed ``Q`` but not the ``.run()`` method; ``.run()`` must be called to actually process your query. After calling ``print()`` on the query result variable we see that we've got a single Subject record as a result, which is what we expect.

Let's take a look at the results:


>>> r[0]
{'id': 'TCGA-13-1409',
 'identifier': [{'system': 'GDC', 'value': 'TCGA-13-1409'},
  {'system': 'PDC', 'value': 'TCGA-13-1409'},
  {'system': 'IDC', 'value': 'TCGA-13-1409'}],
 'species': 'Homo sapiens',
 'sex': 'female',
 'race': 'white',
 'ethnicity': 'not hispanic or latino',
 'days_to_birth': '-26836',
 'subject_associated_project': ['TCGA-OV',
  'CPTAC-TCGA',
  'CPTAC-TCGA',
  'tcga_ov'],
 'vital_status': 'Dead',
 'age_at_death': '1742',
 'cause_of_death': None,
 'File': [{'id': '6850305a-e067-49fa-b617-0a4f32928352',
   'identifier': [{'system': 'GDC',
     'value': '6850305a-e067-49fa-b617-0a4f32928352'}],
   'label': '6850305a-e067-49fa-b617-0a4f32928352.vep.vcf.gz',
   'data_category': 'Simple Nucleotide Variation',
   'data_type': 'Annotated Somatic Mutation',
   'file_format': 'VCF',
   'associated_project': 'TCGA-OV',
   'drs_uri': 'drs://dg.4DFC:6850305a-e067-49fa-b617-0a4f32928352',
   'byte_size': '142504',
   'checksum': '0905d8fe02dd007065629983be81dd72',
   'data_modality': 'Genomic',
   'imaging_modality': None,
   'dbgap_accession_number': None},
  {'id': '14a0766c-6ca4-47bb-ac70-62133c30c1c5',
   'identifier': [{'system': 'GDC',
     'value': '14a0766c-6ca4-47bb-ac70-62133c30c1c5'}],
   'label': 'OV.focal_score_by_genes.txt',
   'data_category': 'Copy Number Variation',
   'data_type': 'Gene Level Copy Number Scores',
   'file_format': 'TXT',
   'associated_project': 'TCGA-OV',
   'drs_uri': 'drs://dg.4DFC:14a0766c-6ca4-47bb-ac70-62133c30c1c5',
   'byte_size': '26280573',
   'checksum': '22e40a89cdeebbc162896f1cdfe7e55e',
   'data_modality': 'Genomic',
   'imaging_modality': None,
   'dbgap_accession_number': None},
  {'id': '2e6f24c1-f5a3-4da4-83bf-457436d4927e',
   'identifier': [{'system': 'GDC',
     'value': '2e6f24c1-f5a3-4da4-83bf-457436d4927e'}],
   'label': '2e6f24c1-f5a3-4da4-83bf-457436d4927e.vcf',
   'data_category': 'Simple Nucleotide Variation',
   'data_type': 'Raw Simple Somatic Mutation',
   'file_format': 'VCF',
   'associated_project': 'TCGA-OV',
   'drs_uri': 'drs://dg.4DFC:2e6f24c1-f5a3-4da4-83bf-457436d4927e',
   'byte_size': '2679669',
   'checksum': '4ec46657a26fd3bcc27ca8fa856a591a',
   'data_modality': 'Genomic',
   'imaging_modality': None,
   'dbgap_accession_number': None},
   ...
   'ResearchSubject': [{'id': '18e0e996-8f23-4f53-94a5-dde38b550863',
   'identifier': [{'system': 'GDC',
     'value': '18e0e996-8f23-4f53-94a5-dde38b550863'}],
   'member_of_research_project': 'TCGA-OV',
   'primary_diagnosis_condition': 'Cystic, Mucinous and Serous Neoplasms',
   'primary_diagnosis_site': 'Ovary',
   'Diagnosis': [{'id': '6b0f33e6-884d-5a93-8335-9f55569790a7',
     'identifier': [{'system': 'GDC',
       'value': '6b0f33e6-884d-5a93-8335-9f55569790a7'}],
     'primary_diagnosis': 'Serous cystadenocarcinoma, NOS',
     'age_at_diagnosis': '26836',
     'morphology': '8441/3',
     'stage': None,
     'grade': 'not reported',
     'method_of_diagnosis': None,
     'Treatment': [{'id': '1140ff80-4d83-58f4-b151-0737143a0984',
       'identifier': [{'system': 'GDC',
         'value': '1140ff80-4d83-58f4-b151-0737143a0984'}],
       'treatment_type': 'Pharmaceutical Therapy, NOS',
       'treatment_outcome': None,
       'days_to_treatment_start': None,
       'days_to_treatment_end': None,
       'therapeutic_agent': None,
       'treatment_anatomic_site': None,
       'treatment_effect': None,
       'treatment_end_reason': None,
       'number_of_cycles': None},
      {'id': 'c9c78335-6d3f-52a5-92a9-c41ccbd8d4d8',
       'identifier': [{'system': 'GDC',
         'value': 'c9c78335-6d3f-52a5-92a9-c41ccbd8d4d8'}],
       'treatment_type': 'Radiation Therapy, NOS',
       'treatment_outcome': None,
       'days_to_treatment_start': None,
       'days_to_treatment_end': None,
       'therapeutic_agent': None,
       'treatment_anatomic_site': None,
       'treatment_effect': None,
       'treatment_end_reason': None,
       'number_of_cycles': None}]}],
   'File': [{'id': '6850305a-e067-49fa-b617-0a4f32928352',
     'identifier': [{'system': 'GDC',
       'value': '6850305a-e067-49fa-b617-0a4f32928352'}],
     'label': '6850305a-e067-49fa-b617-0a4f32928352.vep.vcf.gz',
     'data_category': 'Simple Nucleotide Variation',
     'data_type': 'Annotated Somatic Mutation',
     'file_format': 'VCF',
     'associated_project': 'TCGA-OV',
     'drs_uri': 'drs://dg.4DFC:6850305a-e067-49fa-b617-0a4f32928352',
     'byte_size': '142504',
     'checksum': '0905d8fe02dd007065629983be81dd72',
     'data_modality': 'Genomic',
     'imaging_modality': None,
     'dbgap_accession_number': None},
     ...
     'Specimen': [{'id': '930c3552-f960-4a57-aa35-b504807a9676',
     'identifier': [{'system': 'GDC',
       'value': '930c3552-f960-4a57-aa35-b504807a9676'}],
     'associated_project': 'TCGA-OV',
     'age_at_collection': '-26836',
     'primary_disease_type': 'Cystic, Mucinous and Serous Neoplasms',
     'anatomical_site': None,
     'source_material_type': 'Primary Tumor',
     'specimen_type': 'sample',
     'derived_from_specimen': 'initial specimen',
     'derived_from_subject': 'TCGA-13-1409',
     'File': [{'id': '04da990e-67a3-4ead-ab08-448c7118624c',
       'identifier': [{'system': 'GDC',
         'value': '04da990e-67a3-4ead-ab08-448c7118624c'}],
       'label': 'TCGA.OV.varscan.04da990e-67a3-4ead-ab08-448c7118624c.DR-10.0.protected.maf.gz',
       'data_category': 'Simple Nucleotide Variation',
       'data_type': 'Aggregated Somatic Mutation',
       'file_format': 'MAF',
       'associated_project': 'TCGA-OV',
       'drs_uri': 'drs://dg.4DFC:04da990e-67a3-4ead-ab08-448c7118624c',
       'byte_size': '216647924',
       'checksum': '431606691f638bb07d9028e6605539c7',
       'data_modality': 'Genomic',
       'imaging_modality': None,
       'dbgap_accession_number': None},
       ...
   
The record is pretty large, so we'll print out identifier values for each ``ResearchSubject`` to confirm that we have one ResearchSubject that comes from GDC, and one that comes from PDC:

>>> for research_subject in r[0]['ResearchSubject']:
>>>     print(research_subject['identifier'])
[{'system': 'GDC', 'value': '18e0e996-8f23-4f53-94a5-dde38b550863'}]
[{'system': 'PDC', 'value': '3a36a497-63d7-11e8-bcf1-0a2705229b82'}]

The values represent ResearchSubject IDs and are equivalent to case_id values in data commons.

.. warning::
  In some instances the results will return multiple pages, if this is the case you must include ``next_page()`` in you loop. An example of looping with ``next_page()`` can be found here.

Now that we can create a query with ``Q()`` function, let's see how we can combine multiple conditions.

And, Or and From operators
++++
There are three operators available:
 * ``And()``
 * ``Or()``
 * ``From()``

The following examples show how those operators work in practice.


Example Query 1: And
+++++++
**Find data for subjects who were diagnosed after the age of 50 and who were investigated as part of the TCGA-OV project.**

.. code-block:: python

 
 >>> q1 = Q('ResearchSubject.Diagnosis.age_at_diagnosis > 50*365')
 >>> q2 = Q('ResearchSubject.member_of_research_project = "TCGA-OV"')
 
 >>> q = q1.And(q2)
 >>> r = q.run()
 
 >>> print(r)
 
 Getting results from database
 
 Total execution time: 10550 ms
 
 QueryID: d43dd6bc-cab5-43c0-a683-ff32c5a6f621
 Query: SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.Diagnosis) AS _Diagnosis WHERE ((_Diagnosis.age_at_diagnosis > 50*365) AND (_ResearchSubject.member_of_research_project = 'TCGA-OV'))
 Offset: 0
 Count: 100
 Total Row Count: 461
 More pages: False


Example Query 2: And continued
+++++++
**Find data for donors with melanoma (Nevi and Melanomas) diagnosis and who were diagnosed before the age of 30.**

.. code-block:: python

 >>> q1 = Q('ResearchSubject.Specimen.primary_disease_type = "Nevi and Melanomas"')
 >>> q2 = Q('ResearchSubject.Diagnosis.age_at_diagnosis < 30*365')
 
 >>> q = q1.And(q2)
 >>> r = q.run()
 
 >>> print(r)
 
 Getting results from database
 
 Total execution time: 11287 ms
 
 QueryID: 02c118d4-08ac-442f-bc79-71b794bab6bc
 Query: SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.Specimen) AS _Specimen, UNNEST(_ResearchSubject.Diagnosis) AS _Diagnosis WHERE ((_Specimen.primary_disease_type = 'Nevi and Melanomas') AND (_Diagnosis.age_at_diagnosis < 30*365))
 Offset: 0
 Count: 100
 Total Row Count: 663
 More pages: False


In addition, we can check how many records come from particular systems by adding one more condition to the query:

.. code-block:: python

 >>> q1 = Q('ResearchSubject.Specimen.primary_disease_type = "Nevi and Melanomas"')
 >>> q2 = Q('ResearchSubject.Diagnosis.age_at_diagnosis < 30*365')
 >>> q3 = Q('ResearchSubject.Specimen.identifier.system = "GDC"')
 
 >>> q = q1.And(q2.And(q3))
 >>> r = q.run()
 
 >>> print(r)
 
 
 Getting results from database
 
 Total execution time: 9604 ms
 
 QueryID: 2cd1f165-f6f5-49e4-b699-b4df191a540f
 Query: SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.Specimen) AS _Specimen, UNNEST(_ResearchSubject.Diagnosis) AS _Diagnosis, UNNEST(_Specimen.identifier) AS _identifier WHERE ((_Specimen.primary_disease_type = 'Nevi and Melanomas') AND ((_Diagnosis.age_at_diagnosis < 30*365) AND (_identifier.system = 'GDC')))
 Offset: 0
 Count: 100
 Total Row Count: 663
 More pages: False


By comparing the ``Count`` value of the two results we can see that all the Subjects returned in the initial query are coming from the GDC.

To explore the results further, we can fetch the Subject JSON objects by iterating through the results:

.. code-block:: python

 >>> projects = set()
 
 >>> for subject in r:
 >>>     research_subjects = subject['ResearchSubject']
 >>>     for rs in research_subjects:
 >>>         projects.add(rs['member_of_research_project'])
 
 >>> print(projects)
 {'FM-AD', 'TCGA-SKCM'}


The output shows the projects where Nevi and Melanomas cases appear.

Example Query 3: Or
+++++++

**Identify all samples that meet the following conditions:**

* **Sample is from primary tumor**
* **Disease is ovarian or breast cancer**
* **Subjects are females under the age of 60 years**

.. code-block:: python

 >>> tumor_type = Q('ResearchSubject.Specimen.source_material_type = "Primary Tumor"')
 >>> disease1 = Q('ResearchSubject.primary_disease_site = "Ovary"')
 >>> disease2 = Q('ResearchSubject.primary_disease_site = "Breast"')
 >>> demographics1 = Q('sex = "female"')
 >>> demographics2 = Q('days_to_birth > -60*365') # note that days_to_birth is a negative value
 
 >>> q1 = tumor_type.And(demographics1.And(demographics2))
 >>> q2 = disease1.Or(disease2)
 >>> q = q1.And(q2)
 
 >>> r = q.run()
 >>> print(r)
 
 Getting results from database
 
 Total execution time: 20529 ms
 
 QueryID: 2b325482-f764-4675-aebe-43f7e8d4004a
 Query: SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.Specimen) AS _Specimen WHERE (((_Specimen.source_material_type = 'Primary Tumor') AND ((all_v2_1.sex = 'female') AND (all_v2_1.days_to_birth > -60*365))) AND ((_ResearchSubject.primary_diagnosis_site = 'Ovary') OR (_ResearchSubject.primary_diagnosis_site = 'Breast')))
 Offset: 0
 Count: 100
 Total Row Count: 28040
 More pages: True


In this case, we have a result that contains more than 100 records which is the default page size. To load the next 100 records, we can use the ``next_page()`` method:

.. code-block:: python

 >>> r2 = r.next_page()
 
 >>> print(r2)
 
 QueryID: 92f1a560-5385-49d9-a477-286c16f7f67c
        Query: SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.Specimen) AS _Specimen WHERE (((_Specimen.source_material_type = 'Primary Tumor') AND ((all_v2_1.sex = 'female') AND (all_v2_1.days_to_birth > -60*365))) AND ((_ResearchSubject.primary_diagnosis_site = 'Ovary') OR (_ResearchSubject.primary_diagnosis_site = 'Breast')))
        Offset: 100
        Count: 100
        Total Row Count: 28040
        More pages: True


Alternatively, we can use the ``offset`` argument to specify the record to start from:

.. code-block:: python
 ...
 >>> r = q.run(offset=100)
 >>> print(r)
 
 Getting results from database

Total execution time: 4278 ms

        QueryID: ee2150d8-11fb-4720-a0b3-0352f2d4a38f
        Query: SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.Specimen) AS _Specimen WHERE (((_Specimen.source_material_type = 'Primary Tumor') AND ((all_v2_1.sex = 'female') AND (all_v2_1.days_to_birth > -60*365))) AND ((_ResearchSubject.primary_diagnosis_site = 'Ovary') OR (_ResearchSubject.primary_diagnosis_site = 'Breast')))
        Offset: 100
        Count: 100
        Total Row Count: 28040
        More pages: True


Example Query 4: From
+++++

**Find data for donors with "Ovarian Serous Cystadenocarcinoma" with proteomic and genomic data.**

.. note::
  **Disease type values denoting the same disease groups can be completely different within different systems. This is where CDA features come into play.** We first start by exploring the values available for this particular field in both systems.

>>> unique_terms('ResearchSubject.primary_diagnosis_condition', system="GDC",limit=10)
[None,
 'Acinar Cell Neoplasms',
 'Adenomas and Adenocarcinomas',
 'Adnexal and Skin Appendage Neoplasms',
 'Basal Cell Neoplasms',
 'Blood Vessel Tumors',
 'Chronic Myeloproliferative Disorders',
 'Complex Epithelial Neoplasms',
 'Complex Mixed and Stromal Neoplasms',
 'Cystic, Mucinous and Serous Neoplasms']
 
 
Since “Ovarian Serous Cystadenocarcinoma” doesn’t appear in GDC values let's take a look into the PDC:

>>> unique_terms('ResearchSubject.primary_diagnosis_condition', system="PDC")
['Acute Myeloid Leukemia',
 'Breast Invasive Carcinoma',
 'Chromophobe Renal Cell Carcinoma',
 'Clear Cell Renal Cell Carcinoma',
 'Colon Adenocarcinoma',
 'Early Onset Gastric Cancer',
 'Glioblastoma',
 'Head and Neck Squamous Cell Carcinoma',
 'Hepatocellular Carcinoma ',
 'Lung Adenocarcinoma',
 'Lung Squamous Cell Carcinoma',
 'Oral Squamous Cell Carcinoma',
 'Other',
 'Ovarian Serous Cystadenocarcinoma',
 'Pancreatic Ductal Adenocarcinoma',
 'Papillary Renal Cell Carcinoma',
 'Pediatric/AYA Brain Tumors',
 'Rectum Adenocarcinoma',
 'Uterine Corpus Endometrial Carcinoma']
 
After examining the output, we see that it does come from the PDC. Hence, if we could first identify the data that has research subjects found within the PDC that have this particular disease type, and then further narrow down the results to include only the portion of the data that is present in GDC, we could get the records that we are looking for.

.. code-block:: python

 >>> q1 = Q('ResearchSubject.primary_diagnosis_condition = "Ovarian Serous Cystadenocarcinoma"')
 >>> q2 = Q('ResearchSubject.identifier.system = "PDC"')
 >>> q3 = Q('ResearchSubject.identifier.system = "GDC"')
 
 >>> q = q3.From(q1.And(q2))
 >>> r = q.run()
 
 >>> print(r)
 Getting results from database

Total execution time: 35006 ms

        QueryID: a2ce5a91-bca5-411e-ad51-b6039ced6d5e
        Query: SELECT all_v2_1.* FROM (SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.identifier) AS _identifier WHERE ((_ResearchSubject.primary_diagnosis_condition = 'Ovarian Serous Cystadenocarcinoma') AND (_identifier.system = 'PDC'))) AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.identifier) AS _identifier WHERE (_identifier.system = 'GDC')
        Offset: 0
        Count: 100
        Total Row Count: 275
        More pages: True

As you can see, this is achieved by utilizing ``From`` operator. The ``From`` operator allows us to create queries from results of other queries. This is particularly useful when working with conditions that involve a single field which can take multiple different values for different items in a list that is being part of, e.g. we need ``ResearchSubject.identifier.system`` to be both “PDC” and “GDC” for a single Subject. In such cases, ``And`` operator can’t help because it will return those entries where the field takes both values, which is zero entries.

 >>> r = q1.run()
 >>> r = q1.run(limit=2)            # Limit to two results per page
 
 >>> r.sql   # Return SQL string used to generate the query e.g.
 "SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject WHERE (_ResearchSubject.primary_diagnosis_condition = 'Ovarian Serous Cystadenocarcinoma')"
 
 >>> print(r) # Prints some brief information about the result page eg:
 QueryID: 0d080ca0-1298-4da1-8654-593c92fad1f0
        Query: SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject WHERE (_ResearchSubject.primary_diagnosis_condition = 'Ovarian Serous Cystadenocarcinoma')
        Offset: 0
        Count: 2
        Total Row Count: 283
        More pages: True
 
 >>> r[0] # Returns nth result of this page as a Python dict e.g.
 {'id': 'TCGA-61-1724',
 'identifier': [{'system': 'GDC', 'value': 'TCGA-61-1724'},
  {'system': 'PDC', 'value': 'TCGA-61-1724'}],
 'species': 'Homo sapiens',
 'sex': 'female',
 'race': 'white',
 'ethnicity': 'not hispanic or latino',
 'days_to_birth': '-17168',
 'subject_associated_project': ['TCGA-OV', 'CPTAC-TCGA', 'CPTAC-TCGA'],
 'vital_status': 'Dead',
 'age_at_death': '637',
 'cause_of_death': None,
 'File': [{'id': '14a0766c-6ca4-47bb-ac70-62133c30c1c5',
   'identifier': [{'system': 'GDC',
     'value': '14a0766c-6ca4-47bb-ac70-62133c30c1c5'}],
   'label': 'OV.focal_score_by_genes.txt',
   'data_category': 'Copy Number Variation',
   'data_type': 'Gene Level Copy Number Scores',
   'file_format': 'TXT',
   'associated_project': 'TCGA-OV',
   'drs_uri': 'drs://dg.4DFC:14a0766c-6ca4-47bb-ac70-62133c30c1c5',
   'byte_size': '26280573',
   'checksum': '22e40a89cdeebbc162896f1cdfe7e55e',
   'data_modality': 'Genomic',
   'imaging_modality': None,
   'dbgap_accession_number': None},
   ...
  
 >>> r.pretty_print(0) # Prints the nth result nicely
 {
    "id": "TCGA-61-1724",
    "identifier": [
        {
            "system": "GDC",
            "value": "TCGA-61-1724"
        },
        {
            "system": "PDC",
            "value": "TCGA-61-1724"
        }
    ],
    "species": "Homo sapiens",
    "sex": "female",
    "race": "white",
    "ethnicity": "not hispanic or latino",
    "days_to_birth": "-17168",
    "subject_associated_project": [
        "TCGA-OV",
        "CPTAC-TCGA",
        "CPTAC-TCGA"
    ],
    "vital_status": "Dead",
    "age_at_death": "637",
    "cause_of_death": null,
    "File": [
        {
            "id": "14a0766c-6ca4-47bb-ac70-62133c30c1c5",
            "identifier": [
                {
                    "system": "GDC",
                    "value": "14a0766c-6ca4-47bb-ac70-62133c30c1c5"
                }
            ],
            "label": "OV.focal_score_by_genes.txt",
            "data_category": "Copy Number Variation",
            "data_type": "Gene Level Copy Number Scores",
            "file_format": "TXT",
            "associated_project": "TCGA-OV",
            "drs_uri": "drs://dg.4DFC:14a0766c-6ca4-47bb-ac70-62133c30c1c5",
            "byte_size": "26280573",
            "checksum": "22e40a89cdeebbc162896f1cdfe7e55e",
            "data_modality": "Genomic",
            "imaging_modality": null,
            "dbgap_accession_number": null
        },
    ...
   
 >>> r2 = r.next_page()  # Fetches the next page of results
 >>> print(r2)
 QueryID: 0d080ca0-1298-4da1-8654-593c92fad1f0
        Query: SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject WHERE (_ResearchSubject.primary_diagnosis_condition = 'Ovarian Serous Cystadenocarcinoma')
        Offset: 2
        Count: 2
        Total Row Count: 283
        More pages: True


Example Query 5: From continued (IDC)
+++++

**Find data for donors with "Ovarian Serous Cystadenocarcinoma" with proteomic and imaging data.**

So now we would like to repeat the previouse query but this time identify cases that are also in IDC. As noted before disease type value denoting the same disease groups can be completely different within different systems. So let's explore the values available for this particular field in IDC.

>>> unique_terms('ResearchSubject.primary_disease_type', system="IDC",limit=10)
[]

Oh no! looks like we have an empty set. This is because IDC does not have `ResearchSubject` (or Specimen) intities, only Subject intities (see .. ref:: here `ETL` for more information). So, let try the same code as `Example Query 4: From`_ but change the ``ResearchSubject.identifier.system`` to **IDC** instead of **GDC**. 

.. code-block:: python
  q1 = Q('ResearchSubject.primary_diagnosis_condition = "Ovarian Serous Cystadenocarcinoma"')

  q2 = Q('ResearchSubject.identifier.system = "PDC"')
  q3 = Q('ResearchSubject.identifier.system = "IDC"')
  
  q = q3.From(q1.And(q2))
  r = q.run()
  
  print(r)
  
  Getting results from database

Total execution time: 8746 ms

        QueryID: fc470d8d-a23d-4711-a79e-101226253108
        Query: SELECT all_v2_1.* FROM (SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.identifier) AS _identifier WHERE ((_ResearchSubject.primary_diagnosis_condition = 'Ovarian Serous Cystadenocarcinoma') AND (_identifier.system = 'PDC'))) AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.identifier) AS _identifier WHERE (_identifier.system = 'IDC')
        Offset: 0
        Count: 0
        Total Row Count: 0
        More pages: False


Hmm, zero results. Looks like we made a similar mistake and once again included `ResearchSubject`. If we look at the available searchable fields again using ``columns()``, we will see that there is another field named ``identifier.system`` at the Subject level. So, let's try that:

.. code-block:: python
  q1 = Q('ResearchSubject.primary_diagnosis_condition = "Ovarian Serous Cystadenocarcinoma"')
  q2 = Q('ResearchSubject.identifier.system = "PDC"')
  q3 = Q('identifier.system = "IDC"')
  
  q = q3.From(q1.And(q2))
  r = q.run()
  
  print(r)
  
  Getting results from database

Total execution time: 17130 ms

        QueryID: 92c68759-8516-4b12-bbcd-4554495f4748
        Query: SELECT all_v2_1.* FROM (SELECT all_v2_1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.identifier) AS _identifier WHERE ((_ResearchSubject.primary_diagnosis_condition = 'Ovarian Serous Cystadenocarcinoma') AND (_identifier.system = 'PDC'))) AS all_v2_1, UNNEST(identifier) AS _identifier WHERE (_identifier.system = 'IDC')
        Offset: 0
        Count: 37
        Total Row Count: 37
        More pages: False


After a quick fix we now have 37 cases.


Example query 6: Return all data
++++

In some instances you may want to return all of the data to build/process your own database. This can be done by queries for data in any of the Data Commons using the ``identifier.system`` columns and ``OR`` operator.

.. code-block:: python
  q = query('identifier.system = "GDC" OR identifier.system = "PDC" OR identifier.system = "IDC"')
  r = q.run()
  r
  
  Getting results from database
  
  Total execution time: 25049 ms
  
  QueryID: 211bf374-62bd-477e-8bc6-5c7954eb587f
  Query: SELECT all_v1.* FROM gdc-bq-sample.integration.all_v1 AS all_v1, UNNEST(identifier) AS _identifier WHERE (((_identifier.system = 'GDC') OR (_identifier.system = 'PDC')) OR (_identifier.system = 'IDC'))
  Offset: 0
  Count: 100
  Total Row Count: 104731
  More pages: True

query()
-----

To ease the query writing process, we have also implimented ``query`` which allows ``AND``, ``OR`` and ``FROM`` to be included in the query string without the need of an additional step to use operators. The following `Q` query:

.. code-block:: python
 
 >>> q1 = Q('ResearchSubject.Specimen.primary_disease_type = "Nevi and Melanomas"')
 >>> q2 = Q('ResearchSubject.Diagnosis.age_at_diagnosis < 30*365')
 >>> q3 = Q('ResearchSubject.Specimen.identifier.system = "GDC"')
 
 >>> q = q1.And(q2.And(q3))
 
can be rewritten using the `query` function:

>>> query('ResearchSubject.Specimen.primary_disease_type = "Nevi and Melanomas" AND ResearchSubject.Diagnosis.age_at_diagnosis < 30*365 AND ResearchSubject.identifier.system = "GDC"')
>>> result = q1.run()

Q.sql()
-----

In some cases more complex queries are required, and for that purpose we have implimented ``Q.sql()`` which takes in a SQL style query

.. code-block:: python

 r1 = Q.sql("""
 SELECT
 *
 FROM gdc-bq-sample.cda_mvp.v1, UNNEST(ResearchSubject) AS _ResearchSubject
 WHERE (_ResearchSubject.primary_disease_type = 'Adenomas and Adenocarcinomas')
 """)
 
 >>> r1.pretty_print(0)
 { 'Diagnosis': [],
  'ResearchSubject': [ { 'Diagnosis': [],
                         'Specimen': [],
                         'associated_project': 'CGCI-HTMCP-CC',
                         'id': '4d54f72c-e8ac-44a7-8ab9-9f20001750b3',
                         'identifier': [ { 'system': 'GDC',
                                           'value': '4d54f72c-e8ac-44a7-8ab9-9f20001750b3'}],
                         'primary_disease_site': 'Cervix uteri',
                         'primary_disease_type': 'Adenomas and '
                                                 'Adenocarcinomas'}],
  'Specimen': [],
  'associated_project': 'CGCI-HTMCP-CC',
  'days_to_birth': None,
  'ethnicity': None,
  'id': 'HTMCP-03-06-02177',
  'id_1': '4d54f72c-e8ac-44a7-8ab9-9f20001750b3',
  'identifier': [ { 'system': 'GDC',
                    'value': '4d54f72c-e8ac-44a7-8ab9-9f20001750b3'}],
  'primary_disease_site': 'Cervix uteri',
  'primary_disease_type': 'Adenomas and Adenocarcinomas',
  'race': None,
  'sex': None}

Test queries
----

Test query 1
+++++

**Find data from all Subjects who have been treated with "Radiation Therapy, NOS" and have both genomic and proteomic data.**

.. toggle-header::

  :header: Example 1 **Show/Hide Code**
    
    .. code-block:: python
    
      q1 = Q('ResearchSubject.Diagnosis.Treatment.treatment_type = "Radiation Therapy, NOS"')
      q2 = Q('ResearchSubject.identifier.system = "PDC"')
      q3 = Q('ResearchSubject.identifier.system = "GDC"')
      
      q = q2.From(q1.And(q3))
      r = q.run()
      
      print(r)
      
      Getting results from database
      
      Total execution time: 27414 ms
      
      QueryID: a8eabfc7-7258-45cb-8570-763ec4d1926c
      Query: SELECT all_v1.* FROM (SELECT all_v1.* FROM gdc-bq-sample.integration.all_v1 AS all_v1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.Diagnosis) AS _Diagnosis, UNNEST(_Diagnosis.Treatment) AS _Treatment, UNNEST(_ResearchSubject.identifier) AS _identifier WHERE ((_Treatment.treatment_type = 'Radiation Therapy, NOS') AND (_identifier.system = 'GDC'))) AS all_v1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.identifier) AS _identifier WHERE (_identifier.system = 'PDC')
      Offset: 0
      Count: 100
      Total Row Count: 369
      More pages: True


Test query 2
+++++

**Find data from TCGA-BRCA project, with donors over the age of 50 with imaging data**

.. code-block:: python
  q1 = Q('ResearchSubject.associated_project = "TCGA-BRCA"')
  q2 = Q('days_to_birth > -50*365')
  q3 = Q('identifier.system = "IDC"')
  
  q = q3.From(q1.And(q2))
  r = q.run()
  
  print(r)
  
  Getting results from database
  
  Total execution time: 24125 ms
  
  QueryID: a5de2545-2b5e-476c-9e92-b768d058f603
  Query: SELECT all_v1.* FROM (SELECT all_v1.* FROM gdc-bq-sample.integration.all_v1 AS all_v1, UNNEST(ResearchSubject) AS _ResearchSubject WHERE ((_ResearchSubject.associated_project = 'TCGA-BRCA') AND (all_v1.days_to_birth < -50*365))) AS all_v1, UNNEST(identifier) AS _identifier WHERE (_identifier.system = 'IDC')
  Offset: 0
  Count: 88
  Total Row Count: 88
  More pages: False


Pointing to a custom CDA instance
----

``.run()`` will execute the query on the public `CDA API <https://cda.cda-dev.broadinstitute.org/api/cda/v1/>_.

``.run("http://localhost:8080")`` will execute the query on a CDA server running at
``http://localhost:8080``.

Quick Explanation on UNNEST usage in BigQuery
----

Using Q in the CDA client will echo the generated SQL statement that may contain multiple `UNNEST` inclusions
when including a dot(.) structure which may need a quick explanation.
UNNEST is similar to unwind in which embedded data structures must be flattend to appear in a table or Excel file.
Note; The following call using the SQL endpoint is not the preferred method to execute a nested attribute query in BigQuery.
The Q language DSL abstracts the required unnesting that exists in a Record. In BigQuery, structures must be represented in an UNNEST syntax such that:
``A.B.C.D`` must be unwound to ``SELECT (_C.D)`` in the following fashion: 

```
SELECT (_C.D) 
from TABLE, UNNEST(A) AS _A, UNNEST(_A.B) as _B, UNNEST(_B.C) as _C
```

``ResearchSubject.Specimen.source_material_type`` represents a complex record that needs to unwound in SQL syntax to be queried on properly when using SQL.

```
SELECT DISTINCT(_Specimen.source_material_type) 
FROM gdc-bq-sample.cda_mvp.v3, 
UNNEST(ResearchSubject) AS _ResearchSubject,
UNNEST(_ResearchSubject.Specimen) AS _Specimen
```

Test query answers
----

Test query 1
+++++
**Find data from all Subjects who have been treated with "Radiation Therapy, NOS" and have both genomic and proteomic data.**

.. code-block:: python

  q1 = Q('ResearchSubject.Diagnosis.Treatment.treatment_type = "Radiation Therapy, NOS"')
  q2 = Q('ResearchSubject.identifier.system = "PDC"')
  q3 = Q('ResearchSubject.identifier.system = "GDC"')
  
  q = q2.From(q1.And(q3))
  r = q.run()
  
  print(r)
  
  Getting results from database
  
  Total execution time: 27414 ms
  
  QueryID: a8eabfc7-7258-45cb-8570-763ec4d1926c
  Query: SELECT all_v1.* FROM (SELECT all_v1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.Diagnosis) AS _Diagnosis, UNNEST(_Diagnosis.Treatment) AS _Treatment, UNNEST(_ResearchSubject.identifier) AS _identifier WHERE ((_Treatment.treatment_type = 'Radiation Therapy, NOS') AND (_identifier.system = 'GDC'))) AS all_v1, UNNEST(ResearchSubject) AS _ResearchSubject, UNNEST(_ResearchSubject.identifier) AS _identifier WHERE (_identifier.system = 'PDC')
  Offset: 0
  Count: 100
  Total Row Count: 369
  More pages: True


Test query 2
+++++

**Find data from TCGA-BRCA project, with donors over the age of 50 with imaging data**

.. code-block:: python

  q1 = Q('ResearchSubject.associated_project = "TCGA-BRCA"')
  q2 = Q('days_to_birth > -50*365')
  q3 = Q('identifier.system = "IDC"')
  
  q = q3.From(q1.And(q2))
  r = q.run()
  
  print(r)
  
  Getting results from database
  
  Total execution time: 24125 ms
  
  QueryID: a5de2545-2b5e-476c-9e92-b768d058f603
  Query: SELECT all_v1.* FROM (SELECT all_v1.* FROM gdc-bq-sample.integration.all_v2_1 AS all_v2_1, UNNEST(ResearchSubject) AS _ResearchSubject WHERE ((_ResearchSubject.associated_project = 'TCGA-BRCA') AND (all_v1.days_to_birth < -50*365))) AS all_v2_1, UNNEST(identifier) AS _identifier WHERE (_identifier.system = 'IDC')
  Offset: 0
  Count: 88
  Total Row Count: 88
  More pages: False
