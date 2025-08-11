## Question

- Create a PersistentVolume named `logs-pv` that maps to the hostPath `/tmp/logs`.The access mode should be `ReadWriteOnce` and `ReadOnlyMany`. Provision a storage capacity of 2Gi. Assign the reclaim policy `Delete` and an empty string as the storage class. Ensure that the status of the PersistentVolume shows Available.
- Create a PersistentVolumeClaim named `logs-pvc`. The access it uses is `ReadWriteOnce`. Request a capacity of 1Gi. Ensure that the status of the PersistentVolume shows Bound.
- Mount the PersistentVolumeClaim in a Pod running the image nginx at the mount path `/var/log/nginx`.
- Open an interactive shell to the container and create a new file named `mynginx` log in /var/log/nginx. Exit out of the Pod.
- Delete the Pod and PersistentVolumeClaim. What happens to the PersistentVolume?
- List the available storage classes and identify the default storage class. Note the provisioner.
- Create a new storage class named `custom` using the provisioner of the default storage class.
- Create a PersistentVolumeClaim named custom-pvc. Request a capacity of 500Mi and declare the access mode `ReadWriteOnce`.
  Assign the storage class name custom.
- The PersistentVolume should have been provisioned dynamically. Find out the name and write it to the file named `pv-name.txt`.
- Delete the PersistentVolumeClaim. What happens to the PersistentVolume?

<details>
<summary> Solution</summary>
Use the kubernetes.io documentation to create a PV. 

```

```
</details>