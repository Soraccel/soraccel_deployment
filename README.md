# Soraccel deployment

This repository is the versioned source of truth for robot runtime composition.
It only references published container images pinned by digest: no ROS 2 source
code is built or mounted on the robot.

## Layout

- `compose/`: runtime services.
- `env/robots/`: non-secret, reviewed image references and ROS settings per robot.
- `/etc/soraccel/<robot-id>/`: robot-only configuration and calibration; it is
  deliberately outside Git and mounted read-only.
- `scripts/apply`: validates, pulls and starts an approved environment.
- `systemd/`: optional boot-time service.

## Add a robot

1. Copy `env/robots/uav-dev-01.env.example` to an appropriately named `.env`
   file, replace the placeholder image digest and commit that file.
2. On the robot, create the configuration directory named by `ROBOT_CONFIG_DIR`.
   For `mapping_3d`, add `mapping_3d/mapping_3d.yaml` there.
3. From an approved revision of this repository, run:

   ```bash
   /opt/soraccel/deployment/scripts/apply \
     /opt/soraccel/deployment/env/robots/uav-dev-01.env
   ```

`apply` refuses floating tags: every deployed image must include `@sha256:`.
The robot only pulls and runs the compiled runtime image.

## Rollback

Check out the previously approved deployment commit, then run the same
`scripts/apply` command. Docker will reuse or pull the exact digest recorded in
that revision.

## Secrets

Do not commit GitHub tokens, Wi-Fi credentials, calibration files or private
keys. Keep the GHCR token in `/etc/soraccel/ghcr.env` and robot configuration
under `/etc/soraccel` with restrictive permissions.

