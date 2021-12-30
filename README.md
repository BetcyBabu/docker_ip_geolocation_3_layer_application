# Geo IP location using Docker

This is a 3 layer project written using flask for getting geo location of an IP address using IP Geolocation API and using redis to cache the information. 

- Front end / GUI layer for user interaction
- Middle layer for communicating with a caching server and obtaining IP geo information using [API](https://ipgeolocation.io/)
- Backed - Redis for caching

If the caching server already has the geo IP location, redis will provide  information, otherwise this layer will communicate with https://ipgeolocation.io/ using API key and retrieve the IP information and will cache this information on the redis server.

**AWS Secret Manager** helps you to protect your privacy, so I'm storing my API key in AWS Secret Manager.

## Prerequisites

Unique authentication key to gain access to the IP geolocation API, https://ipgeolocation.io/

## Create bridge network

```
docker network create geoip
```

## Creating container for caching using redis image

```
docker container run -d  --name caching --network geoip --restart always redis:latest
```

## Creating container for middle layer

Here I'm creating a container for communication between the caching server and https://ipgeolocation. 

```
docker container run -d  --name api-caching --network geoip --restart always -p 8080:8080  -e REDIS_HOST=caching -e APP_PORT=8080 -e API_KEY_FROM_SECRETSMANAGER=True -e SECRET_NAME=ipgeolocation -e SECRET_KEY=API-Key -e REGION_NAME=ap-south-1 betcybabu/ipgeolocation-api-service:v1
```

SECRET_NAME, SECRET_KEY, and REGION_NAME available from the AWS managmenet console

![image](https://user-images.githubusercontent.com/23291976/147779403-b26ff8de-263d-4e26-bb3f-9809ac88d22b.png)

Since the local port 8080 is open, you can use http://ip:8080//api/v1/ip to view Geop information in json format.

Example URL: http://65.2.142.60:8080/api/v1/8.8.8.8

![image](https://user-images.githubusercontent.com/23291976/147779646-f77e5681-cafb-4d77-b3db-a11399bd121f.png)

## Creating container for front-end/GUI

```
docker container run --name frontend -d --network geoip -p 80:8080 -e API_SERVER=api-caching -e API_SERVER_PORT=8080 -e API_PATH="/api/v1/" betcybabu/ipgeolocation-frontend:v1
```

![image](https://user-images.githubusercontent.com/23291976/147780668-fa5e86cb-7d8c-451d-ac45-61b425e98b61.png)








