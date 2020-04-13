# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  # Specify the base box
  config.vm.box = "ubuntu/bionic64"

  # Setup port forwarding
  config.vm.network "forwarded_port", guest: 27960, host: 27960, protocol: 'udp'
  config.vm.network "forwarded_port", guest: 80, host: 80, auto_correct: true

  # Sync folder setup
  config.vm.synced_folder "C:/Urbanterror43", "/var/opt/urbanterror"

  # Virtualbox setup
  config.vm.provider "virtualbox" do |vb|
    # Customize the amount of memory on the VM:
    vb.memory = 768
    vb.name = "Spunky Bot - Ubuntu 18.04"
  end

  # Provisioning
  $script = <<SCRIPT
echo "--> Installing systemd scripts..."
touch /etc/systemd/system/urbanterror.service && cat >> /etc/systemd/system/urbanterror.service <<'EOF'
[Unit]
Description=Urban Terror server
After=network.target

[Service]
User=vagrant
ExecStart=/var/opt/urbanterror/Quake3-UrT-Ded.x86_64 +set fs_game q3ut4 +set dedicated 2 +set use_defaultHomePath 0 +set fs_homepath /opt/urbanterror/.q3a +set net_port 27960 +set com_hunkmegs 128 +exec vagrantserver.cfg
StandardOutput=null
Type=simple
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

touch /etc/systemd/system/spunkybots.service && cat >> /etc/systemd/system/spunkybot.service <<'EOF'
[Unit]
Description=Spunky Bot
After=network.target

[Service]
User=vagrant
WorkingDirectory=/opt/spunkybot
ExecStart=/usr/bin/python spunky.py
StandardOutput=null
Type=simple
Restart=always

[Install]
WantedBy=multi-user.target
EOF

echo "--> Installing sysVinit scripts..."
touch /etc/init.d/urbanterror && cat >> /etc/init.d/urbanterror <<'EOF'
#!/bin/sh
#
### BEGIN INIT INFO
# Provides:          urbanterror
# Required-Start:    $network $local_fs
# Required-Stop:     $network $local_fs
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Urban Terror server
# Description:       Startup script for Urban Terror server
### END INIT INFO
set -e
# Get LSB functions
. /lib/lsb/init-functions
USER=vagrant
GROUP=vagrant
DAEMON=/var/opt/urbanterror/Quake3-UrT-Ded.x86_64
OPTIONS="+set fs_game q3ut4 +set dedicated 2 +set fs_homepath /opt/urbanterror/.q3a +set net_port 27960 +set com_hunkmegs 128 +exec vagrantserver.cfg"
# Check that the package is still installed
test -x $DAEMON || exit 0
case "$1" in
	start)
		log_begin_msg "Starting Urban Terror Server..."
		umask 002
		if start-stop-daemon --start --quiet --background --chuid $USER:$GROUP --exec $DAEMON -- $OPTIONS; then
			log_end_msg 0
		else
			log_end_msg $?
		fi
		;;
	stop)
		log_begin_msg "Stopping Urban Terror Server..."
		if start-stop-daemon --stop --quiet --oknodo --exec $DAEMON; then
				log_end_msg 0
		else
				log_end_msg $?
		fi
		;;
	restart)
		log_begin_msg "Restarting Urban Terror Server..."
		start-stop-daemon --stop --quiet --oknodo --exec $DAEMON
		sleep 3
		start-stop-daemon --start --quiet --background --chuid $USER:$GROUP --exec $DAEMON -- $OPTIONS
		log_end_msg $?
		;;
	*)
		echo "Usage: /etc/init.d/urbanterror {start|stop|restart}"
		exit 1
		;;
esac
exit 0
EOF

touch /etc/init.d/spunkybot && cat >> /etc/init.d/spunkybot <<'EOF'
#!/bin/sh
#
### BEGIN INIT INFO
# Provides:          spunkybot
# Required-Start:    $network $local_fs
# Required-Stop:     $network $local_fs
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Initscript for Spunky Bot
# Description:       Start/Stop/Restart the Spunky Bot service daemon
### END INIT INFO
DIR=/opt/spunkybot
USER=vagrant
GROUP=vagrant
DAEMON=$DIR/spunky.py
NAME=spunkybot
RUNDIR=/var/run/$NAME
PIDFILE=$RUNDIR/$NAME.pid
# Get LSB functions
. /lib/lsb/init-functions
# See if the daemon is present
if [ -e "$DAEMON" ]
	then
		chmod +x "$DAEMON"
		chown -R "$USER:$GROUP" "$DIR"
	fi
# Make sure /var/run/spunkybot exists
test -d "$RUNDIR" || {
	mkdir -p "$RUNDIR"
	chown -R "$USER:$GROUP" "$RUNDIR"
}
case "$1" in
	start)
		if [ -e "$PIDFILE" ]
		then
			echo "$0:"
			echo "  Another instance of \"${DAEMON}\" seems to be running."
			echo "  If this is not the case, please remove" $PIDFILE
			exit 1
		fi
		log_begin_msg "Starting service $NAME..."
		umask 002
		if start-stop-daemon --start --quiet --background --chdir $DIR --chuid $USER:$GROUP --pidfile "$PIDFILE" --make-pidfile --exec $DAEMON; then
			log_end_msg 0
		else
			log_end_msg $?
		fi
		;;
	stop)
		log_begin_msg "Stopping service $NAME..."
		if start-stop-daemon --stop --quiet --pidfile "$PIDFILE" && rm -f "$PIDFILE"; then
			log_end_msg 0
		else
			log_end_msg $?
		fi
		;;
	restart)
		$0 stop
		sleep 3
		$0 start
		;;
	*)
		echo "Usage: $0 {start|stop|restart}" >&2
		exit 1
		;;
esac
exit 0
EOF

chmod +x /etc/init.d/urbanterror /etc/init.d/spunkybot

echo "--> Downloading latest Spunky Bot version..."
mkdir -p /opt/spunkybot
wget -qO- https://github.com/SpunkyBot/spunkybot/archive/master.tar.gz | tar -xz --strip-components=1 --directory=/opt/spunkybot
chown -R vagrant:vagrant /opt/spunkybot
chmod +x /opt/spunkybot/spunky.py

echo "--> Installing Urban Terror config files..."
mkdir -p /opt/urbanterror/.q3a/q3ut4
touch /opt/urbanterror/.q3a/q3ut4/autoexec.cfg
touch /opt/urbanterror/.q3a/q3ut4/mapcycle.txt && cat >> /opt/urbanterror/.q3a/q3ut4/mapcycle.txt <<'EOF'
ut4_turnpike
ut4_abbey
ut4_algiers
EOF

touch /opt/urbanterror/.q3a/q3ut4/vagrantserver.cfg && cat >> /opt/urbanterror/.q3a/q3ut4/vagrantserver.cfg <<'EOF'
seta  g_motd         "Powered by www.spunkybot.de"
seta  rconpassword   "secretpassword"
seta  sv_hostname    "Spunky Bot"
seta  sv_joinmessage "Powered by Spunky Bot"
seta  auth_enable    "0"
seta  g_logsync      "1"
seta  g_loghits      "1"
seta  g_mapcycle     "mapcycle.txt"
seta  g_gametype     "4"
seta  g_matchmode    "0"
seta  g_friendlyfire "2"
seta  timelimit      "10"
map  ut4_turnpike
reload
EOF

chown -R vagrant:vagrant /opt/urbanterror

echo "--> Provisioning virtual machine..."
apt-get update -q

echo "--> Installing python2, nginx and php7.2..."
apt-get install -y -q -f python-minimal nginx php7.2-fpm php7.2-common php7.2-sqlite3

echo "--> Configuring nginx..."
touch /etc/nginx/sites-available/nginx_vhost && cat >> /etc/nginx/sites-available/nginx_vhost <<'EOF'
server {
    listen 80;
    server_name localhost;
    root /var/www/;
    index index.php;
    sendfile off;
    location / {
        try_files $uri $uri/ =404;
    }
    location ~* \.php {
        try_files $uri =404;
        include fastcgi_params;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock; 
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_cache off;
        fastcgi_index index.php;
    }
}
EOF

ln -s /etc/nginx/sites-available/nginx_vhost /etc/nginx/sites-enabled/
rm -rf /etc/nginx/sites-enabled/default && rm -rf /var/www/html

echo "--> Downloading latest PRISM version..."
wget -qO- https://github.com/SpunkyBot/PRISM/archive/master.tar.gz | tar -xz --strip-components=1 --directory=/var/www
chown -R vagrant:vagrant /var/www

echo "--> Allowing SSH password authentication..."
sed -i "s/^PasswordAuthentication no/PasswordAuthentication yes/g" /etc/ssh/sshd_config

echo "--> Restarting services..."
service ssh restart && service nginx restart && service php7.2-fpm restart

SCRIPT

  config.vm.provision "shell", inline: $script

end