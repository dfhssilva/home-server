# Home Server

## Introduction: NginX Proxy Manager (NPM) 

[Using NginX Proxy Manager for proper Website Routing](https://shownotes.opensourceisawesome.com/using-nginx-proxy-manager/)

- Using a proxy manager is the way you should serve up websites 
- Reverse proxy works as a single point of entry that takes a request and figures out what it should do
- Useful for self-host with multiple applications
- "I want to run a server inside my network at home. How can I do that with as low a risk as possible?" 
  - **Know what port the server is running on and forward it through your router**
- By using Nginx Proxy Manager it is **easier to manage the port forwarding** because of the single point of entry for multiple services
- Nginx proxies the request to the right service
- You can also be running the webserver on the same machine as the proxy manager. That way a request can be redirected by the proxy to itself
- Check [https://nginxproxymanager.com/guide/](https://nginxproxymanager.com/guide/) for a simple tutorial on how to set up Nginx Proxy Manager

## Hosting your home server
1. Access home router configurations and add Port Forwarding for port 80 and 443 to the host server
2. Get a domain name. Use a registar like [Epik](https://www.epik.com/)
3. Connect your domain and server with DNS records
  3.1. With a static IP address you can do this directly in the registar website
  3.2. If you don't have a static IP address (more likely for home servers), use a dynamic DNS provider like [Cloudflare](https://dash.cloudflare.com/). See section [Setup dynamic DNS](#setup-dynamic-dns)
4. Use NPM as your gateway to forward to your other web based services

### Add new proxy hosts to NPM
**Requirements**:
- NPM container should be running. The container can be launched with `docker compose -f npm-compose.yml up -d`
- Proxy host's service should be running as a Docker container within the home-server network. See [Docker network](#docker-network)

1. Access the NPM dashboard
2. Go to "Proxy Hosts" and "Add Proxy Host"
3. Fill in the Proxy Host details: choose the domain name to which the service will be available, choose which host and port to forward, optionally set up SSL certificates here

## Docker network
By creating a custom Docker network, we don't need to publish ports for our upstream services to all of the Docker host's interfaces.

Create the **home-server** network: `docker network create home-server`

Then add the following to the `docker-compose.yml` file for both NPM and any other services running on the Docker host:
```
# Service-level specification
networks:
  - home-server-network

# Top-level specification
networks:
  home-server-network:
    external: true
    name: home-server 
```

Now in the NPM UI you can create a proxy host with the **docker service name as the hostname**. Ports are "exposed" by each container and not available on the Docker host outside of this Docker network.

## Setup dynamic DNS
This section describes how to setup dynamic DNS with Cloudflare and `ddclient`.

**Requirements**:
- Acquire a domain using a registar like [Epik](https://www.epik.com/)
- Sign up to Cloudflare and follow the steps given for updating your domain name to point towards Cloudflare’s DNS (go to "Add a Site" on the "Websites tab")

1. Install `ddclient` to update dynamic DNS entries for accounts on Dynamic DNS Network Service Provider: `sudo apt update && sudo apt install ddclient`
2. Replace existing uncommented lines in `/etc/ddclient/ddclient.conf` making sure to replace the username, api-key, zone and the DNS record names with the ones you used to setup your Cloudflare account:
```
use=web, web=checkip.dyndns.com/, web-skip='IP Address'
ssl=yes
protocol=cloudflare
server=api.cloudflare.com/client/v4
login=your_email
password=your_cloudflare-global-api-key
zone=your_domain.com
dns-record-name-update-1,dns-record-name-update-2
```
3. Restart `ddclient` with `sudo /etc/init.d/ddclient restart`

To run `ddclient` as a daemon:
1. Add `run_daemon="true"` and set run_dhclient and run_ipup to false in the `/etc/default/ddclient` file
2. Start the `ddclient` service: `sudo service ddclient start` (ensure everything runs smoothly with `sudo service ddclient status`)
3. Force ddclient to update IP address once a week (required by some DDNS services): Create the `/etc/cron.weekly/ddclient` file and add the followinf lines:
```
#!/bin/sh
/usr/sbin/ddclient -force
```
4. Make the script executable: `sudo chmod +x /etc/cron.weekly/ddclient`
5. Check everything runs smoothly `sudo service ddclient status`

## Directory structure
Each hosted service contains a folder with its name in the repository.

Inside each folder is the corresponding service `docker-compose.yml` used to launch it, and a `.gitignore` file in case any files are passed to the container and should be kept out of version control. An optional README file can be kept in the subdirectory to give additional information to the service.
