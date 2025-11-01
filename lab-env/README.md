# This is a Vulnerable Lab Setup created for the purpose of Testing my Skills ;)

This lab is created using **DVWA**, **JuiceShop** **Webgoat** and **Nginx** (for Reverse-proxy). Designed for Web-Application Security Practice.
The above all Vulnerable Applications run in *Docker Environment* for easier access.

## 🧠 Overview

This lab provides a safe, isolated environment for learning and practicing:
- Web penetration testing
- OWASP Top 10 vulnerabilities
- Ethical hacking and exploit development
- Reverse proxy configuration with Nginx
- Docker networking and container management

---


## Installation Steps:

**Beforehand** > Must have DOCKER and Kali *(Its my prefference, U may have any linux based OS)* Installed...

1. ### Create the network connection for Vulnerable lab environment:

'''bash
docker network create vulnlab
'''

2. ### Then create images for your lab, such as DVWA, juiceshop etc.
'''bash
docker run -d --name dvwa --network vulnlab vulnerables/web-dvwa
docker run -d --name juice-shop --network vulnlab bkimminich/juice-shop
docker run -d --name webgoat --network vulnlab webgoat/webgoat
'''

3. ### Create a Nginx.conf file:
'''nginx
events {}

http {
    # Timeouts/helpful proxy settings
    proxy_read_timeout  90s;
    proxy_connect_timeout 10s;
    proxy_send_timeout 10s;

    # Juice Shop
    server {
        listen 80;
        server_name juiceshop.local;

        location / {
            proxy_pass http://juice-shop:3000/;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header Connection "";
        }
    }

    # WebGoat (served at /WebGoat inside container)
    server {
        listen 80;
        server_name webgoat.local;

        location / {
            proxy_pass http://webgoat:8080/WebGoat/;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header Connection "";
        }
    }

    # DVWA
    server {
        listen 80;
        server_name dvwa.local;

        location / {
            proxy_pass http://dvwa:80/;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header Connection "";
        }
    }
}
'''

4. Now run the nginx reverse-proxy:
'''bash
docker run -d --name reverse-proxy \
  --network vulnlab \
  -p 80:80 \
  -v $(pwd)/nginx.conf:/etc/nginx/nginx.conf:ro \
  --restart=always \
  nginx
'''


---


## NOTE:
Make sure to kee few thinga in mind to avoide mistakes I did:

In nginx proxy always keep **--restart=always** because if u forgot to add this code, then whenever u restary your OS the nginx service will stop and will require u to manually start it. 
I hope U wont like to suffer the same I did ^_^

- Make sure to add these lines in /etc/hosts to let know the host about the new connections:

'''text
127.0.0.1  dvwa.local
127.0.0.1  juiceshop.local
127.0.0.1  webgoat.local
'''

### Now you are READY to start your Web-Pentest Journey in a Safe environment !!
---
## Usage

Now open your browser and visit:

- 'http://dvwa.local'
- 'http://juiceshop.local'
- 'http://webgoat.local'


Thank You, regards Mr.Unknown ^--^
