# Docker Evidence – Lab 04

## Team

- Team name: HOANGTHI2509
- Service: core-business
- Image tag: `ghcr.io/hoangthi2509/team-core:v0.1.0-team-core`

## 1. Build evidence

Command:

```bash
docker build -t fit4110/core-business:lab04 .
```

Log Snippet:
```text
#1 [internal] load build definition from Dockerfile
#1 DONE 0.0s
#2 resolve image config for docker-image://docker.io/docker/dockerfile:1.7
#2 DONE 1.1s
#15 [runtime 5/6] COPY src/ ./src/
#15 DONE 0.1s
#16 [runtime 6/6] RUN chown -R appuser:appgroup /app
#16 DONE 0.4s
#17 exporting to image
#17 exporting layers 0.3s done
#17 exporting manifest sha256:82923f3c5a9ee41f4b8be8e28f46d13e61435e5112c79419db1154421e47b357 0.0s done
#17 naming to docker.io/fit4110/core-business:lab04 done
```

## 2. Run evidence

Command:

```bash
docker run -d --rm --name fit4110-core-lab04 -p 8000:8000 --env-file .env.example fit4110/core-business:lab04
```

Container Status (`docker ps`):
```text
CONTAINER ID   IMAGE                         COMMAND                  CREATED         STATUS                   PORTS                                         NAMES
2b062b789103   fit4110/core-business:lab04   "sh -c 'uvicorn core…"   2 minutes ago   Up 2 minutes (healthy)   0.0.0.0:8000->8000/tcp, [::]:8000->8000/tcp   fit4110-core-lab04
```

## 3. Healthcheck evidence

Command:

```powershell
Invoke-RestMethod -Uri http://localhost:8000/health
```

Result:

```json
{
  "status": "ok",
  "service": "core-business",
  "time": "2026-06-02T03:48:07+00:00"
}
```

## 4. Newman evidence

Command:

```bash
npm run test:local
```

Result Snippet:

```text
Smart Campus - Core Business API Contract (Completed)

□ 01_Functional
└ POST /alerts - Happy path
  POST http://localhost:8000/alerts [201 Created, 460B, 30ms]
  √  Status code is 201
  √  Response contains id and status

□ 02_Auth
└ POST /events - Missing Token
  POST http://localhost:8000/events [401 Unauthorized, 300B, 4ms]
  √  Missing token returns 401 Unauthorized

□ 03_Negative
└ POST /events - Invalid Payload
  POST http://localhost:8000/events [400 Bad Request, 302B, 3ms]
  √  Invalid payload returns 400 or 422
  √  Error follows ProblemDetails

□ 04_Boundary_Reliability
└ GET /alerts/recent - Limit Above Max
  GET http://localhost:8000/alerts/recent?limit=101 [400 Bad Request, 312B, 4ms]
  √  Limit above max returns 400 or 422

□ 05_Consumer_side_Smoke
└ POST /vision/face-match - Call AI Vision
  POST http://localhost:8000/vision/face-match [201 Created, 310B, 4ms]
  √  Face match mock returns 201 Created
  √  Response has detection results

□ 06_Local_only_NonFunctional
└ GET /health - Latency Test
  GET http://localhost:8000/health [200 OK, 201B, 3ms]
  √  Local service responds under 1000ms

┌─────────────────────────┬─────────────────┬─────────────────┐
│                         │        executed │          failed │
├─────────────────────────┼─────────────────┼─────────────────┤
│              iterations │               1 │               0 │
├─────────────────────────┼─────────────────┼─────────────────┤
│                requests │               6 │               0 │
├─────────────────────────┼─────────────────┼─────────────────┤
│            test-scripts │               6 │               0 │
├─────────────────────────┼─────────────────┼─────────────────┤
│      prerequest-scripts │               0 │               0 │
├─────────────────────────┼─────────────────┼─────────────────┤
│              assertions │               9 │               0 │
└─────────────────────────┴─────────────────┴─────────────────┘
```

Report path:

```text
reports/newman-lab04-local.html
reports/newman-lab04-local.xml
```

## 5. Notes

- Known limitation: None.
- Next step for Lab 05: Build Docker Compose configurations to orchestrate multiple connected microservices (Core Business, AI Vision, IoT, Access Gate).
