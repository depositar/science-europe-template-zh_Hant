# Version Lifecycle Policy

`translation-config.yml` uses `version_policy` to decide which upstream template
versions automation may touch. This prevents old, reviewed translations from
being refreshed accidentally while still allowing active versions to follow new
clean scaffold artifacts.

## Policy Fields

Each version has an effective policy built from `defaults`, matching `rules`,
and exact `overrides`.

| Field | Meaning |
| --- | --- |
| `state` | Human label such as `active`, `maintenance`, or `archived`. |
| `refresh` | Whether branch scaffold/content refresh is allowed. |
| `migrate_into` | Whether exact-safe translations may be migrated into the version. |
| `publish_release` | Whether version-branch CI may overwrite GitHub Release assets. |
| `reason` | Optional note explaining an override. |

`refresh` and `migrate_into` accept:

| Value | Behavior |
| --- | --- |
| `auto` | Scheduled automation and manual runs may act on the version. |
| `manual` | Only `workflow_dispatch` runs may act on the version. |
| `false` | Automation must not refresh or migrate into the version. |

## Current Intended States

The current repository policy keeps every supported version active. The config
uses active defaults and no special rules:

```yaml
version_policy:
  defaults:
    state: active
    refresh: auto
    migrate_into: auto
    publish_release: true
  rules: []
  overrides: {}
```

## Freezing an Older Version

Use a maintenance rule only when the team wants a version to remain supported
but no longer follow scheduled scaffold refreshes:

```yaml
- match: ">=v1.29.1 <v1.30.0"
  state: maintenance
  refresh: manual
  migrate_into: manual
  publish_release: true
```

Archive a version with an exact override when the team wants to freeze both its
translation content and release assets:

```yaml
overrides:
  v1.30.1:
    state: archived
    refresh: false
    migrate_into: false
    publish_release: false
    reason: frozen after public handoff
```

Archived branches may still receive generated control-file updates, such as a
workflow change that disables release publishing. They must not receive clean
scaffold refreshes or migrated translation content.

## Operating Rules

- Scheduled operations runs use `policy-mode=auto`.
- Manual `workflow_dispatch` operations runs use `policy-mode=manual`.
- Automatic migration targets only versions with `migrate_into: auto`.
- Explicit migration targets may include `manual` versions, but not `false`
  versions.
- `publish_release: false` stops version-branch CI from clobbering release
  assets for that version.

When changing policy, run the operations workflow and inspect its summary. It
should report which branches were created, refreshed, or updated for controls
only.
