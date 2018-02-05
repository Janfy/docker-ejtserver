<p align="center"><a href="https://github.com/crazy-max/docker-ejtserver" target="_blank"><img height="100"src="https://raw.githubusercontent.com/crazy-max/docker-ejtserver/master/.res/ejtserver_docker.png"></a></p>

<p align="center">
  <a href="https://microbadger.com/images/crazymax/ejtserver"><img src="https://images.microbadger.com/badges/version/crazymax/ejtserver.svg?style=flat-square" alt="Version"></a>
  <a href="https://travis-ci.org/crazy-max/docker-ejtserver"><img src="https://img.shields.io/travis/crazy-max/docker-ejtserver/master.svg?style=flat-square" alt="Build Status"></a>
  <a href="https://hub.docker.com/r/crazymax/ejtserver/"><img src="https://img.shields.io/docker/stars/crazymax/ejtserver.svg?style=flat-square" alt="Docker Stars"></a>
  <a href="https://hub.docker.com/r/crazymax/ejtserver/"><img src="https://img.shields.io/docker/pulls/crazymax/ejtserver.svg?style=flat-square" alt="Docker Pulls"></a>
  <a href="https://quay.io/repository/crazymax/ejtserver"><img src="https://quay.io/repository/crazymax/ejtserver/status?style=flat-square" alt="Docker Repository on Quay"></a>
  <a href="https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=SD4J26TW97CSU"><img src="https://img.shields.io/badge/donate-paypal-7057ff.svg?style=flat-square" alt="Donate Paypal"></a>
</p>

## About

🐳 [EJT License Server](https://www.ej-technologies.com/license/files) Docker image based on Alpine Linux.<br />
If you are interested, [check out](https://hub.docker.com/r/crazymax/) my other 🐳 Docker images!

## Features

* License server customizable via environment variables
* Persistence of registration data and configuration in a single directory

## Docker

### Environment variables

* `TZ` : The timezone assigned to the container (default to `UTC`)
* `EJTSERVER_DISPLAY_HOSTNAMES` : If you want to see host names instead of IP addresses (default to `false`)
* `LOG_LEVEL` : [Log4J log level](https://logging.apache.org/log4j/2.x/manual/customloglevels.html) of the EJT License Server (default to `INFO`)

### Volumes

* `/data` : Contains registration data and configuration

In this folder you will find those files :

* `ip.txt` : If you would like to allow only certain IP addresses, enter one IP address per line. If no IP addresses are entered, all IP addresses will be allowed. You can specify IP masks, such as 192.168.2.*
* `license.txt` : Enter your floating licenses in this file, one license key per line
* `users.txt` : If you would like to allow only certain user names, please enter one user name per line. If no user names are entered, all user names will be allowed

### Ports

* `11862` : License server port

### Commands

You also have access to these commands from the container :

* `ejtserver` : This is the license server the daemon launch script. Commands available : `start|stop|run|run-redirect|status|restart|force-reload`.
* `admin` : Admin tool command line based of ejtserver. It allows you to list all active connections and to terminate selected connections. In addition, you can check out a temporary license for use in environments that have no access to the floating license server

Usage :

```bash
docker-compose exec ejtserver admin list
```

## Usage

Docker compose is the recommended way to run this image. You can use the following [docker compose template](docker-compose.yml), then run the container :

```bash
docker-compose up -d
docker-compose logs -f
```

Or use the following minimal command :

```bash
docker run -d -p 11862:11862 --name ejtserver \
  -e TZ="Europe/Paris" \
  -v $(pwd)/data:/data \
  crazymax/ejtserver:latest
```

## Update

Recreate the container whenever i push an update :

```bash
docker-compose pull
docker-compose up -d
```

## Notes

### How to use your floating license ?

[ej-technologies'](https://www.ej-technologies.com/) products offer a floating license mode in the license dialog. Choose `Help -> Enter License Key` from the main menu in the JProfiler GUI or the install4j IDE and select the <b>Floating license</b> radio button.

The "Name" and "Company" fields are informational only, unless you choose to restrict the allowed values for the "Name" field as described in README.TXT. In the license server field you have to enter the hostname of the computer where the license server is running. Instead of a host name, an IP address can also be used.

If have a floating license for a certain major version of a product, you can use older versions of the same product with that floating license as well.

Should you require any additional assistance, please contact *support@ej-technologies.com*

### User groups

If you want to partition keys to different groups of users, you can define groups in the file `license.txt` and the access control files `users.txt` and `ip.txt` by inserting group headers :

```
   [group]
```

All entries after a group header belong to that group until a new group is started. If no group has been started, entries are added to the "default" group.

Users are assigned to a group based on the defined groups in the access control files. If users are defined in users.txt, the group is determined by the that file. If the resulting group is the default group, the `ip.txt` file will be used for determining the associated group. If the users.txt file is empty, only the ip.txt file will be used.

In order to partition a single key to different groups in the `license.txt` file, add the key to multiple groups with the following syntax :

```
   n:key
```

where n is the number of concurrent users that should be assigned to the current group. Use different values of n in different groups that add up to the maximum number of current users for the key. For example :

```
[groupA]
4:F-95-10-xxx
[groupB]
6:F-95-10-xxx
```

splits the 10-user key F-95-10-xxx into 4 concurrent users for `[groupA]` and 6 concurrent users for `[groupB]`. In `users.txt`, the groups would be defined as :

```
[groupA]
bob
alice
...


[groupB]
carol
john
...
```

Alternatively, the `ip.txt` file could define groups as :

```
[groupA]
192.162.1.*
[groupB]
192.162.2.*
```

Group names are shown in the log file next to the user name.

## How can i help ?

We welcome all kinds of contributions :raised_hands:!<br />
The most basic way to show your support is to star :star2: the project, or to raise issues :speech_balloon:<br />
Any funds donated will be used to help further development on this project! :gift_heart:

[![Donate Paypal](.res/paypal.png)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=SD4J26TW97CSU)

## License

MIT. See `LICENSE` for more details.