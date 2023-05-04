# Install Nginx Centos 7 or Docker

**Step 1 — Adding the EPEL Software Repository**

```
sudo yum install epel-release
```

**Step 2 — Installing Nginx**

```
sudo yum install nginx
sudo systemctl start nginx
sudo systemctl status nginx
```

#### Install Nginx with Docker file

```
nginx-proxy:
image: nginx
restart: unless-stopped
ports:
- '80:80'
- '443:443'
volumes:
- /data/services/nginx/conf.d:/etc/nginx/conf.d:ro
- /data/services/letsencrypt:/etc/nginx/ssl:ro
- /etc/localtime:/etc/localtime:ro
```
