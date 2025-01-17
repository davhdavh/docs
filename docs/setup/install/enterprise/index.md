---
title: Install Stash Enterprise Edition
description: Installation guide for Stash Enterprise edition
menu:
  docs_{{ .version }}:
    identifier: install-stash-enterprise
    name: Enterprise Edition
    parent: installation-guide
    weight: 20
product_name: stash
menu_name: docs_{{ .version }}
section_menu_id: setup
---

# Install Stash Enterprise Edition

Stash Enterprise edition is the open core version of [Stash](https://github.com/stashed/stash). It comes with all the functionalities of Stash Community edition as well as some advanced features such as [Database Backup](/docs/guides/addons/overview/index.md), [Auto-Backup](/docs/guides/auto-backup/overview/index.md), [Batch Backup](/docs/guides/batch-backup/overview/index.md), and [Local Backend](/docs/guides/backends/local/index.md) support, etc. A full features comparison between Stash Enterprise Edition and community version can be found [here](/docs/concepts/what-is-stash/overview/index.md).

If you are willing to try Stash Enterprise Edition, you can grab a **30 days trial** license from [here](https://license-issuer.appscode.com/?p=stash-enterprise).

## Prerequisites

- **Kubernetes version**: Stash is compatible with any Kubernetes cluster with version `1.16` or later.
- **Extended API server**: Your cluster needs to support Kubernetes extended API server.
- **Webhook support**: Your cluster must support Kubernetes validation and mutation webhooks.
- **RBAC permissions**: Stash operator needs a few RBAC permissions on your cluster. You can find the list of the required RBAC permissions [here](/docs/guides/security/rbac/index.md).
- **Installing on GKE cluster**: To install Stash on your GKE cluster, please check the requirements [here](https://stash.run/docs/{{< param "info.version" >}}/setup/install/troubleshoting/#installing-in-gke-cluster).
- **NFS volume**: If you are willing to use NFS volume as a backend, you need to customize the Stash installation like [here](https://stash.run/docs/{{< param "info.version" >}}/setup/install/troubleshoting/#configuring-network-volume-accessor).

## Get a Trial License

In this section, we are going to show you how you can get a **30 days trial** license for Stash Enterprise edition. You can get a license for your Kubernetes cluster by going through the following steps:

- At first, go to [AppsCode License Server](https://license-issuer.appscode.com/?p=stash-enterprise) and fill up the form. It will ask for your Name, Email, the product you want to install, and your cluster ID (UID of the `kube-system` namespace).
- Provide your name and email address. **You must provide your work email address**.
- Then, select `Stash Enterprise Edition` in the product field.
- Now, provide your cluster ID. You can get your cluster ID easily by running the following command:

```bash
kubectl get ns kube-system -o=jsonpath='{.metadata.uid}'
```

- Then, you have to agree with the terms and conditions. We recommend reading it before checking the box.
- Now, you can submit the form. After you submit the form, the AppsCode License server will send an email to the provided email address with a link to your license file.
- Navigate to the provided link and save the license into a file. Here, we save the license to a `license.txt` file.

Here is a screenshot of the license form.

<figure align="center">
  <img alt="Stash Backend Overview" src="images/enterprise_license_form.png">
  <figcaption align="center">Fig: Stash License Form</figcaption>
</figure>

You can create licenses for as many clusters as you want. You can upgrade your license any time without re-installing Stash by following the upgrading guide from [here](/docs/setup/upgrade/index.md#upgrading-license).

>Stash licensing process has been designed to work with CI/CD workflow. You can automatically obtain a license from your CI/CD pipeline by following the guide from [here](https://github.com/appscode/offline-license-server#offline-license-server).

## Get an Enterprise License

If you are interested in purchasing Enterprise license, please contact us via sales@appscode.com for further discussion. You can also set up a meeting via our [calendly link](https://calendly.com/appscode/intro).

If you are willing to purchasing Enterprise license but need more time to test in your dev cluster, feel free to contact sales@appscode.com. We will be happy to extend your trial period.

## Install

Stash operator can be installed as a Helm chart or simply as Kubernetes manifests.

<ul class="nav nav-tabs" id="installerTab" role="tablist">
  <li class="nav-item">
    <a class="nav-link active" id="helm3-tab" data-toggle="tab" href="#helm3" role="tab" aria-controls="helm3" aria-selected="true">Helm 3 (Recommended)</a>
  </li>
  <li class="nav-item">
    <a class="nav-link" id="script-tab" data-toggle="tab" href="#script" role="tab" aria-controls="script" aria-selected="false">YAML</a>
  </li>
</ul>
<div class="tab-content" id="installerTabContent">
  <div class="tab-pane fade show active" id="helm3" role="tabpanel" aria-labelledby="helm3-tab">

## Using Helm 3

Stash can be installed via [Helm](https://helm.sh/) using the [chart](https://github.com/stashed/installer/tree/{{< param "info.installer" >}}/charts/stash) from [AppsCode Charts Repository](https://github.com/appscode/charts). To install the chart with the release name `stash`:

```bash
$ helm repo add appscode https://charts.appscode.com/stable/
$ helm repo update
$ helm search repo appscode/stash --version {{< param "info.version" >}}
NAME                       CHART VERSION         APP VERSION         DESCRIPTION
appscode/stash  {{< param "info.version" >}}    {{< param "info.version" >}}  Stash by AppsCode - Backup your Kubernetes native applications

$ helm install stash appscode/stash             \
  --version {{< param "info.version" >}}                  \
  --namespace kube-system                       \
  --set features.enterprise=true                \
  --set-file global.license=/path/to/the/license.txt
```

To see the detailed configuration options, visit [here](https://github.com/stashed/installer/tree/{{< param "info.installer" >}}/charts/stash-enterprise).

</div>
<div class="tab-pane fade" id="script" role="tabpanel" aria-labelledby="script-tab">

## Using YAML

If you prefer to not use Helm, you can generate YAMLs from Stash chart and deploy using `kubectl`. Here we are going to show the prodecure using Helm 3.

```bash
$ helm repo add appscode https://charts.appscode.com/stable/
$ helm repo update
$ helm search repo appscode/stash --version {{< param "info.version" >}}
NAME                       CHART VERSION         APP VERSION         DESCRIPTION
appscode/stash  {{< param "info.version" >}}    {{< param "info.version" >}}  Stash by AppsCode - Backup your Kubernetes native applications

$ helm template stash appscode/stash     \
  --version {{< param "info.version" >}}                     \
  --namespace kube-system                \
  --set features.enterprise=true         \
  --set global.skipCleaner=true          \
  --set-file global.license=/path/to/the/license.txt | kubectl apply -f -
```

To see the detailed configuration options, visit [here](https://github.com/stashed/installer/tree/{{< param "info.installer" >}}/charts/stash-enterprise).

</div>
</div>

## Verify installation

To check if Stash operator pods have started, run the following command:

```bash
❯ kubectl get pods --all-namespaces -l app.kubernetes.io/name=stash-enterprise --watch
NAMESPACE     NAME                                      READY   STATUS    RESTARTS   AGE
kube-system   stash-stash-enterprise-678bcb6db4-267vk   2/2     Running   0          3m
```

Once the operator pod is running, you can cancel the above command by typing `Ctrl+C`.

Now, to confirm CRD groups have been registered by the operator, run the following command:
```bash
$ kubectl get crd -l app.kubernetes.io/name=stash

NAME                                      CREATED AT
backupbatches.stash.appscode.com          2020-08-24T08:20:54Z
backupblueprints.stash.appscode.com       2020-08-24T08:20:55Z
backupconfigurations.stash.appscode.com   2020-08-24T08:20:54Z
backupsessions.stash.appscode.com         2020-08-24T08:20:55Z
functions.stash.appscode.com              2020-08-24T08:20:55Z
recoveries.stash.appscode.com             2020-08-24T08:20:54Z
repositories.stash.appscode.com           2020-08-24T08:20:54Z
restics.stash.appscode.com                2020-08-24T08:20:54Z
restorebatches.stash.appscode.com         2020-08-24T08:20:55Z
restoresessions.stash.appscode.com        2020-08-24T08:20:55Z
tasks.stash.appscode.com                  2020-08-24T08:20:55Z
```

### Verify Catalogs

Stash Enterprise edition automatically installs the necessary Addon catalogs for database backup. Verify that the Addon catalogs have been installed using the following command.

```bash
❯ kubectl get tasks.stash.appscode.com
NAME                          AGE
elasticsearch-backup-5.6.4    5m8s
elasticsearch-backup-6.2.4    5m8s
elasticsearch-backup-6.3.0    5m8s
elasticsearch-backup-6.4.0    5m8s
elasticsearch-backup-6.5.3    5m8s
elasticsearch-backup-6.8.0    5m8s
elasticsearch-backup-7.2.0    5m8s
elasticsearch-backup-7.3.2    5m8s
elasticsearch-restore-5.6.4   5m8s
elasticsearch-restore-6.2.4   5m8s
elasticsearch-restore-6.3.0   5m8s
elasticsearch-restore-6.4.0   5m8s
elasticsearch-restore-6.5.3   5m8s
elasticsearch-restore-6.8.0   5m8s
elasticsearch-restore-7.2.0   5m8s
elasticsearch-restore-7.3.2   5m8s
mariadb-backup-10.5.8         5m8s
mariadb-restore-10.5.8        5m8s
mongodb-backup-3.4.17         5m8s
mongodb-backup-3.4.22         5m8s
mongodb-backup-3.6.13         5m8s
mongodb-backup-4.0.11         5m8s
mongodb-backup-4.0.3          5m8s
mongodb-backup-4.1.13         5m8s
mongodb-backup-4.1.4          5m8s
mongodb-backup-4.1.7          5m8s
mongodb-backup-4.2.3          5m8s
mongodb-restore-3.4.17        5m8s
mongodb-restore-3.4.22        5m8s
mongodb-restore-3.6.13        5m8s
mongodb-restore-4.0.11        5m8s
mongodb-restore-4.0.3         5m8s
mongodb-restore-4.1.13        5m8s
mongodb-restore-4.1.4         5m8s
mongodb-restore-4.1.7         5m8s
mongodb-restore-4.2.3         5m8s
mysql-backup-5.7.25           5m8s
mysql-backup-8.0.14           5m8s
mysql-backup-8.0.21           5m8s
mysql-backup-8.0.3            5m8s
mysql-restore-5.7.25          5m8s
mysql-restore-8.0.14          5m8s
mysql-restore-8.0.21          5m8s
mysql-restore-8.0.3           5m8s
perconaxtradb-backup-5.7      5m8s
perconaxtradb-restore-5.7     5m8s
postgres-backup-10.14         5m8s
postgres-backup-11.9          5m8s
postgres-backup-12.4          5m8s
postgres-backup-13.1          5m8s
postgres-backup-9.6.19        5m8s
postgres-restore-10.14        5m8s
postgres-restore-11.9         5m8s
postgres-restore-12.4         5m8s
postgres-restore-13.1         5m8s
postgres-restore-9.6.19       5m8s
pvc-backup                    5m2s
pvc-restore                   5m2s
```

As you can see from the above output that Stash has created `Task` objects for each supported databases.

Now, you are ready to [take your first backup](/docs/guides/README.md) using Stash.
