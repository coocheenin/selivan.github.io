
## Installing shadowsocks server via bash script

Create self-hosted [shadowsocks](https://shadowsocks.org/) server (shadowsocks-libev) in a single line.
shadowsocks-libev is a port created by [@clowwindy](https://github.com/clowwindy), written in pure C.

This script is forked from [Pavel Selivanov script](https://github.com/coocheenin/selivan.github.io/blob/master/shadowsocks.txt), with adding Debian compatibility.

### Install without setup (running in a single line)

`curl https://raw.githubusercontent.com/coocheenin/shadowsocks-libev-bash/master/shadowsocks.sh | sudo bash`

### Install & Setup (only two lines in terminal)

You can also set the port and password manually before installing:

```bash
export PORT=8080; export PASSWORD=mypass
curl https://raw.githubusercontent.com/coocheenin/shadowsocks-libev-bash/master/shadowsocks.sh | sudo --preserve-env bash
```

It's enough for working, but you can make a [fine tuning](https://shadowsocks.org/en/config/advanced.html).

You will get special URI like:
`ss://YmYtY2ZiOnRlc3QvIUAjOkAxOTIuMTY4LjEwMC4xOjg4ODg`

These URIs are identified by shadowsocks clients and besides very convenient to store and share.

### Clients (MacOS, iOS, Android)
* [shadowsocks clients](https://shadowsocks.org/en/download/clients.html)

### Supported Linux distributions:

* CentOS 7 and RHEL 7
* Debian 8
* Ubuntu 16.04 Xenial
* Ubuntu 18.04 Bionic