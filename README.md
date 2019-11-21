# Kubernetes iSCSI-targetd provisioner helm chart

[iSCSI-targetd provisioner](https://github.com/kubernetes-incubator/external-storage/tree/master/iscsi/targetd)
is an out of tree provisioner for iSCSI storage for Kubernetes.

## Introduction

The provisioner uses the API provided by Open iSCSI [targetd](https://github.com/open-iscsi/targetd)
to create LVM volumes and export those over iSCSI storage from a remote server.

As prerequisites, an existing iSCSI server managed by `targetd` must be available.
To setup one, you can check that [dockerized targetd](https://github.com/lionelnicolas/docker-targetd).

## Installing the Chart

To install the chart with the release name `my-release`:

```sh
$ helm install https://github.com/lionelnicolas/helm-iscsi-targetd-provisioner/archive/master.tar.gz \
	--name my-release \
	--set targetd.address=iscsi-server.example.net
```

Consult [configuration](#configuration) to view the list of all available options.

## Uninstalling the Chart

To prevent LVM volume to remain opened and locked on the server, all attached iSCSI
volumes needs to be detached and deleted before uninstalling the chart:

```sh
$ kubectl get persistentvolumeclaims --all-namespaces
$ kubectl delete --namespace ns1 persistentvolumeclaims pvc-.......
$ kubectl delete --namespace ns1 persistentvolumeclaims pvc-.......

(...)
```

```sh
$ helm delete --purge my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `timezone` | Timezone (use for logging messages) | `America/Toronto` |
| `image.registry` | Docker image registry | `quay.io` |
| `image.repository` | Docker image name | `external_storage/iscsi-controller` |
| `image.tag` | Docker image tag | `v0.0.6` |
| `image.pullPolicy` | Docker image pull policy | `Always` |
| `labels.component` | Value for `app.kubernetes.io/component` label | `iscsi` |
| `labels.partOf` | Value for `app.kubernetes.io/component` label | `storage` |
| `provisioner.name` | Name to use when registering provisioner | `iscsi-targetd` |
| `provisioner.logLevel` | Provisioner log level | `debug` |
| `targetd.scheme` | Scheme to use for the connection | `http` |
| `targetd.address` | HTTP host | `iscsi-target-server.local` |
| `targetd.port` | HTTP port | `18700` |
| `targetd.username` | Targetd username | `admin` |
| `targetd.password` | Targetd password | `admin` |
| `targetd.poolName` | Targetd pool name | `vg-targetd` |
| `storageClass.enabled` | Create a storageClass attached to that provisioner | `false` |
| `storageClass.name` | Name of the storageClass | `iscsi-targetd` |
| `storageClass.fsType` | Filesystem to use when formatting devices | `ext4` |
| `storageClass.readonly` | Readonly device | `false` |
| `storageClass.iscsi.ipAddress` | iSCSI target IP address | `127.0.0.1` |
| `storageClass.iscsi.port` | iSCSI target port | `3260` |
| `storageClass.iscsi.iqn` | iSCSI Qualified Name (IQN) | `iqn.2003-01.org.linux-iscsi.k8s:targetd` |
| `storageClass.iscsi.initiators` | comma separated list of possible iSCSI initiators | `` |

Check [values.yaml](values.yaml) comments for all the possible configuration options.

## License

This is licensed under the Apache License, Version 2.0. Please see [LICENSE](LICENSE)
for the full license text.

Copyright 2019 Lionel Nicolas
