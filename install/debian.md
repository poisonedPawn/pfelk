## Custom Installation Guide (pfSense/OPNsense) + Elastic Stack 

## Table of Contents

- [Preparation](#preparation)
- [Installation](#installation)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Services](#services)

# Preparation

### 1. Disabling Swap
Swapping should be disabled for performance and stability.
```
sudo swapoff -a
```

### 2. Configuration Date/Time Zone
The box running this configuration will reports firewall logs based on its clock.  The command below will set the timezone to Eastern Standard Time (EST).  To view available timezones type `sudo timedatectl list-timezones`
```
sudo timedatectl set-timezone EST
```

### 3. Add Prerequisites
```
apt-get install apt-transport-https gnupg2 software-properties-common dirmngr lsb-release ca-certificates
```

### 4. Download and install the public GPG signing key
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | apt-key add -
```

### 5. Add Elasticsearch|Logstash|Kibana Repositories (version 7+)
```
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | tee -a /etc/apt/sources.list.d/elastic-7.x.list
```

### 6. Update
```
apt-get update
```

### 7. Install MaxMind (Optional)
Follow the steps [here](https://github.com/pfelk/pfelk/wiki/How-To:-MaxMind-via-GeoIP-with-pfELK), to install and utilize MaxMind. Otherwise the built-in GeoIP from Elastic will be utilized.

# Installation
- Elasticsearch v7+ | Kibana v7+ | Logstash v7+

### 8. Install Elasticsearch|Kibana|Logstash
```
apt-get install elasticsearch kibana logstash
```

# Configuration

### 9. Configure Kibana
```
sudo wget -q https://raw.githubusercontent.com/pfelk/pfelk/master/etc/kibana/kibana.yml -P /etc/kibana/
```

# Configuration

### 10. Configure Logstash
```
sudo wget -q https://raw.githubusercontent.com/pfelk/pfelk/master/etc/logstash/pipelines.yml -P /etc/logstash/
```

### 11. Create Required Directories
```
sudo mkdir -p /etc/pfelk/{conf.d,config,logs,databases,patterns,scripts,templates}
```

### 12. (Required) Download the following configuration files
```
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/conf.d/01-inputs.conf -P /etc/pfelk/conf.d/
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/conf.d/02-types.conf -P /etc/pfelk/conf.d/
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/conf.d/03-filter.conf -P /etc/pfelk/conf.d/
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/conf.d/05-apps.conf -P /etc/pfelk/conf.d/
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/conf.d/20-interfaces.conf -P /etc/pfelk/conf.d/
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/conf.d/30-geoip.conf -P /etc/pfelk/conf.d/
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/conf.d/45-cleanup.conf -P /etc/pfelk/conf.d/
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/conf.d/50-outputs.conf -P /etc/pfelk/conf.d/
```

### 13. (Optional) Download the following configuration files
```
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/conf.d/35-rules-desc.conf -P /etc/pfelk/conf.d/
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/conf.d/36-ports-desc.conf -P /etc/pfelk/conf.d/
```

### 14. Download the grok pattern
```
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/patterns/pfelk.grok -P /etc/pfelk/patterns/
```

### 15. (Optional) Download the Database(s)
```
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/databases/rule-names.csv -P /etc/pfelk/databases/
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/databases/service-names-port-numbers.csv -P /etc/pfelk/databases/
```

### 16. (Optional) Configure Firewall Rule Database
To configure pfSense/OPNsense to update the firewall rule database, follow [this reference](https://github.com/pfelk/pfelk/wiki/References:-Rule-Descriptions).

### 17. (Optional) Amend 02-types.conf with unique observer.name field (line 8).  
Amend "OPNsense" as desired.  This will be useful if monitoring multiple instances. Reference the [Wiki page](https://github.com/pfelk/pfelk/wiki/References:-Multiple-Instances) for further assistance.
```
      add_field => [ "[observer][name]", "OPNsense" ]
```
### 18. (Optional) Amend 20-interfaces.conf as desired, to map/reference the interface.name, interface.alias and network.name fields. 
Amend `interface.name`, `interface.alias` and `network.name` fields via [Wiki page](https://github.com/pfelk/pfelk/wiki/References:-Customized-Interface-Names)

# Troubleshooting
### 19. Create Logging Directory 
```
sudo mkdir -p /etc/pfelk/logs
```

### 20. Download Script
`error-data.sh`
```
sudo wget https://raw.githubusercontent.com/pfelk/pfelk/master/etc/pfelk/scripts/error-data.sh -P /etc/pfelk/scripts/
```

### 21. Make Script Executable
`error-data.sh` 
```
sudo chmod +x /etc/pfelk/scripts/error-data.sh
```

# Services
### 22. Start Services on Boot (you'll need to reboot or start manually to proceed)
```
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
sudo /bin/systemctl enable kibana.service
sudo /bin/systemctl enable logstash.service
```
### 23. Start Services Manually
```
systemctl start elasticsearch.service 
systemctl start kibana.service
systemctl start logstash.service
```

### 24. Complete Configuration --> [Configuration](configuration.md)
