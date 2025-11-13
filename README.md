## Summary
This PR implements 7 Helm-chart tasks applied on top of `hyperglance/helm-chart` for review.

## What changed
1) Pod-per-service
   - Split services and refined selectors.
   - Added readiness/liveness probes; configurable via values.
2) NetworkPolicies
   - Default-deny; explicit egress to PostgreSQL + DNS.
   - Per-pod ingress from cluster-internal components.
3) Service-mesh compatibility
   - Added annotations (sidecar.istio.io/inject), probe rewrites.
   - Optional headless svc for mTLS-friendly discovery.
4) Resources & HPA hooks
   - Sensible cpu/memory requests/limits; values-enabled.
   - HPA values scaffold (disabled by default).
5) Security context & PodDisruptionBudget
   - runAsNonRoot, fsGroup; optional PDB for controlled rollouts.
6) Values schema & docs
   - `values.schema.json` + comments in `values.yaml`.
   - README table for new values.
7) Operational toggles
   - ExtraEnv/ExtraVolumes, nodeSelectors/tolerations/affinity.
   - Timestamp annotation for safe rolling updates.

## Why
- Safer defaults, clearer isolation, and easier ops in real clusters.
- Compatible with common meshes (Istio/Linkerd).
- Predictable rollouts and resource usage.

## Backwards compatibility
- All new features default to **disabled** or safe defaults.
- No breaking field renames; previous values continue to work.

## Versioning
- Chart version: `X.Y.Z` -> `X.Y+1.0` (feature bump).
- appVersion unchanged.

## Testing done
- `helm lint`, `helm template`, dry-run install (logs attached).
- KIND install + smoke check (`kubectl get pods`, readiness gates).
- If available: `ct lint` and `ct install` outputs attached.

## How to test
```bash
helm upgrade --install hyperglance charts/hyperglance \
  -f examples/minimal.yaml \
  --set networkPolicy.enabled=true \
  --set resources.enabled=true \
  --set serviceMesh.enabled=false