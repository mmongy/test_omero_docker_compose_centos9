FROM centos:centos7.9.2009@sha256:dead07b4d8ed7e29e98de0f4504d87e8880d4347859d839686a31da35a3b532f
LABEL maintainer="ome-devel@lists.openmicroscopy.org.uk"
LABEL org.opencontainers.image.created="unknown"
LABEL org.opencontainers.image.revision="unknown"
LABEL org.opencontainers.image.source="https://github.com/openmicroscopy/omero-server-docker"

RUN mkdir /opt/setup
WORKDIR /opt/setup
ADD ./omero-server-docker-master/playbook.yml ./omero-server-docker-master/requirements.yml /opt/setup/

RUN yum -y install epel-release \
    && yum -y install ansible sudo ca-certificates \
    && ansible-galaxy install -p /opt/setup/roles -r requirements.yml \
    && yum -y clean all \
    && rm -fr /var/cache

ARG OMERO_VERSION=5.6.6
ARG OMEGO_ADDITIONAL_ARGS=
#ENV OMERO_HOME=/opt/omero/server
ENV OMERODIR=/opt/omero/server/OMERO.server/
ENV PATH=/opt/omero/server/venv3/bin:$PATH
ENV PYTHONPATH=/opt/omero/server/venv3/bin:$PYTHONPATH
ENV OMERO_DATA_DIR=/OMERO
ENV OMERO_DB_NAME=database
ENV ICE_HOME=/opt/ice-3.6.5
ENV PATH="$ICE_HOME/bin:$PATH"
ENV LD_LIBRARY_PATH="$ICE_HOME/lib64:$ICE_HOME/lib:$LD_LIBRARY_PATH"
ENV DYLD_LIBRARY_PATH="$ICE_HOME/lib64:$ICE_HOME/lib:$DYLD_LIBRARY_PATH"
ENV SLICEPATH="$ICE_HOME/slice"
RUN ansible-playbook playbook.yml \
    -e omero_server_release=$OMERO_VERSION \
    -e omero_server_omego_additional_args="$OMEGO_ADDITIONAL_ARGS" \
    && yum -y clean all \
    && rm -fr /var/cache

RUN curl -L -o /usr/local/bin/dumb-init \
    https://github.com/Yelp/dumb-init/releases/download/v1.2.2/dumb-init_1.2.2_amd64 && \
    chmod +x /usr/local/bin/dumb-init
ADD ./omero-server-docker-master/entrypoint.sh /usr/local/bin/
ADD ./omero-server-docker-master/50-config.py ./omero-server-docker-master/60-database.sh ./omero-server-docker-master/99-run.sh /startup/

RUN chmod -R 777 /opt/omero/server
RUN ls -al /opt/omero/server
USER omero-server
EXPOSE 4063 4064
VOLUME ["/OMERO", "/opt/omero/server/OMERO.server/var"]
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
