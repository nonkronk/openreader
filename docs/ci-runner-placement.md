# GitHub Actions runner placement

OCI ARM64 is the default CI execution host. Runner routing must be explicit to prevent general tests from falling back onto Unraid.

## Portable jobs on OCI

`docs-check.yml`, `docs-deploy-on-release.yml`, `docs-deploy.yml`, `docs-version-on-tag.yml`, `vitest.yml`.

These workflows require `[self-hosted, Linux, ARM64, oci-light]`. A repository-scoped OCI runner with the `oci-light` label MUST be registered and online before this branch is merged. The lightweight runner MUST have CPU and memory quotas and MUST NOT expose the OCI Docker socket.

## Unraid exceptions

`docker-publish.yml`, `playwright.yml`.

These workflows remain on the existing `[self-hosted, Linux, X64, openreader]` Unraid runner because they use x86-specific dependencies, browser/system packages, or Docker builds. Review each exception before changing the execution host.

## Activation and rollback

1. Register the new OCI runner and confirm it is online in GitHub repository Settings > Actions > Runners.
2. Execute the representative workflow successfully on OCI, checking the runtime and job logs.
3. Merge this migration only after the OCI runner is healthy.
4. Keep the old Unraid registration until remaining workflows are verified. Stop Unraid polling only after exceptional jobs have a viable event-driven or manual activation path.
5. Roll back by reverting these workflow routing commits if the OCI runner cannot execute the jobs.

Runner infrastructure and resource limits are owned by `nonkronk/oci`.
