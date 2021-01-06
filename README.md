# WordPress
  
<p align="left">
  <a href="https://github.com/vdarkobar/Home_Cloud#proxmox">Home</a>
</p>  
  
Login to <a href="https://dash.cloudflare.com/">CloudFlare</a>  and set domain or subdomain for your WordPress.
```
    A | example.com | YOUR WAN IP
```
or:
```
    CNAME | blog | @ (or example.com)
```

---

### Create Docker networks
```
sudo docker network create wp
sudo docker network create db
```
### Clone WordPress git repository
```
RED='\033[0;31m'; echo -ne "${RED}Enter directory name: "; read NAME; mkdir -p "$NAME"; \
cd "$NAME" && git clone https://github.com/vdarkobar/WordPress.git .
```
  
#### *Decide what you will use for*:
```
Time Zone,
Domain name,
```
  
### Select and run all at once. Enter required data:
*Only works once, use bash*
```
RED='\033[0;31m'
echo -ne "${RED}Enter Time Zone: "; read TZONE; \
echo -ne "${RED}Enter Domain name: "; read DNAME; \
sed -i "s|01|${TZONE}|" .env && \
sed -i "s|02|${DNAME}|" .env && \
echo | openssl rand -base64 48 > secrets/mysql_root_password.secret && \
echo | openssl rand -base64 20 > secrets/wp_mysql_password.secret && \
sudo chown -R root:root secrets/ && \
sudo chmod -R 600 secrets/
```
### Adjust if necessary, *if multiple instances are planed.*
  
### Start
```
sudo docker-compose up -d
```
### Log
```
sudo docker logs -tf --tail="50" wordpress
sudo docker logs -tf --tail="50" wp-db
```
  
### Dynamic config (Traefik VM)
*create file: service_name.yml in Traefik: /data/configurations/ folder for routing and to get a free SSL certificate.*

```
http:

  # All routers:
  routers:

    # WordPress service router
    bitwarden-ws:
      service: WordPress-service
      middlewares:
      entryPoints:
        - "websecure"
      rule: "Host(`example.com`)" # adjust domain or use subdomain (blog.example.com)


  # All services:
  services:

    # WordPress service
    WordPress-service:
      loadBalancer:
        servers:
          - url: "http://local-ip:8686" # adjust ip and port nummber

```
  
```
# There are two database images you can choose from, MySQL:5.7 or MariaDB. As the password authentication method changed in MySQL 8, 
# if you really want to use MySQL, choose version 5.7 or you need extra command listed below.
image: mysql:8
command: '--default-authentication-plugin=mysql_native_password'
```
#### File upload limit:
```
sudo nano html/.htaccess
```
##### *add:*
```
php_value upload_max_filesize 128M
php_value post_max_size 128M
php_value max_execution_time 300
php_value max_input_time 300
```
