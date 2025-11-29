FROM python:3-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt --no-cache-dir
COPY . .
ENTRYPOINT ["python", "main.py"]
#ENTRYPOINT exec uvicorn main:app --host 0.0.0.0 --port $PORT --workers 2
#ENTRYPOINT exec gunicorn -b :$PORT -w 2 -k uvicorn.workers.UvicornWorker main:app
