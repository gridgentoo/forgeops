# Config Saver - instructions for running AM + the config server.


This is the sequence to get AM running with the config saver + sidecar

```
bin/cdk install base --fqdn foo.iam.forgeops.com
bin/cdk install ds
# Start the config server
kubectl apply -k kustomize/base/config-server
# Start AM
kubectl apply -k kustomize/base/am-cdk

```

Note - requires a special AM image that Jay is working on.
https://stash.forgerock.org/projects/OPENAM/repos/openam/pull-requests/14982/overview



Log on to AM, and make some configuration changes.

Tail the config client sidecar and the server to see updates being made.

IDM
- Still a WIP
-