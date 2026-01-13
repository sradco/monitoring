# KubeVirtNoAvailableNodesToRunVMs

## Meaning

The `KubeVirtNoAvailableNodesToRunVMs` alert is triggered when all nodes in the
Kubernetes cluster are missing hardware virtualization or CPU virtualization
extensions. This means that the cluster does not have the necessary hardware
support to run virtual machines (VMs).

## Impact

If this alert is triggered, it means that VMs will not be able to run on the
cluster. This can have a significant impact on the operations of the cluster, as
VMs may be used for critical applications or services.

## Diagnosis

To diagnose the cause of this alert, the following steps can be taken:

1. Check the hardware configuration of the nodes in the cluster. Make sure that
all nodes have hardware virtualization or CPU virtualization extensions
enabled.

2. Check the node labels in the cluster. Make sure that nodes with the necessary
hardware support are labeled as such, so that VMs can be scheduled to run on
these nodes.

3. Verify which nodes advertise KVM device support:

   ```bash
   # Tabular view: node name and KVM allocatable value
   kubectl get nodes -o go-template='{{range .items}}{{.metadata.name}}{{"\t"}}{{index .status.allocatable "devices.kubevirt.io/kvm"}}{{"\n"}}{{end}}'
   ```

   Interpretation:
   - A positive value (for example, `1k`) means the node advertises KVM support and can run VMs with hardware virtualization.
   - `0` means the node does not advertise KVM support and cannot run such VMs (unless software emulation is explicitly enabled).
   - This alert triggers only when all nodes report `0` for `devices.kubevirt.io/kvm`.

## Mitigation

To mitigate the impact of this alert, add nodes to the cluster that have
hardware virtualization or CPU virtualization extensions enabled.

- Validate hardware virtualization on hosts and ensure KVM is enabled. See KubeVirt docs: [Validate hardware virtualization support](https://kubevirt.io/user-guide/operations/installation/#validate-hardware-virtualization-support).

- If you must run temporarily without hardware support, KubeVirt supports a cluster-wide software emulation fallback. Check whether it is enabled:

  ```bash
  NS=$(kubectl get hyperconverged -A -o jsonpath='{.items[0].metadata.namespace}')
  kubectl get kubevirt -n "$NS" kubevirt-kubevirt-hyperconverged \
    -o jsonpath='{.spec.configuration.developerConfiguration.useEmulation}'; echo
  ```

  Refer to the KubeVirt documentation for details and caveats: [Software emulation](https://github.com/kubevirt/kubevirt/blob/main/docs/software-emulation.md). Note: in HCO-managed clusters this setting is controlled by the operator and is generally not recommended for production.

<!--DS: If you cannot resolve the issue, log in to the
link:https://access.redhat.com[Customer Portal] and open a support case,
attaching the artifacts gathered during the diagnosis procedure.-->
<!--USstart-->
If you cannot resolve the issue, see the following resources:

- [OKD Help](https://okd.io/docs/community/help/)
- [#virtualization Slack channel](https://kubernetes.slack.com/channels/virtualization)
<!--USend-->
