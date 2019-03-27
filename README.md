# For Docker

## Git. Add hook pre-commit for crypted .env file

./.git/hooks/pre-commit

```bash
#!/bin/sh

openssl aes-256-cbc -k "<key>" -in .env -out .env.enc
git add ./.env.enc
echo "pre-commit - File '.env.enc' done"
```

## SSH public-key authentication

```bash
# Create a new SSH Key
ssh-keygen -t rsa -b 4096 -C "TravisCIDeployKey"

sudo chown root:root -R /root
sudo chmod 700 ~/.ssh/
sudo chmod 600 ~/.ssh/authorized_keys
```

## Travis SSH deploy only **LINUX**

[https://oncletom.io/2016/travis-ssh-deploy/](https://oncletom.io/2016/travis-ssh-deploy/)

[https://www.rusiczki.net/2018/01/25/use-travis-to-build-and-deploy-your-jekyll-site-through-ssh/](https://www.rusiczki.net/2018/01/25/use-travis-to-build-and-deploy-your-jekyll-site-through-ssh/)

[https://github.com/dwyl/learn-travis/blob/master/encrypted-ssh-keys-deployment.md](https://github.com/dwyl/learn-travis/blob/master/encrypted-ssh-keys-deployment.md)

Все действия производит только на *LINUX*!

```bash
# Encrypt file
touch .travis.yml && travis encrypt-file ./.travis/id_rsa ./.travis/id_rsa.enc --add

# If *.travis.yml* is not add decrypt command or variable in travis, you run command
travis login --org
```

## Build Vue app

```bash
docker run --rm --name web --volume "$(pwd)/trackingCoinsVue:/app" -it node:latest bash -c "cd /app && npm install --global npm@latest && npm install && npm run build"
```

## Other

```bahs
# Remove directory
rm -rf mydir

# Copy file
cp /var/www/coins/nginx.conf .

# Copy directory
cp -r /var/www/coins/site/letsencrypt/ .
```

Run docker

```bash
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
```

## Docker clean

```bash
# Stop all containers
docker stop $(docker ps -a -q)
# Delete all containers
docker rm $(docker ps -a -q)
# Delete all network
docker network rm $(docker network ls --quiet)
# Delete all images
docker rmi -f $(docker images -q)
# Remove all system volumes
docker volume rm $(docker volume ls -q)
# Remove all images, network, containers
docker system prune -a
```

## First step

```bash
docker-compose build
docker-compose run app bash -c 'cd $APP_DIR && npx sequelize db:migrate'
docker-compose run app bash -c 'cd $APP_DIR && npx sequelize db:seed:all'

# Revert migration
docker-compose run app bash -c 'cd $APP_DIR && npx sequelize db:migrate:undo'
```

## Additional command

```bash
docker-compose up --build
docker-compose up --detach
docker-compose down
docker-compose run app bash
```

## Volume

```bash
# Size volume
du --human-readable --summarize $(docker volume inspect --format '{{ .Mountpoint }}' <volume_name>)

# Size folder
du -x --human-readable  --max-depth=1 /

# Size file
ls -l --human-readable

# List free disk space

df --human-readable
```

## Run db with docker

```bash
docker run \
  --rm \
  --name postgres \
  --volume "/var/lib/docker/volumes/coins_coins-postgres/_data:/var/lib/postgresql/data" \
  --publish 5432:5432 \
  --env POSTGRES_USER=<username> \
  --env POSTGRES_PASSWORD=<password> \
  --env POSTGRES_DB=<DB name> \
  --env POSTGRES_PORT=5432 \
  postgres:10.3
```

## Run psql

```bash
docker run \
  --rm \
  --name psql \
  -it \
  --env PGHOST=localhost \
  --env PGUSER=<username> \
  --env PGPASSWORD=<password> \
  --env PGDATABASE=<DB name> \
  --env PGPORT=5432 \
  --network=host \
  postgres \
  psql --echo-all
```

## Create service file

```bash
vim /lib/systemd/system/coffee-print.service
```

```txt
[Unit]
Description=Docker compose for services
After=docker.service
Conflicts=shutdown.target reboot.target halt.target

[Service]
Restart=always
RestartSec=10
WorkingDirectory=/root/coffee-print/coffee-print-docker
ExecStart=/usr/local/bin/docker-compose up
ExecStop=/usr/local/bin/docker-compose down
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
TimeoutStartSec=10
TimeoutStopSec=30
StartLimitBurst=3
StartLimitInterval=60s
NotifyAccess=all

[Install]
WantedBy=multi-user.target
```

```bash
# Run docker-compose project
sudo systemctl daemon-reload
sudo systemctl enable coffee-print
sudo systemctl start coffee-print

sudo systemctl status coffee-print
sudo systemctl restart coffee-print
```

## Docker certbot

```bash
# Add certificate
sudo docker run \
  --rm \
  --name certbot \
  --volume "$(pwd)/certbot/conf:/etc/letsencrypt" \
  --volume "$(pwd)/certbot/www:/var/www/certbot" \
  certbot/certbot certonly \
  --non-interactive \
  --webroot \
  --agree-tos \
  --manual-public-ip-logging-ok \
  --domains domain.name \
  --email example@example.com \
  --webroot-path /var/www/certbot

# Update certificate
sudo docker run \
  --rm \
  --name certbot \
  --volume "/root/coffee-print/coffee-print-docker/certbot/conf:/etc/letsencrypt" \
  --volume "/root/coffee-print/coffee-print-docker/certbot/www:/var/www/certbot" \
  certbot/certbot renew
```

## Add to cron *certbot_renew.sh*. Readme inside file

## Vue Build dist

```bash
# Set execute permission on your script
# chmod +x build_web.sh

docker run --rm --name web --volume "$(pwd)/trackingCoinsVue:/app" -it node:latest bash -c "cd /app && npm install --global npm@latest && npm install && npm run build"
```

## Delete file from all commits

[https://help.github.com/articles/removing-sensitive-data-from-a-repository/](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)

```bash
git filter-branch --force --index-filter \
'git rm --cached --ignore-unmatch PATH-TO-YOUR-FILE-WITH-SENSITIVE-DATA' \
--prune-empty --tag-name-filter cat -- --all

git commit -am 'Bla-bla-bla'

git push origin --force
```
