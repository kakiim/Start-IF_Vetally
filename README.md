# Start-IF_Vetally



1. Installer les dépendances
sudo apt update
sudo apt install -y git wget curl build-essential apache2 mariadb-server mariadb-client \
php php-cli php-common php-curl php-mysql php-mbstring php-xml php-gd php-bcmath php-zip \
php-intl php-soap php-ldap nodejs npm sox
2. Installer Asterisk
cd /usr/src
sudo git clone https://gerrit.asterisk.org/asterisk asterisk-20
cd asterisk-20
sudo contrib/scripts/install_prereq install
sudo ./configure
sudo make -j$(nproc)
sudo make install
sudo make samples
sudo make config
sudo ldconfig

Activer Asterisk :

sudo systemctl enable asterisk
sudo systemctl start asterisk
3. Installer FreePBX
cd /usr/src
sudo git clone https://github.com/freepbx/freepbx.git
cd freepbx
sudo ./start_asterisk start
sudo ./install -n
