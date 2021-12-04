# ISTIO Blue-Green (Red-Black 🔴⚫️) Test - Deployment

## Overview

This is a test for ISTIO with blue-green (red-black) deployment.

## Deployment

`paintme` is a fake application that is ready to be deployed using a [helm chart](../deployment/helm/paintme). Values for the release can be set before first deployment and for future upgrades. Helm will take care of updating those kubernetes manifests that are affected by the changed variables.

[Helmfile](https://github.com/roboll/helmfile) is used to manage helm chart deployments on a given environment. In this example, `paintme` application is deployed as 2 different and independent releases:

- [paintme-black](../deployment/helmfile/config/paintme-black)
- [paintme-red](../deployment/helmfile/config/paintme-red)
- [paintme-traffic](../deployment/helmfile/config/paintme-traffic)

> Traffic `weight` is managed with `paintme-traffic` released.

### Requirements

Binaries:

- helm and the following plugins
  - helm-diff
- helmfile

[OCP project created and with service mesh enabled](./infrastructure.md):

- paintme

### Steps

Deploy `paintme` application with helmfile

```bash
helmfile -f deployment/helmfile/helmfile.yaml apply
```

Obtain the Gateway URL

```bash
export GATEWAY_URL=$(oc -n istio-system get route istio-ingressgateway -o jsonpath='{.spec.host}')
```

### TODO

- [ ] Move chart to a repository
- [ ] Manage application versioning
- [ ] `paintme-traffic` can be a generic helm chart

### Draft - Tekton pipeline

Steps I would present:

- Get environment variables
- Ensure we are pointing to the right environment
- Deploy it!
- Test
