---
name: restart
description: Restart the NanoClaw service and clear all agent session IDs so containers start with fresh context. Use when the service needs a clean restart, skills have been updated, or agent behavior is stale.
---

# Restart NanoClaw

Rebuild the container image, restart the service, and clear all sessions so every group starts with fresh context.

## Steps

1. Stop any running agent containers:

```bash
docker ps -q --filter "name=nanoclaw" | xargs -r docker stop
```

2. Rebuild the container image:

```bash
CONTAINER_RUNTIME=docker ./container/build.sh
```

3. Clear all session IDs from SQLite:

```bash
sqlite3 store/messages.db "DELETE FROM sessions;"
```

4. Restart the systemd service:

```bash
systemctl --user restart nanoclaw
```

5. Verify the service is running:

```bash
systemctl --user is-active nanoclaw
```

6. Confirm sessions are cleared:

```bash
sqlite3 store/messages.db "SELECT count(*) FROM sessions;"
```

The count should be `0`. New session IDs will be created automatically when agents are next invoked.
