# HomeKitInfluxGrafana
Homekit data ingestion in InfluxDB and visualisation in Grafana

# How to setup InfluxDB, grafana and Homekit .

### Prerequisites: 

###  You need a linux machine(VM, raspberry pi,etc), SSL certificate (not self signed)

Install Docker and Portainer ,  you can watch this amazing guide https://www.youtube.com/watch?v=kykvC2cGlNQ&t=0s  and you can use part of his scripts to easy install what you need.
Connect to SSH to your linux machine and run : 
`curl -sk https://raw.githubusercontent.com/rionshin/HomeKitInfluxGrafana/main/InstallDocker_Portainer | sudo bash - `

Go to http://IP:9000 to connect to Portainer and seyup your user/pass on 1st login. 

Install docker compose: 
``` 
sudo apt-get update
sudo apt-get install docker-compose-plugin 
docker compose version
```
Create a folder in your Home(or wherever you want) - I created /home/pi/influx where you need to place telegraf.comf and docker-compose.yaml 
go to the folder you created and run:
$ docker compose up -d  

This will create dockers for  Telegraf, Grafana and InfluxDB - please note as I am using 32 image of rasbian os I installed influxDB v1.8 and not 2.0+

If you need guide how to easily install InfluxDB v2+ you can check this guide: https://www.youtube.com/watch?v=QGG_76OmRnA   

You can verify the docker is up, by opening your Portainer and checking the new docker containers or runnning  ` docker container ps `

For some strange reason when I created the dockers, users and password was not enforced on InfluxDB  ,so if needed :

Open portainer , select the InfluxDB container and go to console and run : 
` influx ` 
```
CREATE USER username WITH PASSWORD 'password' WITH ALL PRIVILEGES  
```
Check DB or create new with:
```
show databases
name: databases
name
internal
Homekit
show users
user    admin
admin   true
homekit true
Create database NAME 
```
* More info here: https://docs.influxdata.com/influxdb/v1.8/administration/authentication_and_authorization/#user-management-commands 
* Then you need to change your config file to have Auth Enabled. This can be done again in portainer console.  
* You need to install VI or NANO (`sudo apt update and then sudo apt install nano`).
```
Open config file located in /etc/influxdb/
root@ :/# cd /etc/influxdb/
root@ :/etc/influxdb# nano influxdb.conf   
```
and then edit the config and add following lines on the bottom:
```
[http]

  enabled = true

  bind-address = ":8086"

  auth-enabled = true
```
Going forward when you connect to your influxdb via console the command will be: influx -username USER -password PASS