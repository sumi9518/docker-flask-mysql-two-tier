# Part 1 — Docker networking

## Observed setup

| Container | Image shown by Docker | Ports | Networks |
| --- | --- | --- | --- |
| `flask-backend` | `two-tier-backend:latest` | Host `5001` forwards to container `5000/tcp` | `bridge`, `two-tier` |
| `zealous_edison` | `mysql` | Container ports `3306/tcp` and `33060/tcp` shown, with no host mapping | `bridge`, `two-tier` |

The browser uses the host port. Flask and MySQL share a Docker network for application-to-database traffic. The exact image build and container startup commands still need to be recorded from the local lab.

## Verify a running stack

Check the running containers, port mapping, and network memberships:

```bash
docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}\t{{.Networks}}'
```

Check the Flask endpoint:

```bash
curl -s -o /dev/null -w 'HTTP %{http_code}\n' http://localhost:5001/
```

Expected result: `HTTP 200`.

Submit a test message in the browser and check the application database using an authenticated MySQL session:

```sql
SELECT * FROM messages;
```

Confirm that the returned rows include the test message. The HTTP check confirms that Flask responded. The SQL check confirms that a message was stored.

## Troubleshooting

- If the browser cannot reach Flask, verify the container state and the host-to-container port mapping.
- If Flask responds but a message is missing, verify both containers share the intended network and check which database the app and SQL client are using.
