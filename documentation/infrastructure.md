# ISTIO Blue-Green (Red-Black 🔴⚫️) Test - Infrastructure

## Overview

This is a guide to prepare the necessary infrastructure for the fake application

> All commands are executed from the root of this repo.

## Infrastructure

### Requirements

[RedHat CodeReady Containers](https://developers.redhat.com/products/codeready-containers/overview) is installed on your machine.

> This guide is based on `OpenShift Container Platform 4.9`.

### OpenShift Container Platform Infrastructure

RedHat Container is configured with 14Gi RAM

```bash
crc config set memory 14336
```

RedHat Container is started

```bash
crc start
```

> Source: [OpenShift documentation](https://docs.openshift.com/container-platform/4.9/service_mesh/v2x/installing-ossm.html#installing-ossm).

Required operators are installed via web console using admin credentials.

```bash
crc console
```

> `kubeadmin` username's credentials are stored on `~/.crc/machines/crc/kubeadmin-password`.

Operators are found on `Operator -> OperatorHub`. The following Operators must be installed in order:

- [Red Hat OpenShift distributed tracing platform v1.28.0](https://operatorhub.io/operator/jaeger/stable/jaeger-operator.v1.25.0)
- [Kiali Operator v1.36.6](https://github.com/kiali/kiali-operator/tree/v1.36.6)
- [Red Hat OpenShift Service Mesh v2.1.0](https://github.com/maistra/istio-operator/tree/maistra-2.1.0)

> All operators are installed with default values: stable versions, available on all namespaces and with automatic upgrades enabled.

Ensure istio is available

```bash
oc new-project istio-system
oc create -n istio-system -f infrastructure/ocp/istio.yaml
```

> Do you like ☕️? This is the right time to go for it.

After a while, mesh service is available

```bash
oc get smcp -n istio-system
NAME    READY   STATUS            PROFILES      VERSION   AGE
basic   8/8     ComponentsReady   ["default"]   2.0.8     32m
```

Unfortunately, it could happen that `kiali` POD is failing. At the moment of writing this article, it was decided to workaround this issue replacing the kiali container image with another from [quay.io registry](https://quay.io/repository/kiali/kiali?tab=tags)

```bash
kubectl -n istio-system patch deployment kiali -p '{ "spec": { "template": { "spec": { "containers": [ { "name": "kiali", "image": "quay.io/kiali/kiali:v1.24"} ] } } } }' -o yaml
```

> Warning: quay.io reports [this image has vulnerabilities](https://quay.io/repository/kiali/kiali/manifest/sha256:23c339e4be50b33942f842d715611435ffd590d116de1d7512a92aaa9590c969?tab=vulnerabilities)

## Paintme project

Service mesh needs to be applied per project.
`paintme` is the name of the project where this application will run.

```bash
oc new-project paintme
```

Service mesh is enabled on the `paintme` project

```bash
oc create -n istio-system -f infrastructure/ocp/servicemeshmemberroll-default.yaml
```

Service mesh member roll now is read

```bash
oc get smmr -n istio-system
NAME      READY   STATUS       AGE
default   1/1     Configured   8m39s
```

### TODO

- [ ] Improve documentation and code references.
- [ ] Automate infrastructure creation and configuration.
- [ ] Reduce resources consumption footprint.
