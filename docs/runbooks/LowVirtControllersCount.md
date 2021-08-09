# LowVirtControllersCount

## Meaning

More than one virt-controller should be ready if we are running in high-availability(Usually we run 2 replicas)

## Impact
This impacts availability of the component. In case of 1 active replica out of two we don’t have additional availability in case of crash.
This alert becomes severe if VirtControllerDown alert appears.


## Diagnosis
Check to see if there are any running virt-controller pods:
```
kubectl -n kubevirt get pods -l kubevirt.io=virt-controller
```

Further check not ready or crashing virt-controller`s pods:
```
kubectl -n kubevirt logs <pod-name>
```
```
kubectl -n kubevirt describe pod/<pod-name>
```

## Mitigation

There can be several reasons, identify the root cause and fix it.