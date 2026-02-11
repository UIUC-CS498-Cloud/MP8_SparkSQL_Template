# Fetch ubuntu 22.04 LTS docker image
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV PYSPARK_PYTHON=python3

RUN sed -i 's|http://security.ubuntu.com|http://archive.ubuntu.com|g' /etc/apt/sources.list
RUN apt-get update && \
    apt-get install -y --no-install-recommends build-essential \
        expect \
        git \
        vim \
        zip \
        unzip \
        wget \
        openjdk-21-jdk \
        wget \
        maven \
        sudo \
        curl
RUN apt-get install -y python3 python3-pip

################################################################################
####################   Spark stuff   ###########################################
################################################################################

RUN cd /usr/local/ && \
    wget "https://archive.apache.org/dist/spark/spark-3.5.4/spark-3.5.4-bin-hadoop3.tgz" && \
    tar -xvzf spark-3.5.4-bin-hadoop3.tgz && \
    ln -s ./spark-3.5.4-bin-hadoop3 spark &&  \
    rm -rf /usr/local/spark-3.5.4-bin-hadoop3.tgz && \
    rm -rf /usr/local/spark/external && \
    chmod a+rwx -R /usr/local/spark/

RUN pip3 install --upgrade pip
RUN pip3 install Cython
RUN pip3 install numpy

RUN echo "alias spark-submit='/usr/local/spark/bin/spark-submit'" >> ~/.bashrc

# Ensure spark log output is redirected to stderr
RUN cp /usr/local/spark/conf/log4j2.properties.template /usr/local/spark/conf/log4j2.properties

# Set relevant environment variables to simplify usage of spark
ENV SPARK_HOME /usr/local/spark
ENV PATH="/usr/local/spark/bin:${PATH}"
RUN chmod a+rwx -R /usr/local/spark/

# Test the arch and set JAVA_HOME accordingly:
# For ARM64: /usr/lib/jvm/java-21-openjdk-arm64
# For x86_64: /usr/lib/jvm/java-21-openjdk-amd64
RUN if [ "$(uname -m)" = "x86_64" ]; then \
        export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64; \
    else \
        export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-arm64; \
    fi && \
    echo "export JAVA_HOME=$JAVA_HOME" >> /root/.bashrc

# Use update-alternatives (instead of update-java-alternatives) to set the default java executable.
RUN if [ "$(uname -m)" = "x86_64" ]; then \
        update-alternatives --set java /usr/lib/jvm/java-21-openjdk-amd64/bin/java; \
    else \
        update-alternatives --set java /usr/lib/jvm/java-21-openjdk-arm64/bin/java; \
    fi

# [Optional] Set working path to /cs498, and run the following command to start the container with code:
# WORKDIR /cs498
# docker run -it --rm --mount type=bind,source=$PATH_TO_CODE,target=/cs498/ mp8.v1 /bin/bash
