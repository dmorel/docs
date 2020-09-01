## Notes from "Scaling Big Data Mining Infrastructure: The Twitter Experience" 
### [Article PDF](http://www.kdd.org/sites/default/files/issues/14-2-2012-12/V14-02-02-Lin.pdf)
### Jimmy Lin and Dmitriy Ryaboy, Twitter, Inc.
* 100TB data ingested per day in main cluster
* 10s of thousands of workflows covering everything in the site; application domains are infinite
* Significant amount of tooling (low and high level) and exploratory/cleaning work is needed

    > The “core” of what
academic researchers think of as data mining—translating
domain insight into features and training models for various
tasks—is a comparatively small, albeit critical, part of the
overall insight-generation lifecycle

* Schemas are important to figure out what data is available, but this is not enough; large amounts of data/tables make it really hard for data scientists (we'll call them DS) to figure out what is available and how, and what is needed to clean up and use the source data

* Making it all work together reliably at the infra+application level (plumbing) is complicated (heterogenous componenents, etc)
* Bigdata is the successor to BI from the 90s. OLAP(online analytical processing): ETL (extract, transform, load) => descriptive statistics => dashboards, drill-down, other.
* But today DS want predictive analytics
    * ML techniques (model training)
    * latent facts by mining large amounts of data (k-means, bayes, others)
* Times have changed; what used to be rocket science is now wanted (needed?) by everyone
* Open-source at the forefront; the Hadoop stack is the de facto standard

* DS research on big data usually starts with an ill-defined problem, contrary to the usual data mining process; the question is fuzzy and the process starts with making the data usable.
    * usually a business objective, for which data questions are needed but not defined
    
        > Before beginning exploratory data analysis, the data scientist needs to know what data are available and how they are organized (surprisingly difficult in practice)
    * data sources are heterogenous in meaning; unifying them for a given problem is a 1st step (example: various log formats for various subsystems)
    * low interaction between engineers and DS, so the information may be unavailable or spread between different sources
    * services, logs, fields and their meanings, they all change over time; no guarantee
    * ideally DS and engineers work closely together so the sources are as close to something usable globally as possible; hard in practice, and speed of dev/ease of analysis are pulling in different directions
    * exactness is impossible other than through cleaning and statistical assessment of validity; surprisingly difficult (define metrics, thresholds, etc; requires a lot of experience)
    * sanity checks reveal sudden shifts (systems changes, usage changes, bursts), takes inter-team cooperation; surprises will keep happening over time

* only once source data is explored and cleaned up/assembled, real DS work can start
    * questions become clearly defined based on what is available
    * classifiers definition, training and validation, etc
* putting classifiers in production requires many things
    * handling failure and retries, data dependencies, requires serious tooling effort
    * re-training classifiers all the time
    * detecting adversaries (spamming, etc) and user behaviour modification (don't let the classifiers enforce self-reinforcing funnels): it has to be adjusted to keep working
    * A/B tests for everything puts further load on the stack
* successful DS problem/project leads to further questions and projects
* Responsabilities
    * DS is responsible for the initial work (including deployment)
    * infra makes sure it works in the long run
    * DS keeps the responsability of making it work on a functional level (accuracy, drifts, source data changes, etc)
* general lack of emphasis in DS litterature and training on everything that is not directly DS, but is needed to make it work
    * data exploration, formatting, cleanup and validation
    * architectural considerations would help selecting lighter algorithms etc
    * awareness is growing, but long way to go
* Schemas
    * core business data is usually defined clearly (schema, checks, etc)
    * high-volume data like logs is usually loosely defined and of lower quality
    * don't use DBs to log (write latency from application, scaling, overkill for the task)
    * enforcing a schema for everything reduces agility
    * log writes to HDFS are done with Scribe at Twitter, Flume and Kafka are other solutions; mandatory use of one of these is a settled matter
    * plain text: avoid it
* JSON
    * flexible, delimited, compressible
    * no strict types, keys can move, be misspelled... many potential issues
    * no schema makes it really hard to use in the long term
* Thrift (or ProtocolBuffers or Avro) was chosen
    * stricter typed schema, optional fields for extension, code generators written for MR, Pig, etc.
    * benefit for DS is self-documentation from the type definitions; less exploration
    * leverage HCatalog to keep a master registry of data structure (what's under the hood is hidden); glue needed
    * added wrapper around HCatalog to add source data information, to build a complete graph of data generation; useful to know what consumes what, and what should be reprocessed in the event of data corruption upstream
    * reconstructing user actions is hard (different log sources) -> enforce a mandatory shared grammar for all sources, with flexibility in what can be added
    
        > The takeaway message
here is that enforcing some degree of semantic homogeneity
can have big payoffs, although we are also quick to point out
that this is not possible or practical in many cases.

* Plumbing is important
    * easy to DoS MySQL with Hadoop -> throttle, push back, etc
    * accomodate differing characteristics of systems
* More data wins over more complex algorithms (proven by research): dumber classifiers on big data works better than smarter one on a smaller set
* Improving relevance through ML and analytics provide a better product, which provides better information to further improve the product (virtuous circle)
* Most machine learning frameworks and tools are not end-to-end solutions; much glue is needed in particular to get the data in and out
* Twitter chose to implement ML as Pig extensions to remove the need for glue
    * Other approaches: MADLib (for SQL), RHIPE, etc; no solution is mature at this stage
* the visualisation of data is still an open question; d3.js is standard for engineers, but tools for use anywhere on massive sets (no preliminary aggregation) are still to invent
* Real-time analysis of massive datasets is still a problem to be solved too; Dremel,
Spark, PowerDrill, and Impala are not yet generic solutions
* Conclusion
    * Balance between ease and speed of development, analytics capability, robustness, etc
    
        >  Ideally, we would like an analytics
framework that provides all the benefits of schemas,
data catalogs, integration hooks, robust data dependency
management and workflow scheduling, etc. while requiring
zero additional overhead. This is perhaps a pipe dream, as
we can point to plenty of frameworks that have grown unusable
under their own weight, with pages of boilerplate code
necessary to accomplish even a simple task. How to provide
useful tools while staying out of the way of developers is a
difficult challenge.

    * there might be a series of (yet) unformal steps an organisation has to go through to manage big data and growth; a smooth path for transitioning from one step to the next (JSON to Thrift migration, etc) could be laid out and documented
    * teaching DS about the operational constraints could drive research in more efficient ways
* NOTE: the PDF contains numerous useful references not mentioned here

