1 FROM alpine
2 
3 RUN apk update
4 RUN apk add wget curl
5
6 RUN wget -O test.txt https://github.com/PacktWorkshops/   The-Docker-Workshop/blob/master/Chapter03/Exercise3.02/100MB.bin
7
8 CMD mkdir /var/www/
9 CMD mkdir /var/www/html/
10
11 WORKDIR /var/www/html/
12
13 COPY Dockerfile.tar.gz /tmp/
14 RUN tar -zxvf /tmp/Dockerfile.tar.gz -C /var/www/html/
15 RUN rm /tmp/Dockerfile.tar.gz
16
17 RUN cat Dockerfile