# docker-rpi-jeedom
## Raspberry Docker images for Jeedom

###jeedom folder:
* Docker build and compose files to generate / run a Jeedom image for Raspberry Pi
(MySQL not included, ready for OpenZWave plugin installation).
* Image available on Docker Hub: https://hub.docker.com/r/sbeuzit/rpi-jeedom/

###jeedom-oz folder:
* Same as above except that OpenZWave plugin is installed during image generation.
* Image available on Docker Hub: https://hub.docker.com/r/sbeuzit/rpi-jeedom-oz/

###jeedom-data folder:
* Image to build the volume container that will store MySQL files
* Image available on Docker Hub: https://hub.docker.com/r/sbeuzit/rpi-jeedom-data/



###How to use these images:
Easiest way is to get either https://hub.docker.com/r/sbeuzit/rpi-jeedom-oz/docker-compose.yml (OpenZWave plugin installed) or https://hub.docker.com/r/sbeuzit/rpi-jeedom/docker-compose.yml (OpenZWave plugin not installed) and launch:
``` docker-compose up``` (docker-compose 1.7.1 or above needed)

Then go to http://<raspberry ip>/ and start the installation. MySQL hostname is jeedom-mysql and password is 'password' (can be changed in docker-compose.yml file).

####Remark 1:
If no ZWave USB key / RFLink module is present (such as the UZB1) then remove the devices section from the docker-compose.yml filedevices section from the docker-compose.yml file.
```


