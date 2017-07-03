# lizardfs-flexvolume

This Kubernetes FlexVolume driver aims to provide an implementation for provisioning LizardFS volumes to a Kubernetes POD resource.

# Installation

FlexVolumes drivers are pretty straightforward to install :

```bash
cp lizardfs /usr/libexec/kubernetes/kubelet-plugins/volume/exec/lowet84~lizardfs/
chmod +x /usr/libexec/kubernetes/kubelet-plugins/volume/exec/lowet84~lizardfs/lizardfs
```

Then proceed to restart the Kubelet service

Obviously you'd need to have a functional running LizardFS cluster reachable on <mfsmaster>:<port>.

## Kubernetes 1.6.X specific instructions
Beware, if you're running the 1.6.X branch of Kubernetes, FlexVolume drivers without an implementation of attach/detach/waitforattach (like this one, or any other driver using a networked file system) will fail

https://github.com/kubernetes/kubernetes/commit/d021db82045af0e55200a943b7db7fa71c558d2e

Either you should :
  - Wait for the next Kubernetes official release
  - Compile Kubernetes against master (you shouldn't for production purposes)
  - Disable __enable-controller-attach-detach__ on Kubelet startup : __--enable-controller-attach-detach=false__
  
Please follow last advice only if not running any other driver (out-of-tree or Kubernetes Integrated) needing this feature (FC, iSCSI..)
  
  
# TO DO :
