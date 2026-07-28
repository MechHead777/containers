# containers

Container practice repo. Small, self-contained projects for working through core Docker/container concepts hands-on.

## joke-dashboard

A three-container setup that fetches a random dad joke every 30 seconds and serves it on a web page.

- **`init`**: one-shot `busybox` container that fixes volume permissions before anything else starts
- **`updater`**: multi-stage build (Ubuntu builder, Alpine runtime) that polls the [icanhazdadjoke](https://icanhazdadjoke.com) API on a loop and writes the result to a shared volume, running as a non-root user
- **`web`**: `nginx-unprivileged` serving that shared volume as static HTML, also non-root

Compose wires these together with `depends_on: condition: service_completed_successfully`, so `web` and `updater` only start once `init` has actually finished fixing permissions, not just once its container exists.

```sh
cd joke-dashboard
docker compose up
# http://localhost:8080
```

Practicing here: multi-stage builds to keep the runtime image minimal, running containers as a non-root UID end to end (not just one service), shared volumes between containers, and init-container-style dependency ordering in compose.
