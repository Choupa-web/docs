# APPLICATION POTAGER

## INSTALLATION ET CONFIGURATION POSTGRESQL



```
sudo apt-get update
```

```
sudo apt install postgresql postgresql-contrib
```



### Creation de la base de donnees

```
sudo -u postgres psql
```

```
postgres=# CREATE DATABASE choupa_db;
```



### Creation d'un utilisateur pour le projet potager

```
postgres=# CREATE USER sylvie WITH PASSWORD 'Radclyff_001_27';
```

```
 postgres=#  ALTER ROLE sylvie SET client_encoding TO 'utf8';
 postgres=#  ALTER ROLE sylvie SET default_transaction_isolation TO 'read committed';
 postgres=#  ALTER ROLE sylvie SET timezone TO 'UTC';

```

```
postgres=# GRANT ALL PRIVILEGES ON DATABASE choupa_db TO sylvie;
```



## DJANGO CONFIGURATION

### Postgresql

Settings.py:

```
'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'choupa_db',
        'USER': 'sylvie',
        'PASSWORD': 'Radclyff_001_27',
        'HOST': 'localhost',
        'PORT': '5432',
```



### Super user

name: potager

mot de passe : Radclyff27

