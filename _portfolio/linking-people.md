---
permalink: /entityresolution/
title: Identity Resolution
excerpt: 'Exploring different models to eliminate redundant and erroneous text records from different sources to manage personally identifiable information(PII)'
category: Exploring Big Data
sidebar:
  title: "Big Data"
  nav: Big-Data
layouts_gallery:
  - url: /assets/images/mm-layout-splash.png
    image_path: /assets/images/mm-layout-splash.png
    alt: "splash layout example"
  - url: /assets/images/mm-layout-single-meta.png
    image_path: /assets/images/mm-layout-single-meta.png
    alt: "single layout with comments and related posts"
  - url: /assets/images/mm-layout-archive.png
    image_path: /assets/images/mm-layout-archive.png
    alt: "archive layout example"
last_modified_at: 2018-06-04T12:04:24-04:00
toc: true
---

Managing Personally Identifiable Information (PII)
--------------------------------------------------

### Overview

Complying with privacy and information security laws is increasingly
becoming more challenging for individuals and organizations. Events such
as the 2017 Equifax data breach of 143 million individual identities is
becoming a common occurrence. In 2017, Yahoo revised its 2013 500
million Yahoo user identity breach to 3 billion identities being stolen.
The scale of these privacy breaches is impacting an organization
business value and are devastating lives and greatly impacting our
individual freedoms. The risk is not just lax regulatory adherence and
fines, but potentially exposing weaknesses in our systems that
potentially can undermine the value of using data to improve
outcomes.[^1] Although the United States has not implemented regulation
as protective of its citizens' privacy as the European Union, the
federal government's [General Services
Administration](https://www.gsa.gov/reference/gsa-privacy-program/rules-and-policies-protecting-pii-privacy-act)
has a Privacy Act that defines personally identifiable information as:

> "... information that can be used to distinguish or trace an
> individual's identity, either alone or when combined with other
> personal or identifying information that is linked or linkable to a
> specific individual. The definition of PII is not anchored to any
> single category of information or technology. Rather, it requires a
> case-by-case assessment of the specific risk that an individual can be
> identified. In performing this assessment, it is important for an
> agency to recognize that non-PII can become PII whenever additional
> information is made publicly available --- in any medium and from any
> source --- that, when combined with other available information, could
> be used to identify an individual"[^2]

In practice, US businesses that conduct business with EU citizens are
mandated to data protection laws mainly focused on General Data
Protection Regulation (GDPR) as implemented by European jurisdictions.
In short, European regulation allows citizens of the EU to mandate that
any PII data be removed from an organizations data asset on request from
the individual. This is the so called "Right to be forgotten" principal.
Depending on jurisdiction and differing regulatory pressures, an
organizations ability to comply with protecting PII starts with managing
data assets effectively and promoting a culture that values privacy and
digital policies.

One of the most challenging aspects of complying with regulatory and/or
managing an organizations reputation risk can be directly tied to
keeping PII information secure and maintaining information governance
standards that effectively manages PII. One such approach to
managing PII data more effectively is for organizations to identify and
link persons across all its data assets into one consolidated entity
resolution index. This single source of truth would act as a master data
repository that all other systems could rely upon to secure PII.

Basic information governance will result from combining datasets and
performing large aggregate analyses using powerful new ways to improve
service across large populations. Critically important to achieving the
master data repository is the task of deduplicating identities across
multiple datasets that were rarely designed to work together.
Inconsistent data entry, typographical errors, and real world identity
changes pose significant challenges to this process.

Besides adhering to PII standards, the ability to correlate personal
identities from disparate data sources allows for approximate joins to
occur giving increased value to the joined datasets. One could consider
the Department of Homeland Security attempt at joining passenger data
with homeland security no-fly lists and other potential terrorism
security measures an extreme in linking related identities.

![test](/assets/images/walkingPeople.jpg)

### Description of Concepts Learned

"Entity resolution is to distinguish the representations referring to
the same real world object entity in one or more databases and recognize
all different real-world entities in the databases."[^4] According to
Wang there are two main types of classifications of entity resolution
based on pairwise entity resolution and group-wise entity resolution. As
an example, for entity resolution on a name set
`{o1=“Wei Wang”, o2=“W Wei”, o3=“Wang Wei”, o4=“Jian Pei”}` the result
of pair-wise entity resolution is `{(o1, o2), (o1, o3), (o2, o3)}` The
pairwise resolution refers to a pair of data objects referring to the
same real-world entity while the group-wise resolution refers to a
family clusters with each data object referring to the same real-world
entity. While Wang's approach is very useful to more typical record
linkage and deduplication efforts needed for cleaning structured
databases, many of today's data projects incorporate data from more
diverse sources. For instance, many projects require linking data from
sources that were not originally created with the intention of joining
or sharing with other less structured datasets. During my literature
search, it was revealed to me that machine learning and supervised
learning is one of the better solutions to entity resolution. The main
concepts to be learned are based on [Bilenko's
assertion](https://repositories.lib.utexas.edu/handle/2152/2681) that:

> "algorithms for these tasks rely on pre-defined similarity measures,
> such as edit distance or cosine similarity for strings, or Euclidean
> distance for vector-space data. However, standard distance functions
> are frequently suboptimal as they do not capture the appropriate
> notion of similarity for a particular domain, dataset, or
> application."[^5]

While the main focus of this paper is to explore the machine learning
concepts outlined in Bilenko's paper and test the implementation of
entity resolution using the open source [dedupe.io python
library](https://docs.dedupe.io/en/latest/) on three datasets (childhood
education, linked customers, campaign contributions), the challenges of
implementing entity resolution are explored as they relate to the type
of source files, the size of data and how to implement a single source
of truth within an organization using a PostGres database. For the
purpose of replicating the experiments in this project, a [Docker
container platform](https://www.docker.com/) was implemented and a
[GitHub repository](https://github.com/piccoloa/dedupePostgresDocker)
can be cloned to follow the results of each sample.

The ability to clean dirty data by detecting the objects referring to
the same real world entity will result in a consolidated entity based
management approach. Understanding the concepts learned combined with
implementing a scalable container based approach as created for the
studies could potentially allow a more effective master data index to
manage personally identifiable information. In short, the concepts
learned could help an organization effectively comply with "the right to
be forgotten".

### Describing the Supervised Learning Model [^6]

![test2](/assets/images/classifierSmart.jpg)

The "committee of classifiers" as depicted above is a good
representation of the learning process and how the user trains the
classifiers through an iterative process of labeling. There are four
main processes described in the learnable similarity functions as
outlined in Bilenko's paper and implemented using the python dedupe
library. Bilenko's notion of hierarchical clustering is implemented in
the dedupe library by creating functions that include: matching records;
making smart comparisons; grouping duplicates and choosing good
thresholds.

**Matching Records**

Matching records requires using similarity functions as they relate to
record similarity and field level similarity. String distances and
setting the weighted sums of field distances are used to measure pairs
of records and fields. Creating the acceptable distance string measure
is done using regularized logistic regression. The acceptable distances
are set by the learnable aspect of the function. These learned weights
are set by the labels created by the user training the model. While
training the model, the user interacts with the model variables to help
create acceptable distance measures and cut-offs.

Consider the fact that every pair would need to be measured to each
other, the number of comparisons becomes extremely complex. The number
of similarity comparisons that need to be made grows quadratically with
the size of the input dataset. As the size of the dataset increases,
comparing string similarities becomes problematic as all instance pairs
need to be compared. This is where the concept of blocking optimizes the
approach to comparing records.

**Making Smart Comparisons**

Blocking is implemented with the presumption that duplicates are rare in
the whole population of records and grouping similar blocks together
early in the process reduces the sets to be compared. Blocking occurs by
creating blocks of records that share a feature. Blocking methods apply
a key-based method using python dictionaries whereby each record is
appended with a unary predicate to the same block. The dedupe.io library
uses two types of blocks in its processing, predicate blocks and
canopies.

Predicate functions provide for keys and values for each field in a
record. The predicate could include "the first 3 characters of the
field," "every word in the field," and so on as a feature sets for
predicates are formed. Records with the same feature are included in
each block. An example of creating a predicate block could include the
first three letters of the site name for the Childhood Learning dataset:

|Id|Site Name | Address | Zip | Phone
|--|--|--|--|--|
| 1 |Hull House Association - Uptown Head Start / Hull House  | 10 S Kedzie Ave | | 9395115
| 2 |Hull House Association - Child Dev. Central Office- Hull House CDS-Homes  | 10 S Kedzie Ave | | 9395115
| 3 |Catholic Charities of the Archdiocese of Chicago - St. Kevin  | 10509 S Torrence Ave | | 4723558
| 4 |Catholic Charities of the Archdiocese of Chicago - St. Thomas  | 1 N North Ave  | | 4723558

The example above would create 2 blocks for, 'hul' contained in records
1 and 2 and 'cat' contained in records 3 and 4. The key value pairs
{'hul': (1,2), 'cat': (3,4)} would create a predate block function for
the first three letters of the site name field. This would be applied as
one type of predicate block to combine with similar records that all
contain the same first three letters for the site name field. There are
several predicate functions that the dedupe library applies including;
whole field, token field, common integer, whole field, token field,
common integer, same three char start, same five char start, same seven
char start, near integers, and common four gram and common six-gram
predicate functions.

A second type of blocking used in the dedupe library is index blocking.
The data structure for an index block is modeled based on an inverse
index. Inverse index structures are a major component of search
algorithms. An inverse index maps words to numbers for a given set of
documents.[^8] The dedupe.io model populates its inverse index for a
given model with all the unique value that appear in a field. Building
the index is the most time and memory consuming resources in applying
the model.

Post creating the predicate blocks and index blocks, the dedupe model
will combine blocks that include the same field to form cross-field
blocks. The concept of combining these blocks are discussed in Bilenko's
thesis under the topic of disjunctive blocking.

![test2](/assets/images/thesis.png)
[^9]

The learnable aspect of the model occurs as the optimal blocking
function are created during the supervised learning of the model. As the
user creates labels for each comparison, the dedupe library uses greedy
algorithms particularly Chvatal's Greedy Set-Cover algorithim.[^10] to
create acceptable weighted values for the string function comparisons.
An example of the supervised learning aspect of the model for the
Company Listing dataset is the figure below:

![test3](/assets/images/trainLabel.jpg)

Prior to creating the final training model for comparing the records and
performing the blocking and indexing on the whole dataset, supervised
learning occurs as the user labels a sample of records based on
comparing records. As each subsequent pair is labeled, the model
actively creates blocks to select additional records to be labeled by
the user. This is the notion of a learnable similarity function. This
supervised learning allows for active learning by the model that can be
continuously updated as new records get added to the dataset.

**Grouping Duplicates**

Beyond comparing two records, the dataset may have records that contain
multiple duplicates. An example would be three records having similarity
measures that are identical. This causes a problem which is resolved in
the dedupe library by using a hierarchical clustering with centroid
linkage. The clustering occurs by the presumption that all points with
the similar distance to the centroid are part of the same group.

There are two general approaches to hierarchical clustering, an
agglomerative approach and divisive approach. A hierarchical approach to
clustering is usually represented by a dendrogram where the root node
represents the whole set and each leaf node is regarded as a data point.
The intermediate nodes are described as the extent to which the objects
are proximal to each other. The height of the dendrogram expresses the
distance between each pair of data points or cluster. Clustering results
by cutting the dendrogram at different levels. The merging of a pair of
clusters or in this case records are formed by measuring distance
between cluster centroids. The proximity measure can be calculated
according to several methods including Euclidean, Mahalanobis, Pearson
and Cosine measures. The linkage between two groups in the dedupe
approach is done using a centroid method. The centroid method is
resolved by comparing the mean of all points with the cluster.

![test4](/assets/images/centroid.jpg)


**Choosing a Good Threshold**

In order to create a good threshold to predict if a pair of records are
identical, the library predicts a probability and measures it against a
threshold. The model creates an acceptable F-score that is based on
precision and recall measures calculated during the supervised learning
process as a sample of records are labeled. During the active learning
process, the random sample of blocked records are used to calculate
pairwise probabilities of the records that will be duplicated within
each of block. From learned probabilities, the model calculates the
expected precision and recall.


**Development of Container Environment**

In addition to effectively testing the dedupe.io python module for
resolving duplicates in three datasets; Childhood Leaning Center,
Linking Customer Names, and Campaign Contributions, a scalable Docker
containerized based approach was explored to allow for a repeatable
extract, transform, and load(ETL) process.

The containerized environment included four containers and a
self-contained network to transfer data between the containers and allow
for access to the environment when deployed in a production environment.
The containerized environment was created using Docker and a repo for
the project can be cloned at
<https://github.com/piccoloa/dedupePostgresDocker>.

"Compose is a tool for defining and running multi-container Docker
applications. With Compose, you use a YAML file to configure your
application's services. Then, with a single command, you create and
start all the services from your configuration."[^14]. The environment
was tested on a MacOs and a Linux virtual machine running VMWare.

![test5](/assets/images/dockerDedupe.jpg)

The four separate containers as visualized above:

>A.  Ubuntu OS with Python 3 including the installation of the Dedupe
    module (ETL)  a.  Built by dockerfile using needed python libraries.

>B.  Ubuntu OS with Posgresql 10 images (Master Database)
b.  https://hub.docker.com/\_/postgres/

>C.  Ubuntu OS with PHP webserver for accessing master database by
    browser(Web)
c.  https://hub.docker.com/\_/php/

>D.  Unbuntu OS with Anaconda and Jupyter notebook running data science
    stacks.(Test)d.  <https://github.com/jupyter/docker-stacks/tree/master/datascience-notebook>

>e.  Additional python libraries were added to container to allow for
        odbc connections


The tested container environment can be further enhanced to a production
version that would write results to the master data index. A similar ETL
environment was created while completing this research for this paper
and was deployed into production by my employer. A public GitHub repo
was created and can be used follow the examples for this paper.

![](/assets/images/gitRepo.jpg){width="4.0396041119860016in"
height="4.469011373578303in"}

References:
---

[^1]: Armerding, Taylor. "The 16 Biggest Data Breaches of the 21st
    Century." CSO Online. CSO, 11 Oct. 2017. Web. 21 Jan. 2018.

[^2]:

[^3]: ^2,3^ "\# Rules and Policies - Protecting PII - Privacy Act."
    https://www.gsa.gov/reference/gsa-privacy-program/rules-and-policies-protecting-pii-privacy-act.
    August 2018

[^4]: ^4^ Wang, Hongzhi. "Overview of Entity Resolution." Innovative
    Techniques and Applications of Entity Resolution. Hershey, PA:
    Information Science Reference/IGI Global, 2014. N. pag. Print.

[^5]: Bilenko, Mikhail. "Learnable Similarity Functions and Their
    Applications to Clustering and Record Linkage." Proceedings of the
    Ninth AAAI/SIGART Doctoral Consortium, pp. 981-982, San Jose, CA,
    July 2004

[^6]: ^6^ docs.dedupe.io.
    <https://docs.dedupe.io/en/latest/How-it-works.html>, Website
    Documentation, August 2018

[^7]: Entity Resolution Tutorial: Lise Getoor, University of Maryland;
    Ashwin Machanavajjhala, Duke University:
    <http://www.umiacs.umd.edu/~getoor/Tutorials/ER_VLDB2012.pdf>

[^8]: En.wikipedia.org. (2018). *Inverted index*. \[online\] Available
    at: https://en.wikipedia.org/wiki/Inverted\_index \[Accessed 12 Aug.
    2018\].

[^9]: Bilenko, Mikhail Yuryevich. *Learnable similarity functions and
    their application to record linkage and clustering*. Diss. 2006.
    Page 113 5.2.1

[^10]: Docs.dedupe.io. (2018). Making Smart Comparisons --- dedupe 1.9.0
    documentation. \[online\] Available at:
    https://docs.dedupe.io/en/latest/Making-smart-comparisons.html
    \[Accessed 12 Aug. 2018\].

[^11]: Domingos, Parag, and Pedro Domingos. "Multi-Relational Record
    Linkage." Department of Computer Science and Engineering University
    of Washington Seattle, WA 98195, U.S.A.
    {parag,pedrod}\@cs.washington.edu
    http://www.cs.washington.edu/homes/{parag,pedrod}

[^12]: Ramya, S., and C. Palaninehru. "A Study of Progressive Techniques
    for Efficient Duplicate Detection ." *International Journal of
    Advanced Research in Computer Science and Software Engineering*,
    vol. 5, no. 11, Nov. 2015,
    pdfs.semanticscholar.org/bf5f/72bd9d7fc330b08f9dcd05a454c94dcd89f7.pdf.

[^13]: Malhotra, Pankaj, and Puneet Agarwal. Incremental Entity
    Resolution from Linked Documents. Tata Consultancy Services Ltd, 13
    Jan. 2018. TCS Research; <https://arxiv.org/pdf/1402.4417.pdf>
    Patent Application No. 169/MUM/2014

[^14]: En.wikipedia.org. (2018). *Inverted index*. \[online\] Available
    at: https://en.wikipedia.org/wiki/Inverted\_index \[Accessed 12 Aug.
    2018\].
