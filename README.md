# WordPress
## deploy WordPress

##### Create docker networks (*if not already created*)
```
sudo docker network create wp
sudo docker network create db
```
### Clone this git repository
```
echo -n "Enter directory name: "; read NAME; mkdir -p "$NAME"; cd "$NAME" \
&& git clone https://vdarkobar:2211620c9da5dab0c7bb77e9aeb02087d293b293@github.com/vdarkobar/WordPress.git .
```
##### Add passwords and change premissions, *adjust folder name*
```
echo | openssl rand -base64 48 > secrets/mysql_root_password.secret
echo | openssl rand -base64 20 > secrets/wp_mysql_password.secret
sudo chown -R root:root secrets/
```
##### *Change container names, labels and volume name, inside docker-compose file, if multiple instances are planed.*
```
sudo nano docker-compose.yml
```
##### Start
```
sudo docker-compose up -d
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
