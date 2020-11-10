# Traefik v2

## How to use this repo:

Clone the repo using:

```
$ git clone https://github.com/CVJoint/traefik2.git $HOME/docker/traefik2
```

Then create the environment variable, **\$TRAEFIK2DIR** which points to this directory. I've been using a **.bash_aliases** file to identify my environment variables with:

```
$ vim $HOME/.bash_aliases
```

```
export TRAEFIK2DIR="$HOME/docker/traefik2"
```

Reload your .bash_aliases file using:

```
$ source $HOME/.bash_aliases
```

I've been using this method in order to maintain the same configuration across different hosts by copying/syncing my .bash_aliases file.

## Starting/Stopping Containers

You can include the following in your .bash_aliases file in order to bring up Docker containers, e.g. `dup traefik`:

  ```
  dup() {
    docker-compose -f $TRAEFIK2DIR/ymlfiles/$1.yml -p $1 up -d
  }
  ```

and to bring down containers, e.g. `ddown traefik`:

  ```
  ddown() {
    docker-compose -f $TRAEFIK2DIR/ymlfiles/$1.yml -p $1 down
  }
  ```

Other environment variables you will need:

 - CLOUDFLARE_EMAIL
 - CLOUDFLARE_API_KEY
 - DOMAINNAME
 - MY_EMAIL
 - GOOGLE_CLIENT_ID
 - GOOGLE_CLIENT_SECRET
 - OAUTH_SECRET
 - ... and a few others

## Traefik_proxy Network

Create the **traefik_proxy** network with:

```
$ docker network create traefik_proxy
```

or specify the subnet that you want to use (as long as it's in the private IP space). My server
is at 192.168.1.50, so I figure a 192.168.50.0/24 subnet will be easy to identify with that
server's docker network. I use this method so that I can assign static IPs to containers.

```
$ docker network create --gateway 192.168.50.1 --subnet 192.168.50.0/24 traefik_proxy
```

or create a macvlan network with:

```
$ docker network create -d macvlan --subnet=192.168.1.0/24 --ip-range=192.168.1.128/25 --gateway=192.168.1.1 -o parent=eth0 traefik_proxy
```

If you create a macvlan network make sure your DHCP server doesn't assign addresses above **.127** .

**Note:** your services on the macvlan network won't be available by the host. This means, for example, that the host couldn't use a pihole on the macvlan network, although all other services and devices could access the pihole. This may not be an issue if you only access the services on other devices, and you run pihole on a dedicated raspberry pi ;-). There's a way around this by modifying your host network config, but I haven't figured that part out yet.

I'm currently using the second method, which is a bridge network with a defined gateway and subnet.

---

## How I actually bring up/down my containers

I use a scripts folder **$HOME/.scripts** where I have the following file
**"dup"**:

```
#!/bin/sh
# $HOME/.scripts/dup

echo $(basename "$0")
CONTAINERS="$@"
for c in $CONTAINERS
do
  echo ""
  echo "...$c up..."
  echo ""
  docker-compose -f "$TRAEFIK2DIR/ymlfiles/$c.yml" -p $c up -d
  done"
```

Then, in my **.bash_aliases** file I've added:

```
export PATH="$PATH:$HOME/.scripts"
```

Now it's possible to bring up multiple projects at the same time with `dup traefik bitwarden
plex` etc. The script will go in a loop and start each service in order.

A similar **ddown** script can be created to bring multiple services down:

```
#!/bin/sh
# $HOME/.scripts/ddown

CONTAINERS="$@"
for c in $CONTAINERS
do
  echo ""
  echo "...$c down..."
  echo ""
  docker-compose -f "$TRAEFIK2DIR/ymlfiles/$c.yml" -p $c down
  done
```

Now I have a line saved in my notes of all my "standard" services so that I can start/stop them easily. I have two lines saved: One starts the database containers first, the other lists the databases at the end so that they're stopped last.

---

#Support the maintainer

This software is provided under MIT license and therefore freely usable without restrictions. Dontations are always welcome:

ETH - 0x05aC8e6C1cb8A7a4624F550c62a41Be96dD5d2E3

BTC - 35UcjMWh39wvbugg2HRDt49a9LV25FT6d8
