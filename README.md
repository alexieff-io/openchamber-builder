# openchamber-builder

Builds the public [openchamber/openchamber](https://github.com/openchamber/openchamber) repo into a Docker image and publishes it to **your** GitHub Container Registry, so a Dockhand-managed Docker host can pull it like any normal image:

```
ghcr.io/YOUR_GITHUB_USER/openchamber:latest
ghcr.io/YOUR_GITHUB_USER/openchamber:v1.18.2   (every upstream release tag)
```

You do not need to own or fork the upstream repo — the workflow checks out the public source at its latest release tag and builds it with the upstream `Dockerfile` (amd64).

The workflow runs daily at 06:15 UTC, checks upstream for a new `vX.Y.Z` release tag, and builds/pushes it (tagged both as the version and `latest`) only if it isn't already in your GHCR. To update the running container, use Dockhand's image update / re-pull on the stack — or enable auto-update if you run something like Watchtower.

Manual controls (Actions tab → *Build OpenChamber image* → Run workflow):

- **ref** — build a specific upstream tag, branch, or commit instead of the latest release.
- **force** — rebuild and overwrite a version that already exists in your GHCR.

## Notes

- Image is built for `linux/amd64` only. To add ARM later, change `platforms:` in `.github/workflows/build.yml` to `linux/amd64,linux/arm64` (builds get slower via QEMU).
- The container binds OpenChamber to `0.0.0.0` (that's why the UI password is mandatory). Put it behind your reverse proxy / VPN as you see fit — upstream has notes in `docs/REVERSE_PROXY.md`.
- Storage uses named Docker volumes by default; the stack file has a comment showing how to switch `workspaces` to a host bind mount.
