ELLIPSE2-N-G4A3-B2

For all details and sources, check http://wiki.ros.org/sbg_driver and https://github.com/SBG-Systems/sbg_ros_driver  

## First Time Setup

`sudo apt-get install ros-noetic-sbg-driver`
`sudo adduser $USER dialout`

Change some param files to support ros msg data types instead of sbg's esoteric data types.  
First roscd into the package.  
Param files will likely be read only and you'll have to chmod the parent folder and the file itself to make changes.  

## First Time IMU Caliberation

roslaunch sbg_driver sbg_device_mag_calibration.launch

## Running it

Detect the port where your SBG device is connected (e.g. /dev/ttyUSB0) using
`dmesg | grep tty`
`edit sbg_device_uart_default.yaml accordingly. It's in config.`

`roslaunch sbg_driver sbg_ellipseN.launch`

