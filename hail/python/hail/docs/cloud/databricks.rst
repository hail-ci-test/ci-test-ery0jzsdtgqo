
==========
Databricks
==========

The docker images described below are maintained by Databricks. Please direct questions about them
to Databricks.

Hail can be installed on a Databricks Spark cluster on Microsoft Azure, Amazon Web Services, or
Google Cloud Platform via an open source Docker container located in the `Project Glow Dockerhub
<https://hub.docker.com/r/projectglow/databricks-hail/tags?page=1&ordering=last_updated>`__.  Docker
files to build your own Hail container on Databricks can be found in the Glow `Github repository
<https://github.com/projectglow/glow/tree/master/docker>`__.

Install Hail via Docker with `Databricks Container Services
<https://docs.databricks.com/clusters/custom-containers.html>`__.

Use the Docker Image URL, ``projectglow/databricks-hail:<hail_version>``, replacing the tag with an
available Hail version.  Please match the Databricks Runtime Spark version to the Spark version Hail
is built with.

Use Hail in a notebook
----------------------

For the most part, Hail in Databricks works identically to the Hail documentation. However, there
are a few modifications that are necessary for the Databricks environment.

Initialize Hail
---------------

When initializing Hail, pass in the pre-created `SparkContext` and mark the initialization as
idempotent. This setting enables multiple Databricks notebooks to use the same Hail context.

:**note**:

  - Enable ``skip_logging_configuration`` to save logs to the rolling driver log4j output. This
    setting is supported only in Hail 0.2.39 and above.
  - Hail is not supported with `Credential passthrough
    <https://docs.databricks.com/security/credential-passthrough/index.html>`__.

:**code**:

    >>> import hail as hl
    >>> hl.init(sc, idempotent=True, quiet=True, skip_logging_configuration=True)  # doctest: +SKIP

Display Bokeh plots
-------------------

Hail uses the `Bokeh <https://docs.bokeh.org/en/latest/>`__ library to create plots. The `show`
function built into Bokeh does not work in Databricks. To display a Bokeh plot generated by Hail,
you can run a command like:

    >>> from bokeh.embed import components, file_html
    >>> from bokeh.resources import CDN
    >>> plot = hl.plot.histogram(mt.DP, range=(0,30), bins=30, title='DP Histogram', legend='DP')
    >>> html = file_html(plot, CDN, "Chart")

And then call the Databricks function `displayHTML` with `html` as its argument.

See Databricks' `Bokeh docs <https://docs.databricks.com/notebooks/visualizations/bokeh.html>`__ for
more information.

