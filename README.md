# vagrant-influxdb-grafana

This Vagrantfile purpose is to quickly setup an Ubuntu box with InfluxDB, Grafana, Telegraf and Chronograf installed. It gives the opportunity to easily prototype ideas.

The provisonning steps include creating an InfluxDB database and the related Grafana's data source. Telegraf is configured to collect basics CPU and memory statistics.

### Prerequisites

- Vagrant
- VirtualBox

### Usage

1. Download or clone this repository
2. Optionnel - change the variables in the Vagrantfile according to your needs

```Ruby
$database_name = "mydb"
$time_interval = "30s"
$datasource_name = "InfluxDB-mydb"
```

3. Launch the VM build with the command `vagrant up`

```Bash
> vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'ubuntu/xenial64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'ubuntu/xenial64' is up to date...
==> default: Setting the name of the VM: vagrant-influxdb-grafana_default_1508399698618_92030
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 8083 (guest) => 8083 (host) (adapter 1)
    default: 8086 (guest) => 8086 (host) (adapter 1)
    default: 8090 (guest) => 8090 (host) (adapter 1)
    default: 8099 (guest) => 8099 (host) (adapter 1)
    default: 3000 (guest) => 3000 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
...
```

4. Check that everything goes well by connecting to the Grafana web interface at the url `http://localhost:3000` and providing the user `admin` with the password `admin`

![screenshot grafana 5](https://user-images.githubusercontent.com/9823778/45226152-73bb8680-b2be-11e8-89ad-cea96bcddb8d.png)

5. Chronograf is available at the url `http://localhost:8888` 

# Author

**Erwan Quélin**
- <https://github.com/equelin>
- <https://twitter.com/erwanquelin>

# License

Copyright 2017-2018 Erwan Quelin and the community.

Licensed under Apache License Version 2.0.

