# Use one of the smaller Alpine based distros with Java 8
FROM frolvlad/alpine-oraclejdk8:slim
MAINTAINER Greg Feigenson <greg.feigenson@8x8.com>

ENV ES_VERSION=2.4.3 ES_HOME=/usr/share/elasticsearch
ENV PATH=${PATH}:${ES_HOME}/bin

RUN apk update && apk upgrade && apk add --no-cache ca-certificates curl bash su-exec && \
    curl -Ls https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-$ES_VERSION.tar.gz | tar -xz -C /usr/share && \
    ln -s /usr/share/elasticsearch-$ES_VERSION $ES_HOME && \

    $ES_HOME/bin/plugin install delete-by-query && \
    # ElasticSearch won't actually run as root, so add a user for later.
    addgroup -S elasticsearch && \
    adduser -S -G elasticsearch elasticsearch && \

    # Clean up after ourselves...
    rm -rf /tmp/* /var/cache/apk/* && apk del ca-certificates && \

    # Create some of the directories we'll need later.
    set -ex \
        && for path in \
           $ES_HOME/data \
           $ES_HOME/logs \
           $ES_HOME/config \
           $ES_HOME/config/scripts \
           ; do \
             mkdir -p "$path"; \
           done && \

    chown -R 1000060000: $ES_HOME && \
    chown -R 1000060000: /usr/share/elasticsearch-$ES_VERSION

# Copy our local resources into the container.
COPY config $ES_HOME/config
COPY docker-entrypoint.sh /

# Set up a data volume
VOLUME ["$ES_HOME/data"]

# Expose the ports we care about
EXPOSE 9200 9300

USER 1000060000

# Get ready to run
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["elasticsearch", "-Des.network.host=0.0.0.0"]
