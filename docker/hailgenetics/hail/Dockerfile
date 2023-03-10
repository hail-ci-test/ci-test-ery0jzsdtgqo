FROM {{ hail_ubuntu_image.image }}

ENV MAKEFLAGS -j2

RUN hail-apt-get-install \
    curl \
    git \
    jq \
    liblapack3 \
    openjdk-8-jre-headless \
    rsync \
    unzip bzip2 zip tar tabix lz4 \
    vim pv

# source: https://cloud.google.com/storage/docs/gsutil_install#linux
RUN curl https://sdk.cloud.google.com | bash && \
    find \
      /root/google-cloud-sdk/bin/ \
      -type f -executable \
    | xargs -I % /bin/sh -c 'set -ex ; ln -s ${PWD}/% /usr/local/bin/$(basename %)'

COPY hail/python/pinned-requirements.txt requirements.txt
RUN hail-pip-install -r requirements.txt \
      ipython \
      matplotlib \
      numpy \
      scikit-learn \
      dill \
      scipy

RUN export SPARK_HOME=$(find_spark_home.py) && \
    curl https://storage.googleapis.com/hadoop-lib/gcs/gcs-connector-hadoop2-2.2.7.jar \
         >$SPARK_HOME/jars/gcs-connector-hadoop2-2.2.7.jar && \
    mkdir -p $SPARK_HOME/conf && \
    touch $SPARK_HOME/conf/spark-defaults.conf && \
    sed -i $SPARK_HOME/conf/spark-defaults.conf \
        -e 's:spark\.hadoop\.google\.cloud\.auth\.service\.account\.enable.*:spark.hadoop.google.cloud.auth.service.account.enable true:' \
        -e 's:spark\.hadoop\.google\.cloud\.auth\.service\.account\.json\.keyfile.*:spark\.hadoop\.google\.cloud\.auth\.service\.account\.json\.keyfile /gsa-key/key.json:'

RUN --mount=src=wheel-container.tar,target=/wheel-container.tar \
    tar -xf wheel-container.tar && \
    hail-pip-install --no-deps hail-*-py3-none-any.whl && \
    rm -rf hail-*-py3-none-any.whl
