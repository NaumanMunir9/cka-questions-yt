# Question 6

- *Task weight: 8%*

Use context: `kubectl config use-context k8s-c1-H`

Create a new PersistentVolume named `safari-pv`. It should have a capacity of `2Gi`, accessMode `ReadWriteOnce`, hostPath `/Volumes/Data` and no storageClassName defined.
 
Next, create a new PersistentVolumeClaim in Namespace `project-tiger` name `safari-pvc`. It should request `2Gi` storage, accessMode `ReadWriteOnce` and should not define a storageClassName. The PVC should bound to the PV correctly.

Finally, create a new Deployment `safari` in Namespace `project-tiger` which mount that volume at `/tmp/safari-data`. The Pods of that deployment should be of image `httpd:2.4.41-alpine`.

---
