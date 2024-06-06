## README.md

This document outlines the steps to set up a Django application with Gunicorn, Supervisor, and Nginx on an Ubuntu server. ikka

### Prerequisites

- Ubuntu server
- Basic knowledge of Linux command line

### Step 1: Update and Upgrade the System

First, update and upgrade the system packages:

```bash
sudo apt-get update
sudo apt-get upgrade
```

### Step 2: Install Python and Create a Virtual Environment

Check the Python version and install the `python3-venv` package:

```bash
python3 --version
sudo apt-get install python3-venv
```

Create a virtual environment and activate it:

```bash
python3 -m venv env
source env/bin/activate
```

### Step 3: Install Django

Install Django within the virtual environment:

```bash
pip3 install django
```

### Step 4: Clone the Project Repository

Clone the Git repository of the project:

```bash
git clone https://github.com/manojsingh7838/z2h
```

### Step 5: Install and Configure Nginx

Install Nginx:

```bash
sudo apt-get install -y nginx
```

### Step 6: Install Gunicorn

Install Gunicorn within the virtual environment:

```bash
pip install gunicorn
```

### Step 7: Install and Configure Supervisor

Install Supervisor:

```bash
sudo apt-get install supervisor
```

Navigate to the Supervisor configuration directory and create a Gunicorn configuration file:

```bash
cd /etc/supervisor/conf.d/
sudo touch gunicorn.conf
sudo nano gunicorn.conf
```

Add the following configuration to `gunicorn.conf`:

```
[program:gunicorn]
directory=/home/ubuntu/z2h
command=/home/ubuntu/env/bin/gunicorn --workers 3 --bind unix:/home/ubuntu/z2h/app.sock letslearn.wsgi:application  
autostart=true
autorestart=true
stderr_logfile=/var/log/gunicorn/gunicorn.err.log
stdout_logfile=/var/log/gunicorn/gunicorn.out.log

[group:guni]
programs:gunicorn
```

Create the log directory for Gunicorn:

```bash
sudo mkdir /var/log/gunicorn
```

Update Supervisor to recognize the new configuration:

```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl status
```

### Step 8: Configure Nginx for Django

Navigate to the Nginx configuration directory and create a new configuration file for the Django project:

```bash
cd /etc/nginx/sites-available
sudo touch django.conf
sudo nano django.conf
```

Add the following configuration to `django.conf`:

```
server {
    listen 80;
    server_name 34.233.125.180 z2hstudy.in www.z2hstudy.in;

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/ubuntu/z2h/app.sock;
    }
}
```

Test the Nginx configuration:

```bash
sudo nginx -t
```

Enable the site by creating a symbolic link to the `sites-enabled` directory:

```bash
sudo ln -s /etc/nginx/sites-available/django.conf /etc/nginx/sites-enabled/
```

Restart Nginx to apply the changes:

```bash
sudo service nginx restart
```

### Step 9: Secure the Application with SSL

Use Certbot to obtain and install an SSL certificate:

```bash
sudo certbot --nginx -d z2hstudy.in -d www.z2hstudy.in
```

### Final Notes

Ensure that you replace `/home/ubuntu/z2h`, `/home/ubuntu/env`, and other paths with the actual paths used in your setup.

This completes the setup for deploying a Django application using Gunicorn, Supervisor, and Nginx on an Ubuntu server. Your application should now be running and accessible at `http://z2hstudy.in` and `http://www.z2hstudy.in`.
