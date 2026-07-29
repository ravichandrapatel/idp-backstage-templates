# Namespace as a Service — Software Template

Version **1.1.0** · Git tag `template-namespace-as-service-v1.1.0`

## 1. How form validation works

Backstage validates in layers:

| Layer | What it does | NaaS usage |
| --- | --- | --- |
| **JSON Schema** | `required`, `pattern`, `enum`, `minItems` | tenant DNS label, env enum, chartVersion pattern |
| **UI fields** | Pickers refuse free text | `EntityPicker` for cluster, `OwnerPicker` for owner |
| **Catalog filter** | Only matching entities | `Resource` + `spec.type: kubernetes-cluster` |
| **Scaffolder filters** | Transform after submit | `parseEntityRef \| name` → Argo `clusterName` |

### Cluster name

`clusterEntity` uses **EntityPicker** with `allowArbitraryValues: false`.

- User can only choose catalog **Resource** entities of type `kubernetes-cluster`.
- Typed-in unknown cluster names are rejected by the UI.
- On submit, name is taken as `${{ parameters.clusterEntity | parseEntityRef | name }}`.
- That name must match an Argo CD cluster (`destination.name`).

Register a new cluster by adding a Resource under `catalog/` in this repo (see `catalog/cluster-labs.yaml`).

### Chart version

`chartVersion` is a **closed enum** of published releases (plus semver `pattern`).

Update the `enum` list whenever you publish a new chart tag on
`ravichandrapatel/namespace-as-service`.

## 2. How Argo CD gets the Helm chart version

Flow:

1. Form field `chartVersion` (e.g. `1.0.0`) is written into  
   `env/<env>/<cluster>/<tenant>.yaml` as `chartVersion: "1.0.0"`.
2. ApplicationSet `idp-argocd-apps/appsets/namespace-as-service.yaml` reads that file and sets:

   ```yaml
   source:
     repoURL: ghcr.io/ravichandrapatel/charts
     chart: namespace-as-service
     targetRevision: "{{.chartVersion}}"   # ← OCI chart tag
   ```

3. Argo CD pulls  
   `oci://ghcr.io/ravichandrapatel/charts/namespace-as-service:<chartVersion>`.

So Argo does **not** invent the version — it uses the value from the GitOps YAML
created by the template. Changing the tenant file’s `chartVersion` (or re-running
the template) changes the pin.

## 3. Tag-based template releases (recommended)

Treat this Software Template like a product version:

```bash
# after merging template changes to main
git tag -a template-namespace-as-service-v1.1.0 -m "NaaS template 1.1.0"
git push origin template-namespace-as-service-v1.1.0
```

Pin Backstage to that tag (immutable) instead of `main`:

```yaml
catalog:
  locations:
    - type: url
      target: https://github.com/ravichandrapatel/idp-backstage-templates/blob/template-namespace-as-service-v1.1.0/templates/namespace-as-service/template.yaml
      rules:
        - allow: [Template]
    # Still load catalog entities (clusters) from main or a catalog tag
    - type: url
      target: https://github.com/ravichandrapatel/idp-backstage-templates/blob/main/catalog/cluster-labs.yaml
      rules:
        - allow: [Resource]
```

| Practice | Why |
| --- | --- |
| Tag every template change | Portal can pin a known-good scaffolder |
| Keep `main` for development | Iterate without breaking production portal |
| Bump `ravichandrapatel.com/template-version` annotation | Visible in catalog / docs |
| Bump chart `enum` with chart releases | Form only offers installable OCI tags |

Skeleton file name `${{values.tenant}}.yaml` → path  
`env/<env>/<clusterName>/<tenant>.yaml` after the PR lands.
