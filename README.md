# tatu-rotate

This implementation creates 10 proxies with output based on differente location using
the surfshark VPN. The main objective this project is a simple way to get a lot
of different IPs to bypass Waf in tests.

## Overview
Tatu-Rotate leverages Docker containers to establish VPN connections using Surfshark OpenVPN configurations. Each container is configured to act as a proxy server on the host, with IP rotation managed by Mubeng. This setup facilitates the bypass of IP-based blocking mechanisms such as WAFs (Web Application Firewalls), brute-force defense mechanisms, API rate-limits, and geo-restrictions.

## Key Features
- **VPN Integration:** Utilizes Surfshark VPN configurations to connect each Docker container to a unique global IP.
- **Proxy Rotation:** Uses Mubeng to rotate proxy requests, ensuring each connection request is sent through a different IP address.


## Prerequisites
To run this project, you will need:
- Docker: [Install Docker](https://docs.docker.com/get-docker/)
- Docker Compose: [Install Docker Compose](https://docs.docker.com/compose/install/)
- An active Surfshark subscription.

## Configuration
### Environment Setup
Create a `.env` file in the project root with the following configurations:
```plaintext
# OpenVPN Configuration
VPN_USER= # Surfshark OpenVPN username
VPN_PASS= # Surfshark OpenVPN password

# OVPN Files - Each corresponds to a different VPN server
OVPN=uy-mvd.prod.surfshark.com_tcp.ovpn
OVPN2=im-iom.prod.surfshark.com_tcp.ovpn
OVPN3=au-syd.prod.surfshark.com_udp.ovpn
OVPN4=br-sao.prod.surfshark.com_tcp.ovpn
OVPN5=ca-van.prod.surfshark.com_udp.ovpn
OVPN6=cy-nic.prod.surfshark.com_tcp.ovpn
OVPN7=ec-uio.prod.surfshark.com_tcp.ovpn
OVPN8=hr-zag.prod.surfshark.com_tcp.ovpn
OVPN9=fi-hel.prod.surfshark.com_tcp.ovpn
OVPN10=es-mad.prod.surfshark.com_tcp.ovpn
```

### Docker Compose
Configure the `docker-compose.yml` to set up the Docker services. Each service (`rotate1` to `rotate10`) represents a Docker container configured as a proxy using a Surfshark VPN server. Mubeng is configured to manage these proxies and rotate them on each request.

### Supervisord Configuration
Supervisord is used to manage the OpenVPN and Privoxy services within the containers. Configuration files are located under `config-templates/supervisord`.

## Running the Project
To start the services, execute:
```bash
docker-compose up -d
```

## Example Usage
To demonstrate the proxy rotation:
```bash
while true; do
    curl -x http://10.47.0.200:8080 http://ip-api.com/json/
    sleep 1  # Pause to prevent rate limiting
done
```

### Output Examples
```json
{
  "status": "success",
  "country": "Canada",
  "city": "Toronto",
  "query": "45.136.154.250"
}
{
  "status": "success",
  "country": "United States",
  "city": "Denver",
  "query": "169.150.231.195"
}
{
  "status": "success",
  "country": "United States",
  "city": "Chicago",
  "query": "138.199.42.185"
}
{
  "status": "success",
  "country": "Sri Lanka",
  "city": "Sri Lanka",
  "query": "62.197.156.37"
}
{
  "status": "success",
  "country": "Slovenia",
  "city": "Ljubljana",
  "query": "195.158.249.26"
}
{
  "status": "success",
  "country": "Uruguay",
  "city": "Montevideo",
  "query": "212.119.32.35"
}
```
These examples demonstrate the effective rotation of IP addresses, with each request appearing to originate from a different global location.
