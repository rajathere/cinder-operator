# Debugging Cinder Services

Debugging services is a really important aspect of fixing issues with
minimal downtime. It is really useful when trying to find and
fix issues if something breaks in the deployment. We will be using
``oc debug`` command for debugging Cinder pods. You can find more info
about ``oc debug`` [here](https://www.redhat.com/sysadmin/how-oc-debug-works)
and about ``kubectl debug``
[here](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_debug/).

## Cinder API

##### NOTE: Make sure that ``cinder-api`` statefulset has one replica count.

To start debugging the Cinder API service, we will first list it's
statefulset.

``oc get statefulset -l component=cinder-api``

Start the debug pod and keep labels to notify loadbalancer to redirect requests

``oc debug statefulset/cinder-api --keep-labels``

Unlabel the original cinder-api pod to stop it from being selected by
loadbalancer

``oc label pod cinder-api-0 component-``

``oc label pod cinder-api-0 service-``

#### REVERT

After debug pod is removed (exited), label the cinder-api service pod again

``oc label pods cinder-api-0 compoent=cinder-api``

``oc label pods cinder-api-0 service=cinder``

## Cinder Scheduler

To debug the Cinder Scheduler service, you need to first start the debug pod

``oc debug statefulset/cinder-scheduler``

Set the replicas of cinderScheduler pod to 0.

``oc edit oscp``

```
cinderScheduler:
  replicas: 0
```

#### REVERT

After exiting the debug pod, set the replicas again to 1.

```
cinderScheduler:
  replicas: 1
```

## Cinder Volume

To debug the Cinder Volume service, you need to first start the debug pod

``oc debug statefulset/cinder-volume-ceph``

Set the replicas of cinderVolume pod to 0.

``oc edit oscp``

```
cinderVolume:
  ceph:
    replicas: 0
```

#### REVERT

After exiting the debug pod, set the replicas again to 1.

```
cinderVolume:
  ceph:
    replicas: 1
```

## Cinder Backup

To debug the Cinder Backup service, you need to first start the debug pod

``oc debug statefulset/cinder-backup``

Set the replicas of cinderBackup pod to 0.

``oc edit oscp``

```
cinderBackup:
  replicas: 0
```

#### REVERT

After exiting the debug pod, set the replicas again to 1.

```
cinderBackup:
  replicas: 1
```
