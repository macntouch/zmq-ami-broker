# ØMQ Asterisk Manager Interface (AMI) Broker
ØMQ broker is used to distribute Asterisk AMI messges.
It is based on res_zmq_manager module from [here](https://github.com/litnimax/asterisk-zmq).

## How it works ##
![Images](https://raw.githubusercontent.com/litnimax/zmq-ami-broker/master/doc/asterisk-zeromq-state-dia.png)

The Broker connect to Asterisk res_manager_zmq module using ømq sockets. This asterisk module has 2 ømq sockets:
* addr_cmd - this socket is REQ/REP type and is used to accept AMI actions over ømq.
* addr_evt - this socket is SUB/PUB type and is used to pubish all AMI events to ømq endpoints, it does not accept actions.

So the Broker receives all AMI messages from all connected Asterisk servers and when events are 
DeviceStateChange or PresenceStateChange it passes these events to other servers using AMI action SetVar 
with DEVICE_STATE and PRESENCE_STATE functions.

## Requirements ##
* Python 2.7.
* System libs libzmq and libzmq-dev.
* Python extension pyZMQ (python-zmq system packages or installed via pip).
* Asterisk headers to compile res_zmq_manager (asterisk-dev package or sources).
* Asterisk modules res_manager_devicestate.so and res_manager_presencestate.so.
* enabled=yes in etc/asterisk/manager.conf

## Installation
Installation is shown in this screencast.
Installation steps:
* Download, compile, install and configure res_zmq_manager.
* Download Broker and add your servers to config.ini file (See [example_config.ini](/litnimax/zmq-ami-broker/blob/master/example_config.ini))
* Run the Broker

### Installation of res_zmq_manager ###
Download it from [here](https://github.com/litnimax/asterisk-zmq).
```
git clone https://github.com/litnimax/asterisk-zmq.git
cd asterisk-zmq
make
```
If make fails check that your have asterisk includes. If you have downloaded asterisk source then add CFLAGS variable to point to include folder:
```
CFLAGS=-I/home/asterisk/include make
```
After make is down take res_zmq_manager.so from build folder and copy it to modules directory:
```
cp build/res_zmq_manager.so /usr/lib/asterisk/modules/
```
Now copy configuration file to /etc/asterisk/:
```
cp conf/zmq_manager.conf /etc/asterisk
```
Now restart Asterisk or load the module manually with 
```
module load res_zmq_manager.so
```

### Installing and running the Broker
The Broker depends on python zmq library. It can be installed system wide or in virtualenv. Here we cover virtualenv way.
```
git clone https://github.com/litnimax/zmq-ami-broker.git
cd zmq-ami-broker
virtualenv env
source env/bin/activate
pip install -r requirenents.txt
```
Now create a copy of config.ini and update it with your settings:
```
cp example_config.ini config.ini
```
Now run the Broker:
```
python broker.py config.ini
```


