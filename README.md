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
3. Connect your domain and server with DNS records (if you don't have a static IP address you need to use a service like DuckDNS)
4. Use NPM as your gateway to forward to your other web based services

### Add new proxy hosts to NPM
1. Access the NPM dashboard
2. Go to "Proxy Hosts" and "Add Proxy Host"
3. ...


## Tips and tricks

### Docker network
By creating a custom Docker network, we don't need to publish ports for our upstream services to all of the Docker host's interfaces.

Create the home-server network: `docker network create home-server`

Then add the following to the `docker-compose.yml` file for both NPM and any other services running on the Docker host:
```
networks:
  home-server-network:
    external: true
    name: home-server 
```

Now in the NPM UI you can create a proxy host with the docker service name as the hostname. Ports are "exposed" by each container and not available on the Docker host outside of this Docker network.
