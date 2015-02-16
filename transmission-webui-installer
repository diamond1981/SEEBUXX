#!/bin/bash
# Script Name: AtoMiC Transmission WebUI installer
# Author: Anand Subramanian
# Initial Release: June 21, 2014
# Publisher: http://www.htpcBeginner.com
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
# 
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307  USA
#

# DO NOT EDIT ANYTHING UNLESS YOU KNOW WHAT YOU ARE DOING.
YELLOW="\033[1;33m"
RED="\033[0;31m"
ENDCOLOR="\033[0m"
CYAN="\e[96m"
SCRIPTPATH=$(pwd)

clear
echo 
echo -e $RED
echo -e " ┬ ┬┬ ┬┬ ┬ ┬ ┬┌┬┐┌─┐┌─┐┌┐ ┌─┐┌─┐┬┌┐┌┌┐┌┌─┐┬─┐ ┌─┐┌─┐┌┬┐"
echo -e " │││││││││ ├─┤ │ ├─┘│  ├┴┐├┤ │ ┬│││││││├┤ ├┬┘ │  │ ││││"
echo -e " └┴┘└┴┘└┴┘o┴ ┴ ┴ ┴  └─┘└─┘└─┘└─┘┴┘└┘┘└┘└─┘┴└─o└─┘└─┘┴ ┴"
echo -e $CYAN
echo -e "                __     __           "
echo -e "  /\ |_ _ |\/|./      (_  _ _. _ |_ "
echo -e " /--\|_(_)|  ||\__    __)(_| ||_)|_ "
echo -e "                              |     "
echo -e $YELLOW
echo -e "AtoMiC Transmission Installer Script"
echo -e $ENDCOLOR
echo 
echo -e $YELLOW
echo -e '--->Transmission installation will start soon. Please read the following carefully.'
echo -e $ENDCOLOR
echo -e '1. The script has been confirmed to work on Ubuntu variants, Mint, and Ubuntu Server.'
echo -e '2. While several testing runs identified no known issues, www.htpcBeginner.com or the author cannot be held accountable for any problems that might occur due to the script.'
echo -e '3. If you did not run this script with sudo, you maybe asked for your root password during installation.'
echo -e '4. By proceeding you authorize this script to install any relevant packages required to install and configure Transmission.'
echo -e '5. Best used on a clean system (with no previous Transmission install) or after complete removal of previous Transmission installation.'
echo -e '6. If this script worked for you please visit www.htpcBeginner.com and comment on the post to let others know.'

echo

read -p "Press y/Y and enter to AGREE and continue with the installation or any other key to exit: "
RESP=${REPLY,,}
if [ "$RESP" != "y" ]
then
	echo -e $RED
	echo -e "So you chickened out. May be you will try again later."
	echo -e $ENDCOLOR
	exit 0
fi

echo 

read -p "Enter the username of the user you want to run Transmission as. Typically, this is your username (IMPORTANT! Ensure correct spelling and case): "
UNAME=${REPLY,,}

if [ ! -d "/home/$UNAME" ]; then
  echo -e $RED
  echo -e "Bummer! You may not have entered your username correctly. Exiting now. Please rerun script."
  echo -e $ENDCOLOR
  echo
  exit 0
fi

echo

echo -e $YELLOW
echo -e "--->Refreshing packages list..."
echo -e $ENDCOLOR
sudo apt-get update

echo
sleep 1

echo -e $YELLOW
echo -e "--->Installing prerequisites..."
echo -e $ENDCOLOR
sudo apt-get -y install python-software-properties

echo
sleep 1

echo -e $YELLOW
echo -e "--->Adding Transmission repository. Press ENTER when asked..."
echo -e $ENDCOLOR
GREPOUT=$(grep ^ /etc/apt/sources.list /etc/apt/sources.list.d/* | grep transmissionbt)
if [ "$GREPOUT" == "" ]; then
    sudo add-apt-repository ppa:transmissionbt/ppa
else
    echo "Transmission PPA already exists..."
fi

echo
sleep 1

echo -e $YELLOW
echo -e "--->Refreshing packages list..."
echo -e $ENDCOLOR
sudo apt-get update

echo
sleep 1

echo -e $YELLOW
echo -e "--->Install the Transmission desktop GUI as well?"
echo -e $ENDCOLOR

read -p "Do you want the desktop version installed in addition to commandline and web interface (y/Y or any other key for No)? (IMPORTANT! Desktop version requires a Desktop Environment. If this is a headless server or if you do not want a desktop GUI then press any other key): "
TMVER=${REPLY,,}

if [ "$TMVER" != "y" ]
then
	echo -e $YELLOW
	echo -e "--->Installing Transmission commandline, and web interface..."
	echo -e $ENDCOLOR
	sudo apt-get -y install transmission-cli transmission-common transmission-daemon
else 
	echo -e $YELLOW
	echo -e "--->Installing Transmission desktop GUI, commandline, and web interface..."
	echo -e $ENDCOLOR
	RES=`ps ax | grep -v grep | grep -i xorg`
	if [ $? -eq 0 ]; 
	then
		sudo apt-get -y install transmission-gtk transmission-cli transmission-common transmission-daemon
	else 
		echo -e $RED
		echo -e "Bummer! You do not have a Desktop Environment installed. So installing only commandline and web interface...."
		echo -e $ENDCOLOR
		sudo apt-get -y install transmission-cli transmission-common transmission-daemon
	fi
fi

echo 
sleep 1

echo -e $YELLOW
echo -e "--->Stopping Transmission temporarily..."
echo -e $ENDCOLOR
sudo /etc/init.d/transmission-daemon stop > /dev/null 2>&1

echo 
sleep 1

echo -e $YELLOW
echo -e "--->Creating download directories..."
echo -e $ENDCOLOR
if [ ! -d "/home/$UNAME/.config" ]; then
	mkdir /home/$UNAME/.config
fi
if [ ! -d "/home/$UNAME/.config/transmission" ]; then
	mkdir /home/$UNAME/.config/transmission
fi
if [ ! -d "/home/$UNAME/Downloads" ]; then
	mkdir /home/$UNAME/Downloads
fi
if [ ! -d "/home/$UNAME/Downloads/transmission" ]; then
	mkdir /home/$UNAME/Downloads/transmission
fi
if [ ! -d "/home/$UNAME/Downloads/transmission/incomplete" ]; then
	mkdir /home/$UNAME/Downloads/transmission/incomplete
fi

sleep 1
echo 

echo -e $YELLOW
echo -e "--->Making some configuration changes..."
echo -e $ENDCOLOR
sudo sed -i 's/USER=debian-transmission/USER='$UNAME'/g' /etc/init.d/transmission-daemon  || { echo 'Replacing daemon username failed.' ; exit 1; }
sudo sed -i 's|/var/lib/transmission-daemon/info|/home/'$UNAME'/.config/transmission|g' /etc/default/transmission-daemon  || { echo 'Replacing config directory failed.' ; exit 1; }

sleep 1
echo 

echo -e $YELLOW
echo -e "--->Copying settings file and setting permissions..."
echo -e $ENDCOLOR
mv $SCRIPTPATH/transmission-initial-settings.json /home/$UNAME/.config/transmission/settings.json || { echo 'Initial settings move failed.' ; exit 1; }
cd /home/$UNAME/.config/transmission
sudo usermod -a -G debian-transmission $UNAME
sudo chown $UNAME:debian-transmission settings.json  || { echo 'CHOWN Failed' ; exit 1; }
sudo rm /var/lib/transmission-daemon/info/settings.json > /dev/null 2>&1
sudo ln -s /home/$UNAME/.config/transmission/settings.json /var/lib/transmission-daemon/info/settings.json || { echo 'Creating symbolic link failed.' ; exit 1; }
sudo chown -R $UNAME:debian-transmission /home/$UNAME/Downloads/transmission
sudo chown -R $UNAME:debian-transmission /home/$UNAME/.config/transmission
sudo chmod -R 775 /home/$UNAME/Downloads/transmission
sudo chmod -R 775 /home/$UNAME/.config/transmission
sudo chmod -R 775 /var/lib/transmission-daemon

echo 
sleep 1

echo -e $YELLOW
echo -e "--->Setting up download, incomplete, and watched folders..."
echo -e $ENDCOLOR
sed -i 's|USER_NAME|'$UNAME'|g' /home/$UNAME/.config/transmission/settings.json || { echo 'Replacing username in settings-json failed.' ; exit 1; }

read -p 'Set a username for Transmission web interface (if you forget this you can edit /home/'$UNAME'/.config/transmission/settings.json file) : '
TUNAME=${REPLY,,}
sed -i 's|WEBUI_USERNAME|'$TUNAME'|g' /home/$UNAME/.config/transmission/settings.json || { echo 'Setting new username in settings.json failed.' ; exit 1; }

read -p 'Set a password Transmission web interface (if you forget this you can edit /home/'$UNAME'/.config/transmission/settings.json file) : '
TPASS=${REPLY,,}
sed -i 's|WEBUI_PASSWORD|'$TPASS'|g' /home/$UNAME/.config/transmission/settings.json || { echo 'Setting new password in settings.json failed.' ; exit 1; }

echo 
sleep 1

echo -e $YELLOW
echo -e "--->Enabling autostart during boot..."
echo -e $ENDCOLOR
sudo update-rc.d transmission-daemon defaults

echo -e $YELLOW
echo -e "--->Starting Transmission..."
echo -e $ENDCOLOR
sudo /etc/init.d/transmission-daemon start /dev/null 2>&1
sudo /etc/init.d/transmission-daemon reload

echo 
sleep 2

echo -e $YELLOW
echo -e "--->All done. You MUST reboot your computer for Transmission to work."
echo -e $ENDCOLOR
echo 'Transmission should autostart on reboot. If not run "sudo /etc/init.d/transmission-daemon start".'
echo 'Then open http://localhost:9091 or http://IPADDRESS:9091 in your browser. The default username and password are both "transmission".'
echo 
echo 'To make changes to the configuration you may edit /home/'$UNAME'/.config/transmission/settings.json file.'
echo 'Your Transmission downloads will be saved inside /home/'$UNAME'/Downloads/transmission folder.'
echo
echo -e $CYAN
echo -e "If this script worked for you, please visit http://www.htpcBeginner.com and like/follow us."
echo -e $ENDCOLOR

RES=`ps ax | grep -v grep | grep -i xorg`
if [ $? -eq 0 ]; then
	if which xdg-open > /dev/null
	then
		xdg-open http://www.htpcbeginner.com
	elif which gnome-open > /dev/null
	then
		gnome-open http://www.htpcbeginner.com
	fi
fi

sleep 5

echo -e $YELLOW
echo -e "--->Please read the instructions above clearly. When you are done reboot your system."
echo -e $ENDCOLOR

read -p "Reboot now? Y/y to reboot or any other key to manually reboot later: "
REBO=${REPLY,,}

if [ "$REBO" != "y" ]
then
	echo -e $YELLOW
	echo -e "Thank you for using the AtoMiC Transmission WebUI install script from www.htpcBeginner.com."
	echo -e $ENDCOLOR
	exit 1
else 
	echo -e $YELLOW
	echo -e "Rebooting..."
	echo -e $ENDCOLOR
	sleep 1
	sudo reboot
fi
