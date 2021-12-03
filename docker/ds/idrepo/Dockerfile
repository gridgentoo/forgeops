FROM gcr.io/forgerock-io/ds/pit1:7.2.0-latest-postcommit

COPY debian-buster-sources.list /etc/apt/sources.list

# If you want to update any o/s software, or require root access, uncomment the lines below
# Ensure that the /opt/opendj is owned by forgeorock:root
# USER root
# RUN chown -R forgerock:root /opt/opendj
# USER forgerock
WORKDIR  /opt/opendj

COPY --chown=forgerock:root common  /opt/opendj/
COPY --chown=forgerock:root idrepo  /opt/opendj/
COPY --chown=forgerock:root scripts /opt/opendj/scripts
COPY --chown=forgerock:root uma /opt/opendj/uma

# Ldif for the alpha/bravo realm config
COPY --chown=forgerock:root idrepo/*.ldif  /var/tmp/


# The ds deployment uses PEM based certificates. This sets the location of the certs.
# This is set at docker *build* time. If you change this at runtime you must edit the config.ldif.
ENV PEM_KEYS_DIRECTORY "/var/run/secrets/keys/ds"
ENV PEM_TRUSTSTORE_DIRECTORY "/var/run/secrets/keys/truststore"

# These are the default locations of cert-manager generated PEM files are mounted.
# These files must be copied to appropriate location and format expected by the DS PEM manager
# TO change these location you must also change the ds-setup.sh script.
ENV SSL_CERT_DIR "/var/run/secrets/ds-ssl-keypair"
ENV MASTER_CERT_DIR  "/var/run/secrets/ds-master-keypair"
# This is not currently needed as we use the truststore from the ssl-keypair above

# ENV TRUSTSTORE_DIR "/var/run/secrets/truststore"

# This primes the idrepo datastore with the required base policy entries in ou=am-config
# AM does not yet dynamically create the realm policy entries when it attaches to a new dynamic config
# store - as is the case with File Based Config.
# This procedure concatenates the profiles for the base config store, the base external datastore and
# The alpha/bravo realms.
# This work was previously performed at runtime by the ldif loader. This moves it to a docker build time
# activity. The profiles will create the prototype configs which are used to prime the PVC.
#
# This can be removed once AM can create these itself. See https://bugster.forgerock.org/jira/browse/AME-19010
RUN chmod +w  template/setup-profiles/AM/config/6.5/base-entries.ldif \
        && cat scripts/external-am-datastore.ldif >> template/setup-profiles/AM/config/6.5/base-entries.ldif \
        && cat uma/opendj_uma_audit.ldif >> template/setup-profiles/AM/config/6.5/base-entries.ldif \
        && cat uma/opendj_uma_pending_requests.ldif >> template/setup-profiles/AM/config/6.5/base-entries.ldif \
        && cat uma/opendj_uma_resource_set_labels.ldif >> template/setup-profiles/AM/config/6.5/base-entries.ldif \
        && cat uma/opendj_uma_resource_sets.ldif >> template/setup-profiles/AM/config/6.5/base-entries.ldif \
        && cat /var/tmp/alpha_bravo.ldif >> template/setup-profiles/AM/config/6.5/base-entries.ldif \
        && chmod +w template/setup-profiles/AM/identity-store/7.0/base-entries.ldif \
        && cat /var/tmp/orgs.ldif >> template/setup-profiles/AM/identity-store/7.0/base-entries.ldif \
        && rm /var/tmp/*ldif

RUN bin/setup.sh && \
    bin/relax-security-settings.sh  && \
    rm bin/setup.sh bin/relax-security-settings.sh
