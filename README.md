# Geo IP location using Docker

This is a 3 layer project written using flask for getting geo location of an IP address using IP Geolocation API and using redis to cache the information. 

- Front end / GUI layer for user interaction
- Middle layer for communicating with a caching server and obtaining IP geo information using [API](https://ipgeolocation.io/)
- Backed - Redis for caching

## Prerequisites

Unique authentication keyto gain access to the IP geolocation API, https://ipgeolocation.io/









