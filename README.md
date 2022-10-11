# HomeKit data to InfluxDB visualized in Grafana
Homekit data ingestion in InfluxDB and visualisation in Grafana

# How to setup InfluxDB, grafana and Homekit .

### Prerequisites: 

###  You need a linux machine(VM, raspberry pi,etc), SSL certificate (not self signed)

I have homebridge image installed on Raspberry PI4, you can install stardart debian, or rasbian, you need linux instace :)  

##Lets START: 

Install Docker and Portainer ,  you can watch this amazing guide https://www.youtube.com/watch?v=kykvC2cGlNQ&t=0s  and you can use part of his scripts to easy install what you need.

Connect to SSH to your linux machine and run : 


`curl -sk https://raw.githubusercontent.com/rionshin/HomeKitInfluxGrafana/main/InstallDocker_Portainer | sudo bash - `

Go to http://IP:9000 to connect to Portainer and setup your user/pass on 1st login. 

Install docker compose: 
``` 
sudo apt-get update
sudo apt-get install docker-compose-plugin 
docker compose version
```
Create a folder in your Home(or wherever you want) - I created /home/pi/influx where you need to place telegraf.comf and docker-compose.yaml 
go to the folder you created and run:
` docker compose up -d`  

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
command : show databases
result: 
name: databases
name
internal
Homekit
command: show users
results:
user    admin
admin   true
homekit true
command: Create database NAME 
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
Going forward when you connect to your influxdb via console the command will be:

`influx -username USER -password PASS`

## Homekit can connect only to valid SSL addresses for that reason you need SSL certificate and ofcourse valid IP and FQDN. 

Configure your DNS A record to map to your IP where your machine is located, 
you can map it to your router IP and then portforwarding to your internal machine with Influx, etc. 

Go and get SSL certificate , it is really cheap - around 10$ per year. 

You can setup the SSL to your Influx instance, however as i already have NGINX installed I created proxy in NGINX that forward   port 8087 with enabled SSL to   8086 where is influx instance with no ssl. 

The config file is located in the repository named as : ` nginx-influxproxy.local `

### Please dont forget to create port mapping on your router for all ports you are using as they need to be reachable from outside. 

If you dont have NGINX installed you can watch any guide or read this article
` https://www.supereasy.com/how-to-configure-nginx-as-a-https-reverse-proxy-easily/ `

With all this setup and installed you now have linux instace with running dockers for InfluxDB, Grafana, Telegraf, you have NGINX web server with SSL proxy to forward 8087 to 8086 , and your router/network is setup so your device is visible outside on ports needed. 