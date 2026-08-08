FROM python:3.13-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    ffmpeg \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY . .

RUN pip install --no-cache-dir .

EXPOSE 8501

CMD ["streamlit", "run", "youtube_downloader.py", "--server.port=8501", "--server.address=0.0.0.0"]