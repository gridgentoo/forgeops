ARG AMSTER_REPO=gcr.io/forgerock-io/amster/pit1
ARG AMSTER_TAG=7.0.0-latest-postcommit
FROM $AMSTER_REPO:$AMSTER_TAG

USER root
RUN apt-get install -y openldap-utils jq
USER forgerock

ENV SERVER_URI /am

COPY --chown=forgerock:root . /opt/amster

ENTRYPOINT [ "/opt/amster/docker-entrypoint.sh" ]
