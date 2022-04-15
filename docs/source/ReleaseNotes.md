# CDA Release 3.0

## Release Notes

Last updated: March 22nd, 2022

### Introduction to CDA

![FigIntro](./ReleaseNotesFigs/CancerDataAggregator_PMD_0.png)

The Release 3.0 of CDA searches across data from the Genomics Data Commons (GDC), the Proteomics Data Commons (PDC), and the Imaging Data Commons (IDC) to aggregate and return data to users via a single application programming interface (API). CDA leverages the work and data model that is concurrently being developed by the [Center for Cancer Data Harmonization](https://datascience.cancer.gov/data-commons/center-cancer-data-harmonization-ccdh) (CCDH). CCDH will provide a single data model that harmonizes syntax and semantics across the CRDC systems and services.

The CCDH data model promises to be a specimen-centric model whereas current CRDC nodes tend to use a case-centric approach.  The diagrams below depict the shift from the respective GDC and PDC entity models (provided by CCDH - Figure 1) towards a specimen-centric model (Figure 2).

| ![figure](./ReleaseNotesFigs/GDCPDCModels.png) |
|:---:|
| **Figure 1**: The PDC and GDC data models are case centric. |



| ![figure](./ReleaseNotesFigs/CCDH_Specimen-centric_Jun2020.png) |
|:---:|
| **Figure 2**: CCDH is moving towards a specimen centric model. |

As the CCDH model develops, CDA leverages the harmonization work of the [CCDH model](https://cancerdhc.github.io/ccdhmodel/entities/) by extending the model only where necessary, such as adding key search fields, to support CDA functionality.  CDA periodically synchronizes with CCDH to maintain consistency between the  [data model](https://github.com/CancerDataAggregator/cda-data-model) implemented in CDA and the developing CCDH model.  This data model is expressed as JSON Schema.


| ![figure](./ReleaseNotesFigs/CDA_MVP_Release_1.png) |
|:---:|
| **Figure 3**: The <a href="https://github.com/CancerDataAggregator/cda-data-model">data model</a> is expressed as JSON schema. |

In Figure 3, the entities rimmed in blue are not yet part of the CCDH model but are extensions to allow CDA to aggregate and deliver data as the CCDH model evolves. It may be helpful to think about your queries in terms of these entities (e.g. Specimen, Subject, Research Subject, Project, Diagnosis) and their attributes (e.g. derived_from_subject, ethnicity, reference_assembly).

When the data is fully harmonized, finding data for a specific subject or a specific specimen across all nodes will be simplified.  For Release 1, CDA was able to identify common subjects across GDC and PDC in cases where the PDC “case_submitter_id” is equal to the GDC “submitter_id”, essentially harmonizing this field. Further alignment was achieved by using lower case characters consistently. For Release 2, CDA similarly merged IDC with GDC and PDC data by matching IDC “PatientID” with GDC “submitter_id” and PDC “case_submitter_id”.

To assist you with the transition to CCDH data model terminology, we have provided a field by field mapping of terms from the GDC, PDC[^1], and IDC data dictionaries to the implemented JSON schema. This information can be found in [CDA Schema Field Mapping](./Schema.md).

For details on the extraction, transformation, load (ETL) process, please see [CDA ETL Process](./ETL.md).


## Datasets & Fields

* All datasets updated as follows
    * GDC: v31.0, 03/17/2022
    * PDC: v2.7, 03/18/2022
    * IDC: v.4.0, 03/09/2022

## Enhanced query functionality

* Added support for IN and LIKE SQL statements
* The Subjects query has been simplified to not return file information, instead the new files Q method can be used for files
* Counts function added to Q that gives total counts for each Data Commons given a query
* Filter flag added to Q's run method which allows horizontal filtering of results
* Verbose flag added to Q's run method to hide/show Q actions when running a query
* Query's on text fields are now case insensitive
* Added to_dataframe to Q's Result object that converts the JSON structure to a pandas dataframe
* Added paginator to Q's Result object that allows for pagination through result pages. This also has a flag for paginating as a dataframe.


## Metadata Changes

* See [CDA Schema Field Mapping](./Schema.md)

* Summary
    * Previous table format now called Subjects endpoint
        * Replaced all File entities with Files - a list of file ids associated with the entity that the list is located in. e.g
            * File -> Files
            * ResearchSubject.File -> ResearchSubject.Files
            * ResearchSubject.Specimen.File -> ResearchSubject.Specimen.Files
    * Files endpoint added:
        * Endpoint oriented around File information
        * Includes all information regarding the file's associated entities(Subject, ResearchSubject, and Specimen)
    * Newly available fields:
        * None
    * Renamed fields (old -> new):
        * None


## Bug fixes

* Fixed issue where queries on list columns that were not lists of json objects (i.e. subject_associated_project) would fail
* Duplicate files should no longer be returned


## Known bugs and issues

* tumor stages are not harmonized, there are redundant terms (complicates query)
* Days_to_birth should be reformatted (currently negative) or have an example query
* Docker jupyter notebook does not work if a notebook is already open in port 8888

<!-- Footnotes themselves at the bottom. -->

[^1]:
     Information pulled from the PDC API may contain embargoed data.
