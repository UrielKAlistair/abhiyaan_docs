# Vajra connection notes: 16th December 2022

First power the vehicle, and connect your device to the wifi.  

`ssh username@vehicleip`

to get into the vehicle.  

To find the vehicle ip in case it is not already known, go to your browser, and go to 192.168.0.1, the modem address.   
Log in to it. In case the password is not known, reset the modem and use the password on its base.  
The vehicle will be seen as a connected device inside this.  

Ask someone for the username password authentication. To finish sshing into the vehicle.  

Present credentials:  
vajra wifi password: abhiyaan123  
vajra username: abhiyaan2  
vajra password: bolt2  

## Copying files

the command scp lets you copy across devices.

`scp filepath1 filepath2`

for the filepaths on a different machine, username@ipaddress:filepath must be used. Else it's assumed to be a path on your machine. Do *not* forget the :  
To find the ip address of your laptop, use
`hostname -I`


## Time issues

Another important thing: when we unplug vajra's battery, its clock stops ticking.  
So when we catkin_make it much later, the command says that the files it's trying to build are from the future and it refuses to build anything.  

We need to sync the time to the net by connecting LAN to vajra, approving netaccess and doing  

`timedatectl set-ntp true
systemctl restart systemd-timesyncd`

This is kind of a pain, would be awesome if someone can come up with a workaround for this.  
