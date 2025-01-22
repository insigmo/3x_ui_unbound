

## Preinstall
```bash
# create user
adduser --create-dir /home/amster amster

# switch to user
su amster

# set locals
export LC_CTYPE=en_US.UTF-8
export LC_ALL=en_US.UTF-8

# install required packages
sudo apt update
sudo apt full-upgrade && sudo apt autoremove
sudo apt install git openssh-server python3.11-venv libaugeas0


curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh

# add user to docker group
sudo usermod -aG docker amster
sudo reboot

# After reboot
# constants
WORKDIR=~/git/vc

# install certbot for httpS
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip
sudo /opt/certbot/bin/pip install certbot

sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
sudo certbot certonly -m qwantone@gmail.com --agree-tos -d hse-vpn.ddns.net --standalone

mkdir $WORKDIR/cert/
sudo cp /etc/letsencrypt/live/hse-vpn.ddns.net/fullchain.pem $WORKDIR/cert/fullchain.pem
sudo cp /etc/letsencrypt/live/hse-vpn.ddns.net/privkey.pem $WORKDIR/cert/privkey.pem

sudo chmod 600 $WORKDIR/cert/*pem

# renewal cert
echo "0 0,12 * * * root /opt/certbot/bin/python -c 'import random; import time; time.sleep(random.random() * 3600)' && sudo certbot renew -q" | sudo tee -a /etc/crontab > /dev/null

```

## Install vpn

```bash
# constants
WORKDIR=~/git/vc
cd $WORKDIR

docker compose up -d
```

## Postinstall vpn
```bash
# Panel Settings -> General -> Public Key Path ==> /root/cert/fullchain.pem
# Panel Settings -> General -> Private Key Path ==> /root/cert/privkey.pem
# Panel Settings -> General -> URI Path ==> /web/
```