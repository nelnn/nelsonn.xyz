# www.nelsonn.xyz

Nelson N's Website.

## Installation

Firtly we need to create these DNS records. Point the domain name to the IPv4 and IPv6 addresses.

| Type | Value | TTL |
|------|----------------|-----|
|A| IPv4 address | 600 |
|AAAA  | IPv6 address  | 600 |
|CNAME |www.nelsonn.xyz |nelson.xyz|


I use VPS by Vultr (Debian12 x64). In my local machine,
```bash
ssh root@nelsonn.xyz
```
to ssh into the server. Use the password for root.

Generate a ssh key for login:
```bash
ssh-keygen
```

#TODO: disable login with password.

we need to install `nginx` for redirecting to the website, and certbot to get SSL certificate.
```bash
apt-get update
apt install nginx
```

To install certbot, follow the steps in the [official documentation](https://certbot.eff.org/#ubuntuxenial-nginx). I chose to install it with `pip`. We also need python3-certbot-nginx:
```bash
sudo apt-get install python3-certbot-nginx
```

Configure nginx:
- copy the `default` file in `site_avaliable` directory
```bash
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/nelson
```
- edit the file #TODO: link that file to this repo
- open port 80 `sudo ufw allow 80`.
- get SSL certificate `certbot --nginx`.
- `sudo systemctl reload nginx`

