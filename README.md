# lizardfs-flexvolume

This Kubernetes FlexVolume driver aims to provide an implementation for provisioning LizardFS volumes to a Kubernetes POD resource.

You shouldn't use this driver in production.

# Installation

FlexVolumes drivers are pretty straightforward to install :

```bash
cp lizardfs /usr/libexec/kubernetes/kubelet-plugins/volume/exec/lowet84~lizardfs/
chmod +x /usr/libexec/kubernetes/kubelet-plugins/volume/exec/lowet84~lizardfs/lizardfs
```

Then proceed to restart the Kubelet service

Obviously you'd need to have a functional running LizardFS cluster reachable.

If you'd want to use the provided YML example files in order to test the feature :
```bash
kubectl create -f examples/provision-volumes.yml
```

Three PersistentVolumes should be created plus one PVC, bound to one of your PVs (could be any of those 3) :
```bash
kubectl get pv
NAME           CAPACITY   ACCESSMODES   RECLAIMPOLICY   STATUS      CLAIM                                   STORAGECLASS   REASON    AGE
mfs-volume     100Gi      RWX           Retain          Bound       default/test-1                                                   3h
mfs-volume-1   100Gi      RWX           Retain          Available                                                                    3h
mfs-volume-2   100Gi      RWX           Retain          Available                                                                    3h
```

Then proceed with a basic POD creation using this PVC __test-1__ :

```bash 
kubectl create -f examples/deployment-using-lizardfs.yml
```

## Kubernetes 1.6.X specific instructions
Beware, if you're running the 1.6.X branch of Kubernetes, FlexVolume drivers without an implementation of attach/detach/waitforattach (like this one, or any other driver using a networked file system) will fail

https://github.com/kubernetes/kubernetes/commit/d021db82045af0e55200a943b7db7fa71c558d2e

Either you should :
  - Wait for the next Kubernetes official release
  - Compile Kubernetes against master (you shouldn't for production purposes)
  - Disable __enable-controller-attach-detach__ on Kubelet startup : __--enable-controller-attach-detach=false__
  
Please follow last advice only if not running any other driver (out-of-tree or Kubernetes Integrated) needing this feature (FC, iSCSI..)
  
  
# TO DO :

```bash
        # Okay, I want to provision the folder in the root of my LizardFS : each pod bound to a PVC bound to a PV has a folder created (folder name is the PV name bound to the PVC)
        # Obvisouly you need to have a fuse mountpoint on /mnt pointing to the root of your LizardFS.
        # TO DO : Automated quickmount -> mkdir -> unmount so all the logic is embedded in the flexvolume driver.
        mkdir -p /mnt/$LFSFOLDER
```

This FlexVolume driver tries to make a new directory in the root of your lizardfs, prealably mounted in /mnt. 
The goal of that behavior is to keep a folder structure matching the PersistentVolume names that were bound to the PersistentVolumeClaim.


