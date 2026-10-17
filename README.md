# kiln

Where the lab's container images are baked. One workflow and nothing else: it checks out the source repository at
the commit it is asked for, builds that repository's own Dockerfiles, and pushes the images to the organisation's
registry. The sources, the Dockerfiles and the images stay where they are; only the build runs here.

    gh workflow run image -R dmrai-lab/kiln -f tessera_ref=main
    gh workflow run image -R dmrai-lab/kiln -f tessera_ref=<branch or sha> -f dmipy_sim_commit=<sha> -f dmipy_sim_cuda_commit=<sha>

The pins default to the ones the checked-out Dockerfiles carry. A build of `main` moves the floating tags; a build of
any other ref is reachable by its commit tag alone. The build runs only when dispatched: no push and no pull request
starts it, and the layer cache lives in the registry, not in this repository's Actions cache.

## Secrets

| secret | what it is | used for |
|---|---|---|
| `SOURCE_TOKEN` | a fine-grained token with *Contents: read* on `dmrai-lab/tessera` and `dmrai-lab/dmipy-sim-cuda` | checking out tessera; reading the kernel's release during the build (a BuildKit secret, never a layer) |
| `GHCR_TOKEN` | a classic token with `write:packages` | pushing to `ghcr.io/dmrai-lab/tessera`, `tessera-node`, `tessera-client` and their build caches |
