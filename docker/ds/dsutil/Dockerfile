ARG DS_REPO=gcr.io/forgerock-io/ds/pit1
ARG DS_TAG=7.0.0
FROM $DS_REPO:$DS_TAG

USER root
RUN apt-get update && apt-get install -y vim
USER forgerock


COPY --chown=forgerock:root bin/*  bin/


ENTRYPOINT ["bash"]
# Just sleep a long time waiting for someone to exec into the pod
CMD [ "-c", "trap : TERM INT; sleep infinity & wait" ]
