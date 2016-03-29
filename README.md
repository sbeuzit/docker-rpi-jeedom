# docker-rpi-jeedom
Raspberry Docker images for Jeedom

jeedom folder:
- Docker build and compose files to generate / run a Jeedom image for Raspberry Pi
(MySQL not included, ready for OpenZWave plugin installation).
- Image available on Docker Hub: https://hub.docker.com/r/sbeuzit/rpi-jeedom/

jeedom-oz folder:
- Same as above except that OpenZWave plugin is installed during image generation.
- Image available on Docker Hub: https://hub.docker.com/r/sbeuzit/rpi-jeedom-oz/

jeedom-data folder:
- Image to build the volume container that will store MySQL files
- Image available on Docker Hub: https://hub.docker.com/r/sbeuzit/rpi-jeedom-data/



How to use these images:
Easiest way is to get either https://hub.docker.com/r/sbeuzit/rpi-jeedom-oz/docker-compose.yml (OpenZWave plugin installed) or https://hub.docker.com/r/sbeuzit/rpi-jeedom/docker-compose.yml (OpenZWave plugin not installed) and launch docker-compose up.
Then go to http://<raspberry ip>/ and start the installation. MySQL hostname is jeedom-mysql and password is 'password' (can be changed in docker-compose.yml file).

Remark: To use tmpfs filesystem for log files and prevent SD card wearout, you can launch the jeedom container with specific parameter (will be added in docker-compose.yml once tmpfs will be supported - currently it is only available in 1.7 RC1 version):
sudo docker stop jeedom
sudo docker rm jeedom
sudo docker run -d --link jeedom-mysql:mysql --device=/dev/ttyACM0:/dev/ttyACM0 --name jeedom --mac-address="b8:27:eb:3c:08:33" -p 80:80 -p 8083:8083 -p 443:443 --tmpfs /tmp:rw,size=32M --tmpfs /var/log:rw,size=32M --tmpfs /var/www/html/log:rw,size=32M  sbeuzit/rpi-jeedom-oz


