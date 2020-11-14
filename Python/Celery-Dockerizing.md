# Celery Dockerizing

> Docker-Compose를 이용한 Celery Daemonize + Dockerizing

<br>

### Project Structure

```bash
├── celeryd
│   ├── app.py
│   ├── dockerfile
│   ├── requirements.txt
└── docker-compose.yml
```

<br>

### Celeryd

**app.py**

```python
from celery import Celery
from celery.exceptions import TaskError

BROKER_URL = "redis://redis:6379/0"
BACKEND_URL = "redis://redis:6379/0"

app = Celery("tasks", broker=BROKER_URL, backend=BACKEND_URL)

@app.task
def TestTask(a, b):
    return a + b
```

**dockerfile**

```dockerfile
FROM python:3.7-slim

ENV CELERYD_DIR /app/celeryd

WORKDIR ${CELERYD_DIR}

COPY requirements.txt ./
RUN pip install --upgrade pip && \
    pip install -r requirements.txt

COPY . .
CMD celery -A app worker -P solo -l info
```

<br>

### Docker-Compose

```yaml
version: "3"

services: 
  redis:
    image: redis
    container_name: redis
    restart: always
    ports:
      - "6379:6379"

  celeryd:
    build: ./celeryd
    container_name: celeryd
    restart: always
```

<br>

```bash
$ docker-compose up -d --build
```



