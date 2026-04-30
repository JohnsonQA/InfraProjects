# Portfolio App Deployment on AWS

This project is a Flask-based portfolio application deployed on AWS EC2 using Gunicorn and Nginx. It demonstrates a real-world setup of application hosting, reverse proxy configuration, and HTTPS enablement.

---

## Application Setup

Clone the repository and set up the Python environment:

```
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python3 app.py
```

This runs the app locally for development. For production, Gunicorn is used instead of Flask’s built-in server.

---

## Why Gunicorn

Flask’s default server is not suitable for production. Gunicorn acts as a production-grade server that:

- Handles multiple concurrent requests
- Runs the application efficiently using worker processes
- Keeps the backend isolated from direct internet traffic

The app runs internally on:

```
127.0.0.1:8000
```

## Command to run as Gunicorn

```
gunicorn app:app --bind 0.0.0.0:8000 --daemon
```

---

## EC2 Deployment Steps

- Launch EC2 instance
- Install required packages:

```
sudo yum install git nginx -y
```

- Clone repository and activate virtual environment by just running ./run.sh
- Install dependencies and start the app using Gunicorn
- Ensure port 8000 is accessible internally

At this stage, the app is running but only accessible via IP and port.

---

## Gunicorn as a Service

Create a systemd service file:

```
sudo vi /etc/systemd/system/gunicorn.service
```

Add the following configuration:

```
[Unit]
Description=Gunicorn service
After=network.target

[Service]
User=ec2-user
WorkingDirectory=/home/ec2-user/PortfolioApp
Environment="PATH=/home/ec2-user/PortfolioApp/.venv/bin"
ExecStart=/home/ec2-user/PortfolioApp/.venv/bin/gunicorn --workers 3 --bind 127.0.0.1:8000 app:app
Restart=always

[Install]
WantedBy=multi-user.target
```

Start and enable the service:

```
sudo systemctl daemon-reload
sudo systemctl start gunicorn
sudo systemctl enable gunicorn
```

---

## Nginx Configuration and Routing

Nginx is used as a reverse proxy to expose the app on standard web ports. We use nginx, so that all static sites, videos, images and path based routing can be done through nginx over gunicorn.

Basic configuration to be updated in nginx.conf:

```
server {
    listen 80;
    server_name static.app.infralabx.space www.static.app.infralabx.space;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### How routing works

- User sends request to domain
- Nginx receives request on port 80
- Nginx forwards request to backend (Gunicorn on port 8000)
- Response is sent back to user

Flow:

```
User → Nginx (80) → Gunicorn (8000) → Flask App
```

---

## Domain Setup

- Create a hosted zone in Route 53
- Update nameservers in domain provider
- Point domain to EC2 public IP
- Verify using nslookup

---

## HTTP vs HTTPS

- HTTP (port 80) sends data in plain text
- HTTPS (port 443) encrypts communication using SSL/TLS

HTTPS ensures:

- Secure data transfer
- Trusted connection via certificate
- Protection against interception

---

## SSL Setup using Certbot

```
sudo yum install certbot python3-certbot-nginx -y

sudo certbot --nginx \
-d static.app.infralabx.space \
-d www.static.app.infralabx.space
```

### What Certbot does

- Verifies domain ownership
- Generates SSL certificates
- Stores them in:

```
/etc/letsencrypt/live/your-domain/
```

- Updates Nginx configuration automatically
- Enables HTTPS and optional redirection

---

## HTTP to HTTPS Redirection

```
return 301 https://$host$request_uri;
```

---

## Final Architecture

```
User → HTTPS (443)
     → Nginx (SSL termination)
     → Gunicorn (8000)
     → Flask App
```

---

## Certificate Renewal

```
sudo certbot renew --dry-run
```

---

## Summary

- Flask app runs on port 8000 using Gunicorn
- Nginx handles incoming traffic and routing
- Domain is mapped using Route 53
- Certbot enables HTTPS with automatic configuration
- Secure communication is enforced using redirection
