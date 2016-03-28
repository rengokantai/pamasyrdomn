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
