[![Join our Telegram RU](https://img.shields.io/badge/Telegram-RU-03A500?style=for-the-badge&logo=telegram&logoColor=white&labelColor=blue&color=red)](https://t.me/hidden_coding)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/aero25x)
[![Twitter](https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=x&logoColor=white)](https://x.com/aero25x)
[![YouTube](https://img.shields.io/badge/YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://www.youtube.com/@flaming_chameleon)
[![Reddit](https://img.shields.io/badge/Reddit-FF3A00?style=for-the-badge&logo=reddit&logoColor=white)](https://www.reddit.com/r/HiddenCode/)
[![Join our Telegram ENG](https://img.shields.io/badge/Telegram-EN-03A500?style=for-the-badge&logo=telegram&logoColor=white&labelColor=blue&color=red)](https://t.me/hidden_coding_en)


### Repository Structure

```
.
├── docker-compose.yml
├── nginx
│   └── conf
│       └── app.conf
├── certbot
│   ├── conf    # Certbot configuration will be stored here
│   └── www     # This folder serves the ACME challenge files
└── README.md
```

---


# Dockerized Nginx with Let's Encrypt SSL

This repository provides a Dockerized setup for running Nginx secured with free SSL/TLS certificates from [Let's Encrypt](https://letsencrypt.org/) using Certbot.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed.
- [Docker Compose](https://docs.docker.com/compose/install/) installed.
- A registered domain name pointing to your server.
- Administrative access to the system.

## Repository Structure

```
.
├── docker-compose.yml
├── nginx
│   └── conf
│       └── app.conf
├── certbot
│   ├── conf    # Let's Encrypt configuration
│   └── www     # ACME challenge files served by Nginx
└── README.md
```

## Setup Instructions

### 1. Clone the Repository

Clone this repository and change into the project directory:

```bash
git clone https://github.com/yourusername/yourrepo.git
cd yourrepo
```

### 2. Update the Nginx Configuration

Open `nginx/conf/app.conf` and replace `your-domain.com` with your actual domain name:

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name your-domain.com www.your-domain.com;
    server_tokens off;

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    location / {
        return 301 https://your-domain.com$request_uri;
    }
}

server {
    listen 443 default_server ssl http2;
    listen [::]:443 ssl http2;

    server_name your-domain.com;

    ssl_certificate /etc/nginx/ssl/live/your-domain.com/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/live/your-domain.com/privkey.pem;
    
    location / {
        proxy_pass http://your-domain.com;
    }
}
```

### 3. Obtain SSL Certificates

First, perform a dry run to test certificate generation:

```bash
docker-compose run --rm certbot certonly --webroot --webroot-path /var/www/certbot/ --dry-run -d your-domain.com
```

If the dry run succeeds, run the command without the `--dry-run` flag:

```bash
docker-compose run --rm certbot certonly --webroot --webroot-path /var/www/certbot/ -d your-domain.com
```

During this process, follow the prompts:
- Enter your email when asked.
- Agree to the Let's Encrypt Terms of Service.

### 4. Start the Nginx Webserver

Once the certificates are obtained, start the containers:

```bash
docker-compose up -d
```

If Nginx is already running and you need to reload the configuration without downtime:

```bash
docker-compose exec webserver nginx -s reload
```

### 5. Renewing Certificates

Let's Encrypt certificates are valid for 90 days. To renew them, run:

```bash
docker-compose run --rm certbot renew
```

It is recommended to set up a cron job (or another scheduler) to run this command regularly.

## Troubleshooting

- **Ports:** Ensure ports 80 and 443 are open and accessible.
- **DNS:** Verify your domain's DNS records point to your server's IP.
- **Logs:** Check Docker logs for any errors:
  
  ```bash
  docker-compose logs
  ```

## License

This project is licensed under the [MIT License](LICENSE).


---

### File: docker-compose.yml

```yaml
version: '3'

services:
  webserver:
    image: nginx:latest
    ports:
      - "80:80"
      - "443:443"
    restart: always
    volumes:
      - ./nginx/conf/:/etc/nginx/conf.d/:ro
      - ./certbot/www/:/var/www/certbot/:ro

  certbot:
    image: certbot/certbot:latest
    volumes:
      - ./certbot/www/:/var/www/certbot/:rw
      - ./certbot/conf/:/etc/letsencrypt/:rw
```

---

### File: nginx/conf/app.conf

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name your-domain.com www.your-domain.com;
    server_tokens off;

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    location / {
        return 301 https://your-domain.com$request_uri;
    }
}

server {
    listen 443 default_server ssl http2;
    listen [::]:443 ssl http2;

    server_name your-domain.com;

    ssl_certificate /etc/nginx/ssl/live/your-domain.com/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/live/your-domain.com/privkey.pem;
    
    location / {
        proxy_pass http://your-domain.com;
    }
}
```

---

### Additional Notes

- **Certificates Directory:**  
  The certificates generated by Certbot will be stored in the `certbot/conf/` directory on your host and mapped to `/etc/letsencrypt/` in the container.

- **ACME Challenge:**  
  The directory `certbot/www/` is used to serve the ACME challenge files required by Let's Encrypt during the certificate issuance process.

- **Customization:**  
  You can further customize the Nginx configuration or add more services as needed.


  [![Join our Telegram RU](https://img.shields.io/badge/Telegram-RU-03A500?style=for-the-badge&logo=telegram&logoColor=white&labelColor=blue&color=red)](https://t.me/hidden_coding)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/aero25x)
[![Twitter](https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=x&logoColor=white)](https://x.com/aero25x)
[![YouTube](https://img.shields.io/badge/YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://www.youtube.com/@flaming_chameleon)
[![Reddit](https://img.shields.io/badge/Reddit-FF3A00?style=for-the-badge&logo=reddit&logoColor=white)](https://www.reddit.com/r/HiddenCode/)
[![Join our Telegram ENG](https://img.shields.io/badge/Telegram-EN-03A500?style=for-the-badge&logo=telegram&logoColor=white&labelColor=blue&color=red)](https://t.me/hidden_coding_en)

