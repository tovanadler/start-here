Compute Principal Coordinate Analysis on the Intersection of Two Datasets
=========================================================================

.. toctree::
   :maxdepth: 2

.. contents::

`Principal Coordinate Analysis <http://occamstypewriter.org/boboh/2012/01/17/pca_and_pcoa_explained/>`_
counts the number of variants two samples have in common.  These counts are then placed into an
``NxN`` matrix where ``N`` is the number of samples in the dataset.  The matrix is centered,
scaled, and then the first two principal components are computed for each invididual.

In the two-way version, the variants shared between two datasets are used to compute PCA among the individuals in both datasets.  This can be useful, for example, as an ethnicity check when comparing a dataset to 1,000 Genomes.  See codelab `Quality Control using Google Genomics`_ for an example of this.

An `Apache Spark`_ implementation is available.

Setup
-----

.. include:: ../../includes/spark_setup.rst

Run the job
-----------

The following command will run a two-way PCA over the BRCA1 region within the `Platinum Genomes`_ dataset and the `1,000 Genomes`_ phase 1 variants.

.. code-block:: shell

  spark-submit \
    --class com.google.cloud.genomics.spark.examples.VariantsPcaDriver \
    --conf spark.shuffle.spill=true \
    --master spark://hadoop-m:7077 \
    /PATH/TO/googlegenomics-spark-examples-assembly-1.0.jar \
    --client-secrets /PATH/TO/YOUR/client_secrets.json \
    --variant-set-id 10473108253681171589 3049512673186936334 \
    --references chr17:41196311:41277499 \
    --output-path gs://YOUR-BUCKET/output/two-way-brca1-pca.tsv

The above command line runs the job over a small portion of the genome, only taking a few minutes.  If modified to run over a larger portion of the genome or the entire genome, it may take a few hours depending upon how many machines are in the Spark cluster.

To run this job over the entire genome:

* Add ``--num-reduce-partitions #`` to be equal to the number of cores in your cluster.
* Use ``--all-references`` instead of ``--references chr17:41196311:41277499`` to run over the entire genome.
* To run the job on a different dataset, change the variant set id for the ``--variant-set-id`` id parameter.

Additional details
------------------

.. include:: ../../includes/spark_details.rst

Gather the results into a single file
-------------------------------------

.. code-block:: shell

  gsutil cat gs://YOUR-BUCKET/output/two-way-brca1-pca.tsv* \
    | sort > two-way-brca1-pca.tsv

