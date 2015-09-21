[![Deploy](http://media.djangopony.com/img/small/badge.png)](https://www.djangoproject.com/)
[![Deploy](https://assets.digitalocean.com/blog/sammy-cleaning-up.png)](https://www.digitalocean.com)

# django skeleton project for deploy in digitalocean

1. Clone the repository on you local environment
2. Create a Digital ocean account if you don't have one
3. Add you ssh key to your digital ocean account

```
    $ pbcopy < ~/.ssh/id_rsa.pub
```
4. Paste your ssh into digital ocean

## Create Digital Ocean Droplet
1. Give it name
2. Select your droplet size
3. Select Region
4. Select application in this case Django on 14.04
5. select your ssh key

### Setup your droplet
*  connect to you droplet via ssh
```
    $ssh root@<droplet IP address> (use your droplet IP address)
```
## Database configuration
* Capture the DB password and *update your settings* (when you log in you can see this information in the terminal)
* Then drop and create the existing database
```
    $ sudo su - postgres
    $ dropdb django
    $ createdb django
    $ exit
   ```
  
  ## Setup the git repo
```
    $ apt-get install git
    $ cd /home/django
    $ mkdir repo
    $ cd repo
    $ git init --bare
    $ cd hooks
```
##  Create or edit your post-receive with the script below
``` 
nano post-recieve
```

```
    #!/bin/sh
    git --work-tree=/home/django/django_project --git-dir=/home/django/repo checkout -f

    pip install -r /home/django/django_project/requirements.txt

    python /home/django/django_project/manage.py migrate --noinput

    service gunicorn restart
```

#  Make the hook executable
```
    $ chmod +x post-receive
```
# Add production remote to local environment
 
```
    $ git remote add production ssh://root@<Droplet Ip adrees>/home/django/repo
```

#  Push code to new production remote
```
    $ git push production master
````
##  if that fails, use this
```
        $ git push production master --force
```

## Create your superuser
* Connected via ssh
```
  ssh root@<droplet IP address> (use your droplet IP address)
  ```
  * Go into the project directory
 
```
   cd /home/django/django-project/
  ./manage.py loaddata fixtures/user.json
```
* Go to the browser
```
    your.ip.adress/admin
```
    User: admin
    Password: admin
