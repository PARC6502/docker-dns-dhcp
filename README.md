# Docker DNS and DHCP server

This is my setup for a dockerized DNS and DHCP server using the [jpillora/dnsmasq](https://hub.docker.com/r/jpillora/dnsmasq) docker image for dnsmasq.

I use this to set a local domain that points to my [media server](https://github.com/PARC6502/docker-media-server) where I have a reverse proxy that points specific subdomains to the different services I have there. I also use it to block ads on my home network, using [hosts](https://github.com/StevenBlack/hosts).

The example conf reflects this setup but it can also be setup in any other way you'd want to use a DNS or DHCP server.

It uses the following ports:

- 8080 Web UI
- 53 DNS
- 67 DHCP

# Caveats

Currently this is running in "host" network mode, so it's not really as isolated as it could be, and there could be port conflicts. However this was the easiest option for getting the DHCP server to work in a docker container. Another option maybe using a [macvlan network](https://docs.docker.com/network/macvlan/). [This page](https://docs.pi-hole.net/docker/DHCP/) contains a brief discussion of the different options.

# Usage

1. Clone this repo and cd into it

```bash
git clone https://github.com/PARC6502/docker-dns-dhcp.git && cd docker-dns-dhcp
```

2. Make copies of example files

```bash
cp dnsmasq.example.conf dnsmasq.conf && cp static_host.example static_hosts && cp ad.example.list ad.list
```

3. Set the domain(s) and IP address(es) in the `dnsmasq.conf` file

This is the line you want to change

```
address=/.local.cloud/192.168.1.3
```

4. (Optional) You can set static IPs based on hostname or MAC address in `dnsmasq.conf`

5. (Optional) Add static hosts to your static_hosts file (could point to different servers, android tv, etc)

6. (Optional) You can add sites you'd like to block in `ad.list` in the format shown below, I'm using [this file](https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts) on my network

```
0.0.0.0 facebook.com
```

7. Start up the container

```bash
sudo docker-compose up -d
```

You should be able to access the web UI at `<IP address>:8080`, (user:foo, pass:bar).

You can view a live feed of the logs from the UI as well as make changes to `dnsmasq.conf`. If you make changes to any of the files the dnsmasq will need to be restarted, this can be done from the UI or from the command line (note that you need to be in the folder with the `docker-compose.yml` file for this command to work)

```bash
sudo docker-compose restart dnsmasq
```
