# DevZero IDP — Backstage Catalog & Templates

**All** Software Templates for the IDP portal live in this repository.

## Layout

```text
catalog/
  entities.yaml
  org.yaml
  cluster-labs.yaml
templates/
  example-nodejs/
  namespace-as-service/    # NaaS → pushes env/<env>/<cluster>/<tenant>.yaml to idp-argocd-apps
catalog-info.yaml
```

## Templates

| Template | What it does |
| --- | --- |
| `example-nodejs` | Sample Node service → new GitHub repo |
| `namespace-as-service` | Creates a NaaS tenant Application file and opens a PR to [`idp-argocd-apps`](https://github.com/ravichandrapatel/idp-argocd-apps) at `env/<env>/<clusterName>/<tenant>.yaml` |

## Register in Backstage

```yaml
catalog:
  locations:
    - type: url
      target: https://github.com/ravichandrapatel/idp-backstage-templates/blob/main/catalog-info.yaml
      rules:
        - allow: [Component, System, API, Resource, Location, Template, User, Group]
```

## Related repos

| Repo | Role |
| --- | --- |
| [namespace-as-service](https://github.com/ravichandrapatel/namespace-as-service) | Helm chart (OCI) |
| [idp-argocd-apps](https://github.com/ravichandrapatel/idp-argocd-apps) | Argo CD ApplicationSet + `env/cluster/namespace.yaml` |
| [backstage](https://github.com/ravichandrapatel/backstage) | Developer portal |
