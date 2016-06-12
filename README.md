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
If no ZWave USB key (such as the UZB1) / RFLink module is present then remove the devices section from the docker-compose.yml file.

###Remark 2:
To move containers and data to a new Raspberry
Create a new Docker image from the running Jeedom container
Stop containers first
```docker stop jeedom-mysql jeedom```
Create new image
```docker commit jeedom```
Tag new image as old one 
```docker images``` # To retrieve the'<image_id>
```docker tag <image_id> sbeuzit/rpi-jeedom-oz```
Save the image in a file 
```docker save sbeuzit/rpi-jeedom-oz > jeedom_oz_backup.tar.gz```

Save data from  jeedom-data container 
docker run --rm --volumes-from jeedom-data -v $(pwd):/backup hypriot/rpi-busybox-httpd tar czvf /backup/data-backup.tar.gz /var/lib/mysql


Copy jeedom_oz_backup.tar.gz, data-backup.tar.gz and docker-compose.yml to the new host
scp ...

Load the Docker image on the new host
```docker load <jeedom_oz_backup.tar.gz```
Check if new image is available 
```docker images```

Connect hardware on the new host (ZWave USB keys, RFLink...) 

Start Jeedom
Form the  docker-compose.yml folder
```docker-compose up```
Restore data. Stop MySQL server first
```docker-compose stop jeedom-mysql```
Restore data 
```docker run --rm --volumes-from jeedom-data -v $(pwd):/backup hypriot/rpi-busybox-httpd tar xzvf /backup/data-backup.tar.gz -C /```
 Verify if data are restored (a jeedom jeedom folder should have been created in /var/lib/mysql):
```docker run --rm -t -i --volumes-from jeedom-data -v $(pwd):/backup hypriot/rpi-busybox-httpd sh```
Then:
```ls -l /var/lib/mysql/```
```exit```

Restart MySQL server:
```docker-compose start jeedom-mysql```

```
