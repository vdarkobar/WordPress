# WordPress
  
<p align="left">
  <a href="https://github.com/vdarkobar/Home_Cloud#small-home-cloud">Home</a> |
  <a href="https://github.com/vdarkobar/NextCloud#nextcloud">NextCloud</a> |
  <a href="https://github.com/vdarkobar/Bitwarden#bitwarden">Bitwarden</a> |
  <a href="https://github.com/vdarkobar/Ghost-blog#ghost-blog">Ghost-blog</a> |
  <a href="https://github.com/vdarkobar/Portainer">Joomla</a>  
  <br><br>
</p>  
  
Login to <a href="https://dash.cloudflare.com/">CloudFlare</a> and set Domain name, or Domain name and Subdomain for your WordPress.
```
    A | example.com | YOUR WAN IP
```
or:
```
    A | example.com | YOUR WAN IP
```
```
    CNAME | subdomain | @ (or example.com)
```

---

### Create Docker networks:
```
sudo docker network create wp
sudo docker network create db
```
### Clone WordPress Git Repository:
```
RED='\033[0;31m'; echo -ne "${RED}Enter directory name: "; read NAME; mkdir -p "$NAME"; \
cd "$NAME" && git clone https://github.com/vdarkobar/WordPress.git .
```
  
#### *Decide what you will use for*:
```
Time Zone,
Domain name,
Subdomain (if planned),
WordPress Port Number.
```
  
### Select and run all at once. Enter required data:
*Only works once, on wrong data input delete folder and clone again*.
```
clear
RED='\033[0;31m'
echo -ne "${RED}Enter Time Zone: "; read TZONE; \
echo -ne "${RED}Enter Domain name: "; read DNAME; \
echo -ne "${RED}Enter Subdomain with . (dot) at the end, or just press Enter to default to Domain name: "; read SDNAME; \
echo -ne "${RED}Enter WordPress Port Number (WPPORTN:80): "; read WPPORTN; \
sed -i "s|01|${TZONE}|" .env && \
sed -i "s|02|${DNAME}|" .env && \
sed -i "s|03|${WPPORTN}|" .env && \
sed -i "s|04|${SDNAME}|" .env && \
echo | openssl rand -base64 48 > secrets/mysql_root_password.secret && \
echo | openssl rand -base64 20 > secrets/wp_mysql_password.secret && \
sudo chown -R root:root secrets/ && \
sudo chmod -R 600 secrets/
```
    
### Start:  
*Change Container names/Port numbers, if multiple instances are planed.*
```
sudo docker-compose up -d
```
### Log:
```
sudo docker logs -tf --tail="50" wordpress
sudo docker logs -tf --tail="50" wp-db
```
  
### Dynamic config (Traefik VM):
Create file: *service_name.yml* in Traefik: */data/configurations/* folder for routing and to get a free SSL certificate.

```
http:

  # All routers:
  routers:

    # WordPress service router
    wordpress-router:
      service: wordpress-service
      middlewares:
#        - www-redirect@file # Uncomment, give it a unique name. Set the same name in WWW-Redirect (middlewares.yml) if using domain name only.
      entryPoints:
        - "websecure"
      rule: "Host(`subdomain.example.com`)" # comment out if using domain name
#      rule: "Host(`example.com`) || Host(`www.example.com`)" # comment out if using subdomain, used for non-www to www redirect

  # All services:
  services:

    # WordPress service
    wordpress-service:
      loadBalancer:
        servers:
          - url: "http://local-ip:WPPORTN" # adjust ip and port nummber

```
  
### Middlewares *(Traefik VM)*:
Add to: *middlewares.yml* in Traefik: */data/configurations/* for non-www to www redirect  
  
* *If using Domain name for WordPress*
```
http:

  # All middlewares
  middlewares:
  
    # WWW-Redirect
    www-redirect: # match the name from WordPress router in service_name.yml
      redirectRegex:
        regex: "^https://example.com/(.*)"
        replacement: "https://www.example.com/${1}"
```  
  
### Follow <i><a href="https://github.com/vdarkobar/shared/blob/main/WordPress.md">this link</a></i> for additional WordPress settings.  
  

<a href="https://github.com/vdarkobar/WordPress#wordpress">top of the page</a>
