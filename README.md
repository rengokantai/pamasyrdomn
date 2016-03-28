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



