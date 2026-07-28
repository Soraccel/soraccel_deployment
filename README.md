# Soraccel deployment

This public repository is the versioned desired state of Soraccel robots. It
contains no operational scripts and no secrets.

## Contents

```text
robots/
└── <robot-id>/
    ├── deployment.yaml
    └── config/
        └── <component>.yaml
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

All non-secret configuration files are versioned in
`robots/<robot-id>/config/`. At `apply`, this flat directory is mirrored to
`/etc/soraccel/config/`; removed files are removed from the robot as well.
Component metadata refers to those files by basename, for example
`/config/mapping_3d.yaml`. Never place credentials in this public repository;
they remain only under `/etc/soraccel` on the robot.

## Update

After a reviewed manifest is merged, install its explicit tag or commit on the
robot using the setup agent:

```bash
sudo /opt/soraccel/setup/scripts/install-deployment-revision \
  --deployment-ref <approved-tag-or-commit>
/opt/soraccel/setup/scripts/apply
```

This updates the desired state only. It does not update the setup scripts.
