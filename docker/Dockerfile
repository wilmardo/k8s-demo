FROM ubuntu:disco

RUN apt-get update && apt-get install -y python3 python3-pip

COPY app /app

RUN pip3 install -r /app/requirements.txt

CMD ["python3", "/app/app.py"]
