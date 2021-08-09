# VirtControllerDown

## Meaning
No running virt-controller was detected for the last 5 min. Virt-controller deployment has a default replica of 2 pods. 

## Impact
Complete failure in VM lifecycle management i.e. launching a new VM instance or shutting down an existing VM instance.

## Diagnosis
Observe the status of the virt-controller deployment
```
kubectl get deployment -n kubevirt virt-controller -oyaml
```
Observe the logs of the kubecontroller manager pod, to see why it cannot create the virt-controller pods.

## Mitigation
Node resource exhaustion may be the root cause of this event.