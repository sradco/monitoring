# KubeVirtCRModified

## Meaning

This alert fires when an operand of the HyperConverged Cluster Operator (HCO) is
changed by someone or something other than HCO.

HCO configures KubeVirt and its supporting operators in an opinionated way and
overwrites its operands when there is an unexpected change to them. Users must
not modify the operands directly. The `HyperConverged` custom resource is the
source of truth for the configuration.

## Impact

Changing the operands manually causes the cluster configuration to fluctuate and
might lead to instability.

## Diagnosis

- Check the `component_name` value in the alert details to determine the operand
  kind (`kubevirt`) and the operand name (`kubevirt-kubevirt-hyperconverged`)
  that are being changed:

  ```text
  Labels
    alertname=KubeVirtCRModified
    component_name=kubevirt/kubevirt-kubevirt-hyperconverged
    severity=warning
  ```

Inspect the current managed KubeVirt operand and the HCO `HyperConverged` source of truth:

  ```bash
  # Discover the HCO namespace (commonly 'openshift-cnv' or 'kubevirt-hyperconverged')
  NS=$(kubectl get hyperconverged -A -o jsonpath='{.items[0].metadata.namespace}')

  # Managed KubeVirt CR (operand managed by HCO)
  kubectl get kubevirt -n "${NS}" kubevirt-kubevirt-hyperconverged -o yaml

  # HCO configuration (edit this to reconcile desired state)
  kubectl get hyperconverged -n "${NS}" kubevirt-hyperconverged -o yaml
  ```

## Mitigation

Do not change the HCO operands directly. Use `HyperConverged` objects to
configure the cluster.

The alert resolves itself after 10 minutes if the operands are not changed
manually.

To reconcile unexpected changes, update the `HyperConverged` resource instead of the operand:

  ```bash
  kubectl edit hyperconverged -n "${NS}" kubevirt-hyperconverged
  ```
