# Configuration de Gunicorn/Nginx

##  

Les logiciels suivants doivent etre installes:

- Python
- Virtualenv
- nginx
- Django (installer a l'interieur de l'environnement virtuel)
- Gunicorn (installer a l'interieur de l'environnement virtuel)



## Test si Gunicorn peut servir le project Django

Garder l'environnement virtuel actif.

Aller dans le repertoire du projet django **/workspaces/web_project** et utiliser gunicorn pour charger le module WGSI du projet:

```
(env) $ cd ~/web_project 

(env) $ gunicorn --bind 0.0.0.0:8000 web_project.wgsi
```

Cela va demarer gunicorn sur la meme interface que le server Django.

> Note: The admin interface will not have any of the styling applied since Gunicorn does not know about the static CSS content responsible for this.

We passed Gunicorn a module by specifying the relative directory path to Django's wsgi.py file, which is the entry point to our application, using Python's module syntax. Inside of this file, a function called application is defined, which is used to communicate with the application. 

Une fois les tests ok quitter gunicorn : **CTRL + C**

Quitter l'environnement virtuel : **deactivate**



## Creation d'un fichier de service systemd gunicorn

Apres avoir teste que gunicorn peut interagir avec notre application Django, nous devons mettre en place un moyen plus sur de demarrer et de stopper l'application.

Pour cela nous allons creer un fichier de service systemd:

```
sudo nano /etc/systemd/system/gunicorn.service
```



Ajouter le contenu suivant:

```
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=sylvie
Group=www-data
WorkingDirectory=/home/sylvie/workspaces/web_project
ExecStart=/home/sylvie/workspaces/env/bin/gunicorn --access-logfile - --workers 3 --bind unix:/home/sylvie/workspaces/web_project/web_project.sock web_project.wsgi:application

[Install]
WantedBy=multi-user.target
```

Demarrage et activation du service gunicorn que nous venons de creer, ainsi il demarrera au boot:

```
sudo systemctl start gunicorn
sudo systemctl enable gunicorn
```

Pour savoir si cela s'est bien passe il faut verifier le fichier de socket.



## Verification du fichier de socket

Verifier le status du processus:

```
sudo systemctl status gunicorn
```

Si cette commande retourne une erreur, verifier les logs du processus gunicorn

```
sudo journalctl -u gunicorn
```

Ensuite verifier l'existence du fichier web_project.sock dans le dossier de votre projet:

```
ls /home/sylvie/workspaces/web_project
```

```
Output
manage.py  web_project  env  web_project.sock  static
```

Si vous apportez des modificationa au fichier **/etc/systemd/system/gunicorn.service** alors il faut recharger le daemon pour relire la definition du service. Puis redemarrer gunicorn:

```
sudo systemctl daemon-reload
sudo systemctl restart gunicorn
```



## Configurer Nginx pour le passage proxy vers gunicorn

Nous devons maintenant configurer nginx pour passer le trafic vers le processus gunicorn

Creer un nouveau block de serveur dans le dossier sites-available de Nginx:

```
sudo nano /etc/nginx/sites-available/web_project
```

Ajouter le contenu suivant:

```
server {
    listen 80;
    server_name 192.168.1.101;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        root /home/sylvie/workspaces/web_project;
    }

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/sylvie/workspaces/web_project/myproject.sock;
    }
}

```

Maintenant nous pouvons activer le fichier en le liant au dossier sites-available

```
sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled
```

Tester la configuration Nginx:

```
sudo nginx -t
```

S'il n'y a aucune erreur redemarrer Nginx:

```
sudo systemctl restart nginx
```

Enfin nous devons ouvrir le port 80 sur le pare-feu et fermer le port 8000:

```
sudo ufw delete allow 8000
sudo ufw allow 'Nginx Full'
```