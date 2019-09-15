# Traefik v2.0 -- Under Development...

-------

## How to use this repo:

Clone the repo using:

  `git clone https://github.com/CVJoint/traefik2.git`
 
Then create the environment variable **$TRAEFIK2DIR** which points to this directory **e.g. $PWD/traefik2**. I have been calling these out in my **.bash_aliases** file by using

  `export TRAEFIK2DIR=/home/$USER/traefik2`

or whichever directory you'd like. Reload your .bashrc or .bash_aliases file using `source .bashrc`. I've been using this method in order to maintain the same configuration across machines just by copying my .bash_aliases file.

You can include the following in your .bash_aliases file in order to bring up containers, e.g. `dup traefik`:

  ```
  dup() {
    docker-compose -f /home/$USER/traefik2/ymlfiles/$1.yml -p $1 up -d
  }
  ```

and to bring down containers, e.g. `ddown traefik`:

  ```
  ddown() {
    docker-compose -f /home/$USER/traefik2/ymlfiles/$1.yml -p $1 down
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

Create the **traefik_proxy** network with:

  `docker network create traefik_proxy`

or specify the subnet that you want to use (as long as it's in the private IP space). My server is at 192.168.1.50, so I figure a 192.168.50.0/24 subnet will be easy to identify with that server's docker network. I use this method if I need to assign IPs to containers.

  `docker network create --gateway 192.168.50.1 --subnet 192.168.50.0/24 traefik_proxy`

or create a macvlan network with:

  `docker network create -d macvlan --subnet=192.168.1.0/24 --ip-range=192.168.1.128/25 --gateway=192.168.1.1 -o parent=eth0 traefik_proxy`

  If you create a macvlan network make sure your DHCP server doesn't assign
  addresses above .127  **Note:** services won't be availabe by the host. This
  hasn't been an issue for me since I run my services on a server, and access
  them on other devices, with the exception of pihole.
  Pihole runs in a docker container on a raspberry pi as a separate piece of
  networking equipment.


Change **your email** in the traefik.toml, and run: 

`dup traefik`

---

