# Starter worker service

Background jobs with state on a volume that outlives the container. Nothing here accepts inbound
connections, and that absence is the kind: platfolio refuses this template if a host is ever attached,
because "worker" is a promise about attack surface, not a label.

```sh
docker compose -f compose.yaml -f compose.dev.yaml up --build
docker compose logs -f queue        # watch jobs recover their history after a restart
```

## The part that costs money

`job-state` keeps the worker’s job history between deployments. Removing the worker with
**Keep data** stops it and retains the volume. Restoring it reconnects the same disk and
reads `jobs.jsonl`. Retained storage may still incur charges.

**Wipe data** deletes the separate Railway project and schedules deletion of its storage.

## First changes worth making

1. Replace `tick()` with your queue client (SQS, Postgres `LISTEN`, Redis) and delete the disk journal.
2. Keep `/healthz` for the platform’s startup check. The worker has no public address.
3. Set `WORKER_CONCURRENCY` from the environment rather than the default of 4.
