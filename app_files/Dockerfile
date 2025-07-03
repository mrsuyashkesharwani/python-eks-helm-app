# Stage 1: Build
FROM python:3.11-slim AS builder

WORKDIR /app
COPY src/requirements.txt .

RUN pip install --upgrade pip && \
    pip install --user -r requirements.txt

# Stage 2: Runtime
FROM python:3.11-slim

WORKDIR /app
COPY src/ /app
COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH

EXPOSE 5000
CMD ["python", "app.py"]
