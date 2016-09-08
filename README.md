# Djansible

## Ansible Playbook

This playbook designed for environments running a Django app. It can work with the following technologies:

- Django
	- [django-decouple](http://github.com/henriquebastos/python): Strict separation of settings from code
- Git
- MySQL
- Nginx
- Gunicorn
- Supervisor

**This playbook supports**

- Multiple Settings Configuration
- Multiple Requirements Files



**What this playbook do?**

- Installs system and extra packages.
- Installs mysqlserver and configures it.
- Clone an existent git repository .
- Creates a virtualenv with Python 3 and install requirements.txt (according to the environment)
- Configures a secret environment with python-decouple like:
- Configures django: makemigrations, migrate, collectstatic and if needs will run initial fixtures
- Installs gunicorn and supervisor and configures.
- Installs nginx server and configures.

## Why do you use ansible?


#### The excuse that works only on my computer doesn't work more!!!!

Because, you will reduce the time it takes to configurate the application in the server.

In few minutes or seconds, you can provision a fully working server!
Using ansible, we have dramatically reduced the time it takes us to deliver applications into production, from weeks to days and even hours.


## What django project template is recommended for this playbook?

&nbsp;
#### The project structure should look like this:

	project_name/
		application_name/
			apps/
               __init__.py
        	settings/
        	 	__init__.py
 				base.py
				local.py
				production.py
			__init__.py
			urls.py
			wsgi.py
		requirements/
			base.txt
			dev.txt
			production.txt
		venv/
		manage.py	
		README.md
		.gitignore
		
		
**If you don't follow this structure, don't WORRY!** Read the section to customize this playbook for you!



## How to run this playbook?

First of all you need to have Python 2.7 installed, because Ansible only works with this python version.

After, install ansible (Make sure that you have pip installed)

```
$ pip install ansible
```

So, clone this repository

```
$ git clone https://github.com/leonardocouy/djansible
```

## Keep a Calm and Let's go configure this playbook for your project

&nbsp;

### Structure

- **env_vars:** this folder has environment variables, if you need a private variable to some environment then you need to put in the specific environment file. **Base.yml** **has general settings for all environments**

- **roles:** this folder has playbook roles, **like a puzzle**, what does that mean? This means that folder contains everything our application needs.** Like:  **nginx, common operations, mysql, gunicorn and project configuration

- **hosts:** Host addresses that you will deploy must be here.

- **production.yml:** Configuration to deploy the application to production server. You can specify the root user, var files and roles that you like

- **staging.yml:** Configuration to deploy the application to staging server. You can specify the root user, var files and roles that you like

- **vagrant.yml:** Configuration to deploy the application to vagrant local server. You can specify the root user, var files and roles that you like

### Configuring this playbook for your project

####General Settings
- **env_vars/base.yml:**
	- **project_name:** name of the root folder **like:** blog
	- **application_name:** name of the folder that contains apps, wsgi.py, settings
	- **server_domain:** put your hosts addresses according to the environment
	- **load_initial_data:** if you are not using initial data, change to NO this option. (*Automatically will ignore the initial_data_file variable*)
	- **python_decouple**: **!!ATTENTION!!!** if you are not using python_decouple, change to NO this option. But, you need enable the commented variable **DJANGO_SECRET_KEY** and put your secret key. Furthermore, you should comment the following vars:
		- **django_debug**
		- **allowed_hosts**
	- **allowed_hosts:** put your hosts addresses allowed according to the environment (ONLY IF YOU ARE USING PYTHON_DECOUPLE)
	
---

####MySQL Settings
- **roles/mysql/vars/main.yml:**
	- **db_name:** Put your Database Name
	- **db_user:** Put your Database User
	- **db_pass:** Put your User Password
	
---

####Git Settings
- **roles/project/defaults/main.yml:**
	- **git_repo:** Put your Github Repository
	- **ssh_github:** If you would like to use HTTPS rather than SSH, change to NO
	- **is_private_repo:** If your repository isn't private, so you should change to NO
	- **compress_static_files:** If you are not using django-compressor, change to NO

---

## How to run?

Provision local machine with vagrant

```
vagrant up
```

**ACCESS YOUR DJANGO APPLICATION BY GOING TO THIS URL: **[http://192.168.4.23](http://192.168.4.23)

Re-provision the box to apply changes.

```
vagrant provision
```

**SSH to the box**

```
vagrant ssh
```


## DEPLOY!

Provision the staging server

```
ansible-playbook -i hosts staging.yml
```

Provision the production server

```
ansible-playbook -i hosts production.yml
```

## Python-decouple

### .env configuration

Go to **roles/project/templates/.env.j2** and change this template if you would like to put more environment variables.

- **SECRET_KEY:** This var gets a output secret_key generated by a script
- **DATABASE_URL:** If you are not using dj-database-url you must remove this and put your database variables in this template.
- **COMPRESS_OFFLINE:** If you are not using django-compressor you should remove this.


## Useful Links

- [Ansible-Django-Stack](https://github.com/jcalazan/ansible-django-stack)
- [How To Use MySQL or MariaDB with your Django Application on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-use-mysql-or-mariadb-with-your-django-application-on-ubuntu-14-04)
- [Setting up Django with Nginx, Gunicorn, virtualenv, supervisor and PostgreSQL](http://michal.karzynski.pl/blog/2013/06/09/django-nginx-gunicorn-virtualenv-supervisor/)