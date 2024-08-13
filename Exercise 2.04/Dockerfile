# WORKDIR, COPY and ADD example
#
# This is an updated version of the Docker file. 
# The origional image wasn't available.
#
FROM ubuntu:latest
RUN apt-get update && apt-get install apache2 -y
WORKDIR /var/www/html/
COPY index.html .
ADD https://upload.wikimedia.org/wikipedia/commons/thumb/1/1f/AT-6C_Texans_in_flight_1943.jpg/1280px-AT-6C_Texans_in_flight_1943.jpg AT-6.jpg
CMD ["ls"]
