# Tor - Sock - Proxy

#### 1. Install tor-stock-proxy with docker

Setup the proxy server at the first time

```
docker pull peterdavehello/tor-socks-proxy
```

Run container Tor with paramter --restart=always the container will always on daemon startup.

```
docker run -d --restart=always --name tor-socks-proxy -p 127.0.0.1:9150:9150/tcp peterdavehello/tor-socks-proxy:latest
```

If you already setup the instance before

```
docker start tor-socks-proxy
```

#### 2. Make sure it's running

You can using cmd docker logs for check it

```
docker logs tor-sock-proxy
```

**Test Sock**

```
curl --socks5-hostname 127.0.0.1:9150 https://check.torproject.org
```

#### 3. Foward connect

We can now use a tool like ProxyChains to forward out traffic through this SOCKS proxy by adding the following entry to the ProxyList section of the /etc/`proxychains`.conf file:

```
socks5 127.0.0.1 9150
```

For example, to forward traffic through our SOCKS proxy with ProxyChains prefix any command with `proxychains` like this (the `-q` is to ignore errors):

```
proxychains curl -q ipinfo.me; echo
```

