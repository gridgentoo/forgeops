FROM gcr.io/forgerock-io/ds/pit1:7.2.0-latest-postcommit

USER root

COPY debian-buster-sources.list /etc/apt/sources.list

RUN chown -R forgerock:root /opt/opendj
USER forgerock
COPY --chown=forgerock:root common  /opt/opendj/
COPY --chown=forgerock:root cts     /opt/opendj/
COPY --chown=forgerock:root scripts /opt/opendj/scripts
ARG profile_version


# The ds deployment uses PEM based certificates. This sets the location of the certs.
# This is set at docker *build* time. If you change this at runtime you must edit the config.ldif.
ENV PEM_KEYS_DIRECTORY "/var/run/secrets/keys/ds"
ENV PEM_TRUSTSTORE_DIRECTORY "/var/run/secrets/keys/truststore"

# These are the default locations of cert-manager generated PEM files are mounted.
# These files must be copied to appropriate location and format expected by the DS PEM manager
# TO change these location you must also change the ds-setup.sh script.
ENV SSL_CERT_DIR "/var/run/secrets/ds-ssl-keypair"
ENV MASTER_CERT_DIR  "/var/run/secrets/ds-master-keypair"

RUN bin/setup.sh && \
    bin/relax-security-settings.sh  && \
    rm bin/setup.sh bin/relax-security-settings.sh
