# DevZero IDP — Backstage Software Templates

Software Templates for the [DevZero Backstage](https://github.com/ravichandrapatel/backstage) developer portal.

## Layout

```text
templates/
  example-nodejs/
    template.yaml    # kind: Template
    content/         # scaffold payload (fetch:template)
```

## Register in Backstage

Point a catalog location at a template YAML (GitHub URL):

```yaml
catalog:
  locations:
    - type: url
      target: https://github.com/ravichandrapatel/idp-backstage-templates/blob/main/templates/example-nodejs/template.yaml
      rules:
        - allow: [Template]
```

Or register the root [`catalog-info.yaml`](catalog-info.yaml) Location entity.
