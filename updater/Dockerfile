FROM ubuntu:18.04
RUN apt-get update && \
    apt-get -y upgrade && \
    apt-get install -y clamav curl && \
    /usr/bin/freshclam
CMD /usr/bin/freshclam
