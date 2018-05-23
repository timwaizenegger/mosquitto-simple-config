# mosquitto-simple-config
Setup and config guide for mosquitto mqtt broker for small iot projects



# Intro

this guide helps you set up mosquitto on a working docker host that is internet-accessible.

We don't set up TLS, only authentication. This should be secure enough for most simple, non-commercial setups.


First, install mosquitto binaries on the host. We won't run the server there, but use the clients/tools later. (You can skip this step if you want and get the tools some other way)

```bash
sudo apt install mosquitto mosquitto-client

# make sure mosquitto doesn't run on the host
sudo systemctl disable mosquitto
```




# Prepare the config and user/device accounts

```bash
mkdir /var/mqtt
sudo cp mosquitto.conf.example /var/mqtt/mosquitto.conf

# create the first account and init. the passwd file
#   enter a password for the device
sudo mosquitto_passwd -c /var/mqtt/passwd device1


# in the future, you can add users in batch mode
sudo mosquitto_passwd -b /var/mqtt/passwd device2 secr3t

```

# start the mosquitto docker container
```bash
docker run -d --restart=always --name mqtt \
--publish 1883:1883  \
--volume /var/mqtt:/mosquitto/config \
eclipse-mosquitto
```


# test the broker
```bash
# subscribe to all topics (#)
mosquitto_sub -v -d -h myhostname.de -u device1 -P secr3t -V mqttv311 -t "#"

# publish a message to the topic "test"
mosquitto_pub -h myhost.de -u device2 -P secr3t -V mqttv311 -m "hallo" -t test
```

# Notes

* use the same value for device ID and username when connecting
* ech user may only connect once


