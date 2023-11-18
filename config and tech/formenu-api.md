---
title: config formenu api
date: 2023-10-21
---

Pour permettre d'accéder a l'api depuis le scope "formenu.fr/api" j'ai modifier la config *nginx* (du projet formenu-website ) pour :

```json

<%
if (s.forceSsl) {
%>
    server {

        listen       80;

        server_name  <%-s.publicDomain%>;

        # Used by Lets Encrypt
        location /.well-known/acme-challenge/ {
            root <%-s.staticWebRoot%>;
        }

        # Used by CapRover for health check
        location /.well-known/captain-identifier {
            root <%-s.staticWebRoot%>;
        }

        location / {
            return 302 https://$http_host$request_uri;
        }
    }
<%
}
%>


server {

    <%
    if (!s.forceSsl) {
    %>
        listen       80;
    <%
    }
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

        
```edit
    location /api/ {
        rewrite ^/api/?(.*)$ /$1 break;
        proxy_pass http://srv-captain--api-formenu:1339;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto $scheme;
    }
```
```


        location / {


	<%
	if (s.redirectToPath) {
	%>
	    return 302 <%-s.redirectToPath%>;
	<%
	} else {
	%>

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



----
Import / export des données : 

> [Data import | Strapi Documentation](https://docs.strapi.io/dev-docs/data-management/import)
> [Data export | Strapi Documentation](https://docs.strapi.io/dev-docs/data-management/export)
> 
> `yarn strapi export --no-encrypt --exclude files`
> 
> `sudo docker cp d6c8097f943d:/app/export_20231118154431.tar.gz /home/CinquinAndy`
> 
> `scp CinquinAndy@andy-cinquin.fr:/home/CinquinAndy/export_20231118154431.tar.gz "C:\Users\andyq\DocumentsAndy\ProjetPro2023_2024\ForMenu\api-formenu"`
> 
> `yarn strapi import -f "C:\Users\andyq\DocumentsAndy\ProjetPro2023_2024\ForMenu\export formenu api\export_20231118154431.tar.gz"`

