# Create a new folder for the project
`mkdir umgacs`

`cd umgacs`

# Create the Flask Application file
`nano umgacs.py`

Content:
```py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return 'Hey'

if __name__ == '__main__':
    app.run(host='0.0.0.0')
```

# Install everything needed
`sudo apt update`

`sudo apt install nginx`

`sudo apt install software-properties-common`

`sudo apt install python3 python3-pip`

`pip3 install flask`

`sudo apt install gunicorn`

`sudo apt install snapd`

`sudo snap install --classic certbot`

`sudo ln -s /snap/bin/certbot /usr/bin/certbot`

# Get a certificate on Nginx
`sudo certbot --nginx`

# Edit the Nginx config
`sudo nano /etc/nginx/sites-enabled/default`

Content:
```nginx
server {
    server_name umgacs.online;

    location / {
        proxy_pass http://localhost:8000;
        include /etc/nginx/proxy_params;
        proxy_redirect off;
    }

    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/umgacs.online/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/umgacs.online/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}
server {
    if ($host = umgacs.online) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    listen 80 ;
    listen [::]:80 ;
    server_name umgacs.online;
    return 404; # managed by Certbot
}
```

# Enable Ports needed
`sudo ufw allow http/tcp`

# Run gunicorn
Get Cores Amount  on your VPS with :
`nproc --all`

Run gunicorn :
`gunicorn -w WORKERS umgacs:app`

(Replace WORKERS with : Cores Amount x 2 + 1)

# Visit your website
https://umgacs.online/

