FROM alpine

RUN apk update
RUN apk add wget curl

RUN wget -O test.txt https://github.com/PacktWorkshops/The-Docker-Workshop/raw/master/Chapter03/Exercise3.02/100MB.bin

CMD mkdir /var/www/
CMD mkdir /var/www/html/

WORKDIR /var/www/html/

COPY Dockerfile.tar.gz /tmp/
RUN tar -zxvf /tmp/Dockerfile.tar.gz -C /var/www/html/
RUN rm /tmp/Dockerfile.tar.gz

RUN cat Dockerfile