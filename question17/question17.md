## Question

Install the MinIO Operator using Helm in Namespace minio. Then configure and create the Tenant CRD:

- Create namespace minio
- Install Helm chart minio/operator into the new namespace. The Helm Release should be called minio-operator.
- Update the Tenant resource in /opt/course/2/minio-tenant.yaml to include enableSFTP: true under features
- Create the Tenant resource from /opt/course/2/minio-tenant.yaml

Note: It is not required for MinIO to run properly. Installing the Helm Chart and the Tenant resource as requested is enough

## Prerequistes

Add the helm repository for MinIO

```
helm repo add minio https://operator.min.io
```
Create the following file `/opt/course/2/minio-tenant.yaml`

```
apiVersion: minio.min.io/v2
kind: Tenant
metadata:
  name: tenant
  namespace: minio
  labels:
    app: minio
spec:
  features:
    bucketDNS: false
  image: quay.io/minio/minio:latest
  pools:
    - servers: 1
      name: pool-0
      volumesPerServer: 0
      volumeClaimTemplate:
        apiVersion: v1
        kind: persistentvolumeclaims
        metadata: { }
        spec:
          accessModes:
            - ReadWriteOnce
          resources:
            requests:
              storage: 10Mi
          storageClassName: standard
        status: { }
  requestAutoCert: true

```

<details>
<summary> Solution</summary>

Create namespace `minio`

```
kubectl create namespace minio
```
List the helm repositories

```
helm repo list
```

Check the charts in the repo

```
helm search repo minio
```
Install the Helm `minio/operator` into the new namespace

```
heml install -n minio minio-operator minio/operator
```
Edit `/opt/course/2/minio-tenant.yaml` 

```
apiVersion: minio.min.io/v2
kind: Tenant
metadata:
  name: tenant
  namespace: minio
  labels:
    app: minio
spec:
  features:
    bucketDNS: false
    enableSFTP: true #ADD
  image: quay.io/minio/minio:latest
  pools:
    - servers: 1
      name: pool-0
      volumesPerServer: 0
      volumeClaimTemplate:
        apiVersion: v1
        kind: persistentvolumeclaims
        metadata: { }
        spec:
          accessModes:
            - ReadWriteOnce
          resources:
            requests:
              storage: 10Mi
          storageClassName: standard
        status: { }
  requestAutoCert: true

```

```
kubectl apply -f /opt/course/2/minio-tenant.yaml
```

```
kubectl -n minio get tenant
```

</details>

<details>
<summary> Clean Up</summary>
```
helm -n minio uninstall minio-operator
kubectl delete namespace minio
rm --f opt/course/2/minio-tenant.yaml
```
</details>