---
date: 2017-012-05T00:00:00+00:00
title: kubectl
author: kubeci
tags: [ kubernetes, kubectl ]
repo: kubeciio/kubectl
logo: kubectl.svg
image: kubeciio/kubectl
---

The kubectl plugin can be used to do everything kubectl can do.
It provides some helpers and shortcuts to make your pipeline step more readable.

Example configuration running inside a kubernetes cluster:

```yaml
pipeline:
  kubectl:
    image: kubeciio/kubectl
    kubectl: get pods
```

Example configuration using a kubeconfig via a secret:

This is **required** when running outside a cluster or a different one should be talked to. 

```diff
pipeline:
  kubectl:
    image: kubeciio/kubectl
    kubectl: get pods
+   secrets: [ kubeconfig ]
```

_This takes the secret kubeconfig, base64 decodes it and writes it to disk ready for kubectl._ 

Example configuration using a different kubeconig via a secrets:

```diff
pipeline:
  kubectl:
    image: kubeciio/kubectl
    kubectl: get pods
+   secrets:
+     - source: kubeconfig_development
+       target: kubeconfig
```

_This maps the `kubeconfig_develpment` secret to be used by the plugin as `kubeconfig` which is then forwarded to kubectl._

Example configuration running inside a kubernetes cluster using a different namespace:

```diff
pipeline:
  kubectl:
    image: kubeciio/kubectl
-   kubectl: get pods
+   kubectl: --namespace kubeci get pods
```

equivalent:

```diff
pipeline:
  kubectl:
    image: kubeciio/kubectl
    kubectl: get pods
+   namespace: kubeci
```

Example configuration using file paths to apply:

```diff
pipeline:
  kubectl:
    image: kubeciio/kubectl
+   kubectl: apply -f /path/to/folder/foo.yaml -f /path/to/folder/bar.yaml -f /path/to/folder/baz.yaml
```

equivalent but easier to read:

```diff
pipeline:
  kubectl:
    image: kubeciio/kubectl
-   kubectl: apply -f /path/to/folder/foo.yaml -f /path/to/folder/bar.yaml -f /path/to/folder/baz.yaml
+   kubectl: apply
+   files: 
+     - /path/to/folder/foo.yaml 
+     - /path/to/folder/bar.yaml 
+     - /path/to/folder/baz.yaml
```

### Templating

Templating generate commands or files to be used by kubectl when executing.

Setting the image to the current commit:

```diff
pipeline:
  kubectl:
   image: kubeciio/kubectl
   kubectl: set image deployment/foo container=bar/baz:{{ .DroneCommit }}
```
