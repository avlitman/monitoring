# VMMemoryOvercommitTooHigh

## Meaning

This alert fires when the cluster-wide VM memory overcommit percentage
exceeds 200% for more than 5 minutes.

## Impact

High memory overcommit is a critical condition that will immediately
cause VMs to be killed due to OOM.

## Diagnosis

1. Set the `NAMESPACE` environment variable:

   ```bash
   $ export NAMESPACE="$(kubectl get kubevirt -A -o custom-columns="":.metadata.namespace)"
   ```

2. Check the current memory overcommit setting:

   ```bash
   $ kubectl -n $NAMESPACE get hyperconverged kubevirt-hyperconverged -o jsonpath='{.spec.higherWorkloadDensity}'
   ```

3. Check for memory pressure on nodes:

   ``` bash
   $ kubectl get nodes -o custom-columns="NAME:.metadata.name,MEMORY_PRESSURE:.status.conditions[?(@.type=='MemoryPressure')].status"
   ```

4. Check the `kubevirt_hco_unsafe_modifications` metric to verify
   the overcommit value:

   ```bash
   $ kubectl -n $NAMESPACE exec -it prometheus-k8s-0 -- promtool query instant http://localhost:9090 'kubevirt_hco_unsafe_modifications'
   ```

## Mitigation

Reduce the memory overcommit percentage in the HyperConverged CR:

```bash
$ kubectl -n $NAMESPACE patch hyperconverged kubevirt-hyperconverged --type merge -p '{"spec":{"higherWorkloadDensity":{"memoryOvercommitPercentage":100}}}'
```
<!--DS: If you cannot resolve the issue, log in to the
link:https://access.redhat.com[Customer Portal] and open a support case,
attaching the artifacts gathered during the diagnosis procedure.-->
<!--USstart-->
If you cannot resolve the issue, see the following resources:

- [OKD Help](https://okd.io/docs/community/help/)
- [#virtualization Slack channel](https://kubernetes.slack.com/channels/virtualization)
<!--USend-->
