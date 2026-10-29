FROM ubuntu:22.04
LABEL org.opencontainers.image.title="my-ubuntu-web"
ENV APP_ENV=dev
ENV PORT=8000
RUN apt-get update && apt-get install -y python3 curl && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY app/ /app/
HEALTHCHECK --interval=10s --timeout=3s CMD curl -f http://localhost:8000/ || exit 1
EXPOSE 8000
CMD ["python3", "-m", "http.server", "8000"]
