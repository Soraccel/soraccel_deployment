# Soraccel deployment

This public repository is the versioned desired state of Soraccel robots. It
contains no operational scripts and no secrets.

## Contents

```text
robots/
└── <robot-id>/
    └── deployment.yaml
```

The robot installs a selected revision of this repository under
`/opt/soraccel/deployment`. The separate
[`soraccel_setup`](https://github.com/Soraccel/soraccel_setup) repository owns
bootstrap, Compose rendering and user-systemd management.

## Manifest

```yaml
environment:
  RMW_IMPLEMENTATION: rmw_cyclonedds_cpp

components:
  mapping_3d:
    image: ghcr.io/soraccel/mapping_3d:v0.1.1@sha256:...
    enabled_at_boot:
      - mapper
```

Every image embeds the `repository.yaml` used during its build. `apply` reads
that file from the pulled image and creates one user-systemd service for each
declared launch. `enabled_at_boot` selects which of those services starts on
boot; it is a robot policy and is never declared by the component.

Robot-local values and credentials never go here. Keep them under
`/etc/soraccel/config` and `/etc/soraccel`, respectively.

## Update

After a reviewed manifest is merged, install its explicit tag or commit on the
robot using the setup agent:

```bash
sudo /opt/soraccel/setup/scripts/install-deployment-revision \
  --deployment-ref <approved-tag-or-commit>
/opt/soraccel/setup/scripts/apply
```

This updates the desired state only. It does not update the setup scripts.
