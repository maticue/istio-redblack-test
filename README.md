# ISTIO Blue-Green (Red-Black 🔴⚫️) Test

## Overview

This is an example for ISTIO with blue-green (red-black 🔴⚫️) deployment using a fake application called `paintme`.
This document explains how to test it and points to other two documents that explains how it can be deployed.

## Usage guide

### Infrastructure

Infrastructure is built following the [infrastructre guide](./documentation/infrastructure.md).

Ensure the infrastructure is available:

```bash
crc start
```

> Wait some minutes ☕️

### Deployment test

Deployment is built following the [deployment guide](./documentation/deployment.md).

Get the Gateway URL

```bash
export GATEWAY_URL=$(oc -n istio-system get route istio-ingressgateway -o jsonpath='{.spec.host}')
echo ${GATEWAY_URL}
```

Open your browser and go to `http://${GATEWAY_URL}/paintme`. You will see either a black, red or blue background color.

Get the Kiali URL

```bash
export KIALI_URL=$(oc -n istio-system get route kiali -o jsonpath='{.spec.host}')
echo ${KIALI_URL}
```

Open another tab on your browser and go to `https://${KIALI_URL}`. Accept the self-signed TLS certificate and login as `kubeadmin` user. Password can be found on `~/.crc/machines/crc/kubeadmin-password`.

If you want to change weights between `black` and `red`, just go to [./deployment/helmfile/config/paintme-traffic/values.yaml.gotmpl](./deployment/helmfile/config/paintme-traffic/values.yaml.gotmpl) and set your prefered values. Then deploy with `helmfile`.

```bash
helmfile -f deployment/helmfile/helmfile.yaml apply
```

## Destroy demo

Destroy the fake application.

```bash
helmfile -f deployment/helmfile/helmfile.yaml delete
```
