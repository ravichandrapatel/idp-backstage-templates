# DevZero IDP — Backstage Catalog & Templates

Catalog entities, org model, and Software Templates for the [DevZero Backstage](https://github.com/ravichandrapatel/backstage) developer portal.

## Layout

```text
catalog/
  entities.yaml       # System / Component / API examples
  org.yaml            # Users & Groups
  cluster-labs.yaml   # Cluster Resource
templates/
  example-nodejs/     # Software Template + scaffold content
catalog-info.yaml     # Location aggregating the above
```

## Register in Backstage

Prefer registering the root Location:

```yaml
catalog:
  locations:
    - type: url
      target: https://github.com/ravichandrapatel/idp-backstage-templates/blob/main/catalog-info.yaml
```

Or register each file:

```yaml
catalog:
  locations:
    - type: url
      target: https://github.com/ravichandrapatel/idp-backstage-templates/blob/main/catalog/entities.yaml
    - type: url
      target: https://github.com/ravichandrapatel/idp-backstage-templates/blob/main/catalog/org.yaml
      rules:
        - allow: [User, Group]
    - type: url
      target: https://github.com/ravichandrapatel/idp-backstage-templates/blob/main/catalog/cluster-labs.yaml
      rules:
        - allow: [Resource]
    - type: url
      target: https://github.com/ravichandrapatel/idp-backstage-templates/blob/main/templates/example-nodejs/template.yaml
      rules:
        - allow: [Template]
```
