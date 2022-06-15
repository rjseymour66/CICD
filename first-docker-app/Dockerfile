FROM ubuntu:18.04
RUN apt-get update && \
    apt-get install -y python
ENV NAME Ryan
COPY hello.py .
ENTRYPOINT ["python", "hello.py"]
