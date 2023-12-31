---
title: portefolio andy
date: 2023-10-22
---


> [!abstract]+ Présentation
> 
> Portefolio de Andy fait avec NextJS & Strapi 

> [!example]+ Liens et ressources liées
> 
> - **Discord** : 
> 	- [Portefolio Andy Liens discord (.env & github CI)](https://discord.com/channels/749294142114496646/1130620313823088732)
> - **CapRover** : 
> 	- [CapRover API](https://captain.beta.andy-cinquin.fr/#/apps/details/andycinquin-api-portefoliov6)
> 	- [CapRover PgSQL](https://captain.beta.andy-cinquin.fr/#/apps/details/andycinquin-pgsql-portefoliov6)
> 	- [CapRover Website](https://captain.beta.andy-cinquin.fr/#/apps/details/andycinquin-portefoliov6)
> - **GitHub** : 
> 	- [Projet de mon Portefolio sur Github](https://github.com/CinquinAndy/PortefolioV6)
> 	- [Projet de l'API de mon portefolio sur Github](https://github.com/CinquinAndy/api-PortefolioV6)
> - **Website** : 
> 	- [version FR Portefolio](https://andy-cinquin.fr/)
> 	- [version EN Portefolio](https://andy-cinquin.com/)
> 	- [api Portefolio](https://api.andy-cinquin.fr/admin)
> - **Gestion de projet** : 
> 	- https://github.com/users/CinquinAndy/projects/8
> - **DNS** : 
> 	- [OVH dns Portefolio (.fr)](https://www.ovh.com/manager/#/web/zone/andy-cinquin.fr)
> 	- [OVH dns Portefolio (.com)](https://www.ovh.com/manager/#/web/domain/andy-cinquin.com/information)


---

contact@andy-cinquin.fr

---
### Conf nginx caprover
```
# !!! Alerte chimie ici !!! ( pour faire fonctionner les images, besoin de bidouiller le proxy pass header )
# Redirection for www.andy-cinquin.com to andy-cinquin.com
server {
    listen 80;    
    listen [::]:80;
    server_name www.andy-cinquin.com;
    return 301 https://andy-cinquin.com$request_uri;
}

# Redirection for www.andy-cinquin.fr to andy-cinquin.fr
server {
    listen 80;    
    listen [::]:80;
    server_name www.andy-cinquin.fr andycinquin-portefoliov6.beta.andy-cinquin.fr;
    return 301 https://andy-cinquin.fr$request_uri;
}

# Original server block for handling non-www requests and other subdomains
server {
    listen 80;    
    listen [::]:80;
    server_name andy-cinquin.fr andy-cinquin.com;
    return 301 https://$host$request_uri;
}

# SSL Redirection for https://www.andy-cinquin.com to https://andy-cinquin.com
server {
    listen              443 ssl http2;
    ssl_certificate     <%-s.crtPath%>;
    ssl_certificate_key <%-s.keyPath%>;
    server_name www.andy-cinquin.com;
    return 301 https://andy-cinquin.com$request_uri;
}

# SSL Redirection for https://www.andy-cinquin.fr to https://andy-cinquin.fr
server {
    listen              443 ssl http2;
    ssl_certificate     <%-s.crtPath%>;
    ssl_certificate_key <%-s.keyPath%>;
    server_name www.andy-cinquin.fr andycinquin-portefoliov6.beta.andy-cinquin.fr;
    return 301 https://andy-cinquin.fr$request_uri;
}

server { 
    <%
    if (s.hasSsl) {
    %>
        listen              443 ssl http2;
        ssl_certificate     <%-s.crtPath%>;
        ssl_certificate_key <%-s.keyPath%>;
    <%
    }
    %>

        client_max_body_size 500m;

        server_name  <%-s.publicDomain%>;

        # 127.0.0.11 is DNS set up by Docker, see:
        # https://docs.docker.com/engine/userguide/networking/configure-dns/
        # https://github.com/moby/moby/issues/20026
        resolver 127.0.0.11 valid=10s;
        # IMPORTANT!! If you are here from an old thread to set a custom port, you do not need to modify this port manually here!!
        # Simply change the Container HTTP Port from the dashboard HTTP panel
        set $upstream http://<%-s.localDomain%>:<%-s.containerHttpPort%>;

        location / {

    <%
    if (s.httpBasicAuthPath) {
    %>
            auth_basic           "Restricted Access";
            auth_basic_user_file <%-s.httpBasicAuthPath%>; 
    <%
    }
    %>

            proxy_pass $upstream;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

    <%
    if (s.websocketSupport) {
    %>
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_http_version 1.1;
    <%
    }
    %>
        }

        # Used by Lets Encrypt
        location /.well-known/acme-challenge/ {
            root <%-s.staticWebRoot%>;
        }
        
        # Used by CapRover for health check
        location /.well-known/captain-identifier {
            root <%-s.staticWebRoot%>;
        }

        error_page 502 /captain_502_custom_error_page.html;
        location = /captain_502_custom_error_page.html {
                root <%-s.customErrorPagesDirectory%>;
                internal;
        }
}
```