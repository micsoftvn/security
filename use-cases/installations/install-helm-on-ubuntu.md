# Install Helm on Ubuntu

```
$ wget https://get.helm.sh/helm-v3.12.0-linux-amd64.tar.gz
```

```
$ tar -xvf  helm-v3.12.0-linux-amd64.tar.gz
```

```
$ sudo mv linux-amd64  /usr/local/bin
```

```
$ sudo ln -s /usr/local/binlinux-amd64/helm /usr/local/bin/helm
```

Check version

```
helm version
```

