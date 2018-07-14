---
title:  "Deploy Django Project in CentOS 7.2"
date:   2016-04-04 15:47:00 -0200
Categories: ["Articles"]
---

Today We will deploy my first project created with Django Framework 1.9 in CentOS 7.2. To do that, first I created a droplet with CentOS 7.2 installed. If you don't know how to create a droplet in Digital Ocean, see this <a href="https://www.digitalocean.com/community/tutorials/how-to-create-your-first-digitalocean-droplet-virtual-server" target="_blank">tutorial</a>. Lastly, to deploy the project, we will use Apache with mod_wsgi.

Now, let's start:

- First update repositories:
{{< highlight bash >}}
yum update -y
{{< / highlight >}}

- To get `pip`, we'll need to enable the EPEL repository, which as some additional packages. You can do that easily by typing:
{{< highlight bash >}}
sudo yum install epel-release
{{< / highlight >}}

- With EPEL enabled, we can install the components we need by typing:
{{< highlight bash >}}
sudo yum install python-pip httpd mod_wsgi 
{{< / highlight >}}

- Now, we will install Mysql for database project. So, let's download and install MySQL repository:
{{< highlight bash >}}
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm
yum update
{{< / highlight >}}

- After repositories is OK, let's install MySQL Server and Start it !
{{< highlight bash >}}
yum install mysql-server
systemctl start mysqld
{{< / highlight >}}

- With MySQL installed, we will configure using following command:
{{< highlight bash >}}
mysql_secure_installation
{{< / highlight >}}

- Follow steps configuration. Before we download the project from git, let's add user and put the project in user's folder. In this way, you can add others projects in others users, using one server. Substitute <username> in any user:
{{< highlight bash >}}
useradd <username>
passwd <username>
{{< / highlight >}}

- After added user, let's install git and download django project in user folder:
{{< highlight bash >}}
yum install git -y
cd /home/<username>
git clone <URL-Repository>
{{< / highlight >}}

- Using git clone, you will download all files project into your user's folder. Now, we use `virtualenv` and `virtualenvwrapper` to manage each project virtual enviroment. So let's install using `virtualenvwrapper`, because it will install `virtualenv` automatically:
{{< highlight bash >}}
pip install virtualenvwrapper
{{< / highlight >}}

- To bash CentOS recognize `virtualenvwrapper` commands, let's add 3 lines at ~/.bash_profile, so use command `nano ~/.bash_profile` and add this content:
{{< highlight bash >}}
export WORKON_HOME=$HOME/.virtualenvs
export PROJECT_HOME=$HOME/Devel
source /usr/bin/virtualenvwrapper.sh
{{< / highlight >}}

- After that, we will create our virtualenv and install all requeriments of your project in txt:
{{< highlight bash >}}
mkvirtualenv <nameproject>
yum install gcc python-devel mysql-devel libevent-devel
pip install -r requeriments.txt
{{< / highlight >}}

- Let's now create database for your project and import structure by Django migrations:
{{< highlight bash >}}
mysql -u root -p<PASSWORD>
	create database <databasename>;
	exit;

python manage.py migrate
{{< / highlight >}}

- Create a superuser Administration:
{{< highlight bash >}}
python manage.py createsuperuser
{{< / highlight >}}

- Now, we will collect static files, but you should certificate your `settings.py` STATIC_FILES is configured correctly:
{{< highlight bash >}}
python manage.py collectstatic
{{< / highlight >}}

- So, all it's ok ! We just need to configure `Apache` with `mod_wsgi`. To do that, we will create a file configuration at `/etc/httpd/conf.d/` with any name:
{{< highlight bash >}}
nano /etc/httpd/conf.d/django.conf
{{< / highlight >}}

- And put this content, replacing variables <> with your username or nameproject. The <envproject> is the name of VirtualEnv you created to your project.
{{< highlight bash >}}
<VirtualHost *:80>
    ServerName www.mydomain.com
    DocumentRoot /home/<username>/<nameproject>

    Alias /static /home/<username>/<nameproject>/static
    <Directory /home/<username>/<nameproject>/static>
        Require all granted
    </Directory>

    <Directory /home/<username>/<nameproject>/<nameproject>>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>

    WSGIDaemonProcess <nameproject> python-path=/home/<username>/<nameproject>:/<username>/.envs/<envproject>/lib/python2.7/site-packages
    WSGIProcessGroup <nameproject>
    WSGIScriptAlias / /home/<username>/<nameproject>/<nameproject>/wsgi.py

</VirtualHost>
{{< / highlight >}}

- Now we will put some permissions and we finish !
{{< highlight bash >}}
chmod 664 /home/<username>/<nameproject>
sudo chown :www-data /home/<username>/<nameproject>
{{< / highlight >}}

- To finish, we restart Apache !
{{< highlight bash >}}
sudo service apache2 restart
{{< / highlight >}}
