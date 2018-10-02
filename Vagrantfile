# -*- mode: ruby -*-
# vi: set ft=ruby :

# Variables
## Infrastructure
$box_image = "ubuntu/xenial64"

## InfluxDB & Grafana 
$database_name = "mydb"
$time_interval = "30s"
$datasource_name = "InfluxDB-mydb"

##Version
$chronograf_version = "chronograf_1.6.2_amd64.deb"


## Scripts
$build_prereq = <<SCRIPT
echo "...Updating OS..."
apt-get update -y && apt-get upgrade -y
SCRIPT

$install_influxdb = <<SCRIPT
echo "...Installing InfluxDB..."
curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
source /etc/lsb-release
echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
sudo apt-get update && sudo apt-get install -y influxdb
sudo systemctl enable influxdb.service
sudo systemctl start influxdb
SCRIPT

$install_telegraf = <<SCRIPT
echo "...Installing Telegraf..."
sudo apt-get install -y telegraf

sudo rm /etc/telegraf/telegraf.conf

cat > /etc/telegraf/telegraf.conf << EOF
[agent]
  interval = "#{$time_interval}"

# OUTPUTS
[[outputs.influxdb]]
  url = "http://localhost:8086" # required.
  database = "#{$database_name}" # required.

# INPUTS
[[inputs.mem]]

[[inputs.cpu]]
  percpu = true
  totalcpu = true
EOF

sudo systemctl restart telegraf.service
SCRIPT

$install_grafana = <<SCRIPT
echo "...Installing Grafana..."
sudo apt-get install -y adduser libfontconfig
cat <<EOF >/etc/apt/sources.list
deb https://packagecloud.io/grafana/stable/debian/ jessie main
EOF
curl https://packagecloud.io/gpg.key | sudo apt-key add -
sudo apt-get update && sudo apt-get install -y grafana
sudo systemctl daemon-reload
sudo systemctl enable grafana-server
sudo systemctl enable grafana-server.service
sudo systemctl start grafana-server
SCRIPT

$create_influx_database = <<SCRIPT
echo "...Create Influx database..."

timeout 10 bash -c "until </dev/tcp/localhost/8086; do sleep 1; done"

curl -i -XPOST http://localhost:8086/query --data-urlencode "q=CREATE DATABASE #{$database_name}"
SCRIPT

$reset_grafana_admin_password = <<SCRIPT
echo "...Reset Grafana admin password..."

timeout 10 bash -c "until </dev/tcp/localhost/3000; do sleep 1; done"

curl -uadmin:admin -X PUT -H "Content-Type: application/json" -d '{
  "oldPassword": "admin",
  "newPassword": "admin",
  "confirmNew": "admin"
}' http://localhost:3000/api/user/password
SCRIPT

$create_grafana_data_source = <<SCRIPT
echo "...Create Grafana data source..."

timeout 10 bash -c "until </dev/tcp/localhost/3000; do sleep 1; done"

curl -uadmin:admin -X POST -H "Content-Type: application/json" -d '{
  "name":"#{$datasource_name}",
  "type":"influxdb",
  "url":"http://localhost:8086",
  "access":"proxy",
  "basicAuth":false,
  "database":"#{$database_name}",
  "isDefault":true,
  "jsonData": {
  "timeInterval":"#{$time_interval}"
  }
}' http://localhost:3000/api/datasources
SCRIPT

$install_chronograf = <<SCRIPT
echo "...Installing Chronograf..."
wget https://dl.influxdata.com/chronograf/releases/#{$chronograf_version}
sudo dpkg -i #{$chronograf_version}
rm #{$chronograf_version}
SCRIPT

Vagrant.configure("2") do |config|

    config.vm.box = $box_image

    config.vm.network "forwarded_port", :guest => 8083, :host => 8083
    config.vm.network "forwarded_port", :guest => 8086, :host => 8086
    config.vm.network "forwarded_port", :guest => 8090, :host => 8090
    config.vm.network "forwarded_port", :guest => 8099, :host => 8099
    config.vm.network "forwarded_port", :guest => 3000, :host => 3000
    config.vm.network "forwarded_port", :guest => 8888, :host => 8888
    
    config.vm.provision "shell", inline: <<-SHELL
        #{$build_prereq}
        #{$install_influxdb}
        #{$create_influx_database}
        #{$install_telegraf}
        #{$install_grafana}
        #{$reset_grafana_admin_password}
        #{$create_grafana_data_source}
        #{$install_chronograf}
    SHELL
end    
