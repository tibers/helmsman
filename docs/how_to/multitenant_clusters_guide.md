---
version: v1.2.0-rc
---

# Multitenant Clusters Guide

This guide helps you use Helmsman to secure your Helm deployment with service accounts and TLS. 

>Checkout Helm's [security guide](https://github.com/kubernetes/helm/blob/master/docs/securing_installation.md)

> These features are available starting from v1.2.0-rc

## Deploying Tiller in multiple namespaces

In a multitenant cluster, it is a good idea to separate the Helm work of different users. You can achieve that by deploying Tiller in multiple namespaces. This is done in the `namespaces` section using the `installTiller` flag:

```toml

[namespaces]
    [namespaces.staging]
    installTiller = true
    [namespaces.production]
    installTiller = true
    [namespaces.developer1]
    installTiller = true
    [namespaces.developer2]
    installTiller = true

```

## Deploying Tiller with a service account 

You can also deploy each of the Tillers with a different k8s service account Or with a default service account of your choice. 

```toml

[settings]
# other options
serviceAccount = "default-tiller-sa"

[namespaces]
    [namespaces.staging]
    installTiller = true
    tillerServiceAccount = "custom-sa"

    [namespaces.production]
    installTiller = true
    
    [namespaces.developer1]
    installTiller = true
    tillerServiceAccount = "dev1-sa"

    [namespaces.developer2]
    installTiller = true
    tillerServiceAccount = "dev2-sa"

```

In the example above, namespaces `staging, developer1 & developer2` will have Tiller deployed with different service accounts. 
The `production` namespace ,however, will be deployed using the `default-tiller-sa` service account defined in the `settings` section. If this one is not defined, the production namespace Tiller will be deployed with k8s default service account.

## Deploying Tiller with TLS enabled

In a multitenant setting, it is also recommended to deploy Tiller with TLS enabled. This is also done in the `namespaces` section:

```toml

[namespaces]
    [namespaces.kube-system]
    installTiller = false # has no effect. Tiller is always deployed in kube-system 
    caCert = "secrets/kube-system/ca.cert.pem"
    tillerCert = "secrets/kube-system/tiller.cert.pem"
    tillerKey = "$TILLER_KEY" # where TILLER_KEY=secrets/kube-system/tiller.key.pem
    clientCert = "gs://mybucket/mydir/helm.cert.pem"
    clientKey = "s3://mybucket/mydir/helm.key.pem"

    [namespaces.staging]
    installTiller = true

    [namespaces.production]
    installTiller = true
    tillerServiceAccount = "tiller-production"
    caCert = "secrets/ca.cert.pem"
    tillerCert = "secrets/tiller.cert.pem"
    tillerKey = "$TILLER_KEY" # where TILLER_KEY=secrets/tiller.key.pem
    clientCert = "gs://mybucket/mydir/helm.cert.pem"
    clientKey = "s3://mybucket/mydir/helm.key.pem"

```



