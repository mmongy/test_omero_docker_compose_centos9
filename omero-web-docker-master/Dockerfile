FROM centos:centos7.9.2009@sha256:dead07b4d8ed7e29e98de0f4504d87e8880d4347859d839686a31da35a3b532f
LABEL maintainer="ome-devel@lists.openmicroscopy.org.uk"


RUN mkdir /opt/setup
WORKDIR /opt/setup
ADD ./omero-web-docker-master/playbook.yml ./omero-web-docker-master/requirements.yml /opt/setup/

RUN yum -y install epel-release \
    && yum -y install ansible sudo \
    && ansible-galaxy install -p /opt/setup/roles -r requirements.yml \
    && yum -y clean all \
    && rm -fr /var/cache

RUN ansible-playbook playbook.yml \
    && yum -y clean all \
    && rm -fr /var/cache

RUN curl -L -o /usr/local/bin/dumb-init \
    https://github.com/Yelp/dumb-init/releases/download/v1.2.0/dumb-init_1.2.0_amd64 && \
    chmod +x /usr/local/bin/dumb-init
ADD ./omero-web-docker-master/entrypoint.sh /usr/local/bin/
ADD ./omero-web-docker-master/50-config.py ./omero-web-docker-master/60-default-web-config.sh ./omero-web-docker-master/98-cleanprevious.sh ./omero-web-docker-master/99-run.sh /startup/
ADD ./omero-web-docker-master/ice.config /opt/omero/web/OMERO.web/etc/

RUN chmod -R 777 /opt/omero/web
USER omero-web
EXPOSE 4080
VOLUME ["/opt/omero/web/OMERO.web/var"]

ENV OMERODIR=/opt/omero/web/OMERO.web/
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
