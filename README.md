# Start-IF_Vetally

## 1. Installer les dépendances

```bash
sudo apt update
sudo apt install -y git wget curl build-essential apache2 mariadb-server mariadb-client \
  php php-cli php-common php-curl php-mysql php-mbstring php-xml php-gd php-bcmath php-zip \
  php-intl php-soap php-ldap nodejs npm sox libedit-dev
```

## 2. Installer Asterisk

```bash
cd /usr/src
sudo git clone -b 20 https://github.com/asterisk/asterisk.git asterisk-20
cd asterisk-20
sudo contrib/scripts/install_prereq install
sudo ./configure
sudo make -j$(nproc)
sudo make install
sudo make samples
sudo make config
sudo ldconfig
```

### Activer Asterisk

```bash
sudo systemctl enable asterisk
sudo systemctl start asterisk
```

## 3. Installer FreePBX 17

### Option A : Script officiel Sangoma (Debian 12 uniquement)

```bash
cd /tmp
wget https://github.com/FreePBX/sng_freepbx_debian_install/raw/master/sng_freepbx_debian_install.sh -O /tmp/sng_freepbx_debian_install.sh
bash /tmp/sng_freepbx_debian_install.sh
```

Options disponibles :

| Option | Description |
|---|---|
| `--dahdi` | Installe avec le support DAHDI (cartes Sangoma) |
| `--opensourceonly` | Installe uniquement les modules open source |
| `--nofreepbx` | Prépare l'environnement sans installer FreePBX |
| `--noasterisk` | Installe sans Asterisk (pour utiliser votre propre version) |

### Option B : Installation manuelle (Ubuntu / Debian)

Télécharger et extraire FreePBX :

```bash
cd /usr/src
sudo wget http://mirror.freepbx.org/modules/packages/freepbx/freepbx-17.0-latest.tgz
sudo tar -xvzf freepbx-17.0-latest.tgz
cd freepbx/
sudo ./start_asterisk start
sudo adduser --system --group --home /var/lib/asterisk asterisk
sudo chown -R asterisk:asterisk /var/run/asterisk
sudo chown -R asterisk:asterisk /etc/asterisk
sudo chown -R asterisk:asterisk /var/{lib,log,spool}/asterisk
sudo chown -R asterisk:asterisk /usr/lib/asterisk
sudo pkill asterisk
cd /usr/src/freepbx
sudo ./start_asterisk start
ps aux | grep asterisk
sudo ./install -n
```

Configurer Apache pour FreePBX :

```bash
sudo mv /etc/apache2 /etc/apache2_backup
sudo apt purge apache2 apache2-bin apache2-utils apache2-data -y
sudo apt autoremove -y
sudo apt install apache2 -y
sudo systemctl start apache2
sudo nano /etc/apache2/envvars
```
Il faut chercher les lignes :
export APACHE_RUN_USER=www-data
export APACHE_RUN_GROUP=www-data

Et remplacer par :
export APACHE_RUN_USER=asterisk
export APACHE_RUN_GROUP=asterisk

(pour quitter faire ctrl+x puis Y puis entrer)

```bash
sudo a2enmod rewrite
sudo nano /etc/apache2/apache2.conf
```

Il faut chercher la ligne :
AllowOverride None

Et remplacer par :
AllowOverride All

(pour quitter faire ctrl+x puis Y puis entrer)

```bash
sudo systemctl restart apache2
```

> **Note :** Sur Ubuntu 22.04, PHP 8.2 n'est pas disponible par défaut. Il faut ajouter le PPA `ppa:ondrej/php` avant d'installer les dépendances PHP.
