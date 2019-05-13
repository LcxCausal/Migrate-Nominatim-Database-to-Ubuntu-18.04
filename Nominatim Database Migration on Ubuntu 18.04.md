# __Nominatim Database Migration on _`Ubuntu 18.04`___

## __1. Installing the Required Softwares__

```
sudo apt update -y
sudo apt upgrade -y
sudo apt autoremove -y

sudo apt install -y build-essential cmake g++ libboost-dev libboost-system-dev \
                        libboost-filesystem-dev libexpat1-dev zlib1g-dev libxml2-dev\
                        libbz2-dev libpq-dev libproj-dev \
                        postgresql-server-dev-10 postgresql-10-postgis-2.4 \
                        postgresql-contrib-10 \
                        apache2 php php-pgsql libapache2-mod-php php-pear php-db \
                        php-intl git

sudo apt install -y python3-setuptools python3-dev python3-pip \
                        python3-psycopg2 python3-tidylib phpunit php-cgi

pip3 install --user behave nose
sudo pear install PHP_CodeSniffer
```

## __2. System Configuration__

### __2.1 Creating Dedicated User Accounts__

```
sudo useradd -d /srv/nominatim -s /bin/bash -m nominatim

export USERNAME=nominatim
export USERHOME=/srv/nominatim

sudo chmod a+x $USERHOME
```

### __2.2 Setting up PostgreSQL__

#### Start the postgresql service

```
sudo systemctl start postgresql
```

#### Edit postgresql configuration to allow remote access, including _`postgresql.conf`_ and _`pg_hba.conf`_

__`postgresql.conf:`__ /etc/postgresql/10/main/postgresql.conf

```
#listen_addresses = 'localhost'
listen_addresses = '*'

#shared_buffers = 128MB
shared_buffers = 12288MB

#work_mem = 4MB
work_mem = 1024MB

#maintenance_work_mem = 64MB
maintenance_work_mem = 2048MB

#fsync = on
fsync = off

#synchronous_commit = on
synchronous_commit = off
```

__`pg_hba.conf:`__ /etc/postgresql/10/main/pg_hba.conf

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD
host	all		all		0.0.0.0/0		md5
```

#### Set user _`"postgres"`_ password

```
sudo passwd postgres
```

#### Create postgresql user and database

```
sudo -s -u postgres
psql

CREATE USER nominatim WITH PASSWORD 'nominatim';
CREATE DATABASE nominatim;
GRANR ALL PRIVILEGES ON DATABASE nominatim to nominatim;
ALTER USER nominatim WITH SUPERUSER;
\q
```

#### Restart the postgresql service

```
sudo systemctl restart postgresql
```

### __2.3 Installing Nominatim__

```
su ubuntu

cd $USERHOME
wget https://nominatim.org/release/Nominatim-3.2.0.tar.bz2
tar xf Nominatim-3.2.0.tar.bz2
cd Nominatim-3.2.0

mkdir build
cd build
cmake ..
make
```

## __3. Restore _`Nominatim Database Backup File`___

### __3.1 Download _[pgAdmin](https://www.pgadmin.org/download/)_ client__

### __3.2 Remote access postgreSQL Database by pgAdmin client__

### __3.3 Select _`nominatim`_ database and restore `Backup File`__


