# WordPress
## deploy WordPress behind Traefik

##### Create docker network
```
sudo docker network create proxy
```
### Clone this git repository:
```
git clone https://vdarkobar:2211620c9da5dab0c7bb77e9aeb02087d293b293@github.com/vdarkobar/WordPress.git
```
##### Add passwords and change premissions
```
echo | openssl rand -base64 20 >> ./secrets/mysql_root_password.secret
echo | openssl rand -base64 20 >> ./secrets/wp_mysql_password.secret
sudo chown -R root:root ~/WordPress/secrets
```
##### Change domain name
```
sudo nano WordPress/docker-compose.yml
```
##### Start
```
sudo docker-compose -f WordPress/docker-compose.yml up -d
```
##### Log
```
sudo docker logs -tf --tail="50" wordpress
sudo docker logs -tf --tail="50" wp-db
```
```
# There are two database images you can choose from, MySQL:5.7 or MariaDB. As the password authentication method changed in MySQL 8, 
# if you really want to use MySQL, choose version 5.7 or you need extra command[1] listed below.
image: mysql:8
command: '--default-authentication-plugin=mysql_native_password'
```
