# Déploiement du sous-domaine api-ocr.valomnia.com avec Nginx et SSL

## 1. Création du sous-domaine DNS

Accéder au provider DNS (GoDaddy) et créer un enregistrement :


| Type | Nom      | Valeur             |
| ---- | -------- | ------------------ |
| A    | api-ocr  | 137.74.116.178     |


## 2. Installation Nginx Configuration du sous-domaine 
* Installer Nginx
`sudo apt install nginx -y`
* Créer le fichier de configuration 
`sudo nano /etc/nginx/sites-available/api-ocr.valomnia.com`
* Ajouter le contenu suivant 
```
server {
    server_name api-ocr.valomnia.com;

    location = / {
        return 302 /login;
    }

    location / {
        proxy_pass http://127.0.0.1:8001;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

* Activer le site et recharger Nginx
```
sudo ln -s /etc/nginx/sites-available/api-ocr.valomnia.com /etc/nginx/sites-enabled/
sudo systemctl reload nginx
```

## 3. Installation et configuration du certificat SSL avec Certbot
* Installer Certbot et le plugin Nginx
`sudo apt install certbot python3-certbot-nginx -y`
* Générer le certificat pour le domaine
`sudo certbot --nginx -d api-ocr.valomnia.com`
Pendant l’installation :
1. Email : houda.mabrouk@valomnia.com
2. Terms : Accepter (Y)
3. Redirect HTTP → HTTPS : Oui ✅

* Tester le renouvellement automatique
`sudo certbot renew --dry-run`
* Les certificats sont stockés dans 
`/etc/letsencrypt/live/api-ocr.valomnia.com/`