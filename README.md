#### pamasyrdomn
#####email
dnsimple
fastmail->go to hosting service, add a txt record

#####sourcecode
######dropboxrepo
init a base repo(simulate a remote repo)  
in remote repo:
```
git init --bare
```
in local repo:
```
touch a
git add .
git commit -am
git remote add origin c:/remote
git push origin master
```
######using gitlab
create a droplet, name code.yd.me
```
cat ~/.ssh/id_rsa.pub | ssh root@[your.ip.address.here] "cat >> ~/.ssh/authorized_keys"
```
[see this tutorial] (https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets)  

add an A record to dnsimple, add `code.yd.me`  

#####blog
######create ghost (I failed using 512mb and 1gb droplets)
Note if `/etc/subdoers busy try again later`
type
```
fg
```
to return.  

setting up user:
```
adduser ghost
update-alternatives --config editor //select 3
```
however, to create a home directory, we must use
```
mkhomedir_helper username
```
if user homedirectory does not exist

```
visudo
```
add
```
ghost ALL=(ALL:ALL) ALL
```

Next time use
```
ssh ghost@ip
```
to login.


install node.
```
apt-get upgrade -y
apt-get install build-essential wget unzip python-software-properties
```

######install ghost
```
sudo mkdir /var/www
curl -L https://ghost.org/zip/ghost-latest.zip -o ghost.zip
unzip -uo ghost.zip -d /var/www/ghost
```
config:
```
vim config.js
```
change
```
server:127.0.0.1 -> 0.0.0.0
```
finally
```
npm start --production
```

Run:
```
http://host:2368/ghost/setup/
```

######install nginx ,shadow remote 80 to local 2368
```
apt-get ..
sudo vim /etc/nginx/conf.d/yd.conf
```
edit:
```
server{
listen 80;
server_name 54.86.31.87;
location / {
proxy_set_header X-real_IP $remote_addr;
proxy_set_header Host $http_host;
proxy_pass http://127.0.0.1:2368;
}
}
```
######pm2
```
npm install -g pm2
pm2 start index.js --name ghost -i max
```
this will cause an issue, so we need to export env.
```
echo "export NODE_ENV=production" >>~/.profile
source ~/.profile
pm2 kill
```
restart:
```
pm2 start index.js --name ghost
```
(do not forget to change production ip to server name ip)
######postgresql
```
apt-get install postgresql libpq-dev
```
use postgres:
```
sudo su postgres
psql
>> create database ghost;
>> create role ghost with password '123456' LOGIN;  //must single quote
>> alter database ghost owner to ghost;
\q
exit
```
edit config.js ,change sqlite3 to pg
```
npm install pg
```
then
```
        database: {
            client: 'pg',
            connection: {
                host     : 'localhost',
                user     : 'ghost',
                password : 'ghost',
                database : 'ghost',
                charset  : 'utf8'
            },
            debug: false
        },
  ```
#####owncloud
[Install](https://software.opensuse.org/download.html?project=isv:ownCloud:community&package=owncloud)
[config](http://idroot.net/tutorials/install-owncloud-8-ubuntu-14-04/)
install components
```
apt-get install apache2 php5 php5-mysql php5-gd php5-json php5-curl php5-intl php5-mcrypt php5-imagick mysql-server
```
```
mysql_secure_install
```

and specify user and password
```
mysql> CREATE USER 'test'@'localhost' IDENTIFIED BY 'test';
mysql> CREATE DATABASE test;
mysql> GRANT ALL ON test.* TO 'test'@'localhost';
mysql> FLUSH PRIVILEGES;
mysql> exit
```
then
```
http://ip/owncloud
```
to use.

```
alias l="ls -la"
```

######fix apache
```
/etc/apache2/sites-enabled
```
change default:
```
vim /etc/apache2/sites-available/000-default.conf
```
change documentRoot name.from html->oncloud. then ```localhost```
######ssl
enable modules:
```
a2enmod ssl
service apache2 restart
```
create dir
```
mkdir /etc/apache2/ssl
```
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/woncloud.key -out /etc/apache2/ssl/owncloud.crt
```
Then config 
```
vim /etc/apache2/sites-available/000-default.conf
```
change locations:
```
SSLCertificateFile
SSLCertificateKeyFile
```
Then
```
a2ensite default-ssl.conf
```
