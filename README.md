# Installing and setting up Elasticsearch, Logstash, Kibana, and Filebeat on Amazon Linux 2023

This guide will help you install and set up Elasticsearch, Logstash, Kibana, and Filebeat on Amazon Linux 2023

## Requirements

Amazon Web Services (AWS), Amazon Linux 2023

## Initial setup

### Step 1: Update your system

```console
$ sudo yum update
$ sudo yum upgrade
```

### Step 2: Install Java

```console
$ sudo yum install java
```

### Step 3: Install Elasticsearch

```console
$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.12.0-x86_64.rpm
$ sudo yum install elasticsearch/elasticsearch-8.12.0-x86_64.rpm
```

### Step 4: Configure Elasticsearch

Edit the Elasticsearch configuration file

```console
$ sudo vim /etc/elasticsearch/elasticsearch.yml
```

Edit the following lines

```yml
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
cluster.name: my-application # <----- change made here
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
node.name: node-1  # <----- change made here
#
# Add custom attributes to the node:
#
#node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
path.data: /var/lib/elasticsearch
#
# Path to log files:
#
path.logs: /var/log/elasticsearch
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
#bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# By default Elasticsearch is only accessible on localhost. Set a different
# address here to expose this node on the network:
#
network.host: 0.0.0.0  # <----- change made here
#
# By default Elasticsearch listens for HTTP traffic on the first free port it
# finds starting at 9200. Set a specific HTTP port here:
#
http.port: 9200  # <----- change made here
#
# For more information, consult the network module documentation.
#
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when this node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
#discovery.seed_hosts: ["host1", "host2"]
#
# Bootstrap the cluster using an initial set of master-eligible nodes:
#
#cluster.initial_master_nodes: ["node-1", "node-2"]
#
# For more information, consult the discovery and cluster formation module documentation.
#
discovery.type: single-node # <----- change made here
#
# ---------------------------------- Various -----------------------------------
#
# Allow wildcard deletion of indices:
#
#action.destructive_requires_name: false

#----------------------- BEGIN SECURITY AUTO CONFIGURATION -----------------------
#
# The following settings, TLS certificates, and keys have been automatically
# generated to configure Elasticsearch security features on 20-01-2024 14:20:22
#
# --------------------------------------------------------------------------------

# Enable security features
xpack.security.enabled: false  # <----- change made here

xpack.security.enrollment.enabled: false  # <----- change made here

# Enable encryption for HTTP API client connections, such as Kibana, Logstash, and Agents
xpack.security.http.ssl:
  enabled: false  # <----- change made here
  keystore.path: certs/http.p12

# Enable encryption and mutual authentication between cluster nodes
xpack.security.transport.ssl:
  enabled: false  # <----- change made here
  verification_mode: certificate
  keystore.path: certs/transport.p12
  truststore.path: certs/transport.p12
# Create a new cluster with the current node only
# Additional nodes can still join the cluster later
#cluster.initial_master_nodes: ["ip-172-31-85-107.ec2.internal"]  # <----- change made here

# Allow HTTP API connections from anywhere
# Connections are encrypted and require user authentication
http.host: 0.0.0.0

# Allow other nodes to join the cluster from anywhere
# Connections are encrypted and mutually authenticated
#transport.host: 0.0.0.0

#----------------------- END SECURITY AUTO CONFIGURATION -------------------------
```

Enable and start Elasticsearch

```console
$ sudo systemctl enable --now elasticsearch
```

### Step 5: Install Logstash

```console
$ wget https://artifacts.elastic.co/downloads/logstash/logstash-8.12.0-x86_64.rpm
$ sudo yum install logstash-8.12.0-x86_64.rpm
```

### Step 6: Configure Logstash
Edit the Logstash configuration file:

```console
$ sudo vim /etc/logstash/conf.d/logstash.conf
```

Add the following input and output configuration

```yml
input {
  beats {
    port => 5044
  }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
    manage_template => false
    index => "%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM.dd}"
  }
}
```

Enable and start Logstash

```console
$ sudo systemctl enable --now logstash
```

### Step 7: Install Kibana

```console
$ wget https://artifacts.elastic.co/downloads/kibana/kibana-8.12.0-x86_64.rpm
$ sudo yum install kibana-8.12.0-x86_64.rpm
```

### Step 8: Configure Kibana
Edit the Kibana configuration file

```console
$ sudo vim /etc/kibana/kibana.yml
```

Edit the following lines:

```yml
server.host: "0.0.0.0" 
elasticsearch.hosts: ["http://localhost:9200"]
```

Enable and start Kibana:

```console
$ sudo systemctl enable --now kibana
```

### Step 9: Install Filebeat

```console
$ wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-8.12.0-x86_64.rpm
$ sudo yum install filebeat-8.12.0-x86_64.rpm
```

### Step 10: Configure Filebeat
Enable the system module and set up Filebeat

```console
$ sudo filebeat modules enable system
$ sudo filebeat setup
$ sudo systemctl enable --now filebeat
```

This guide should help you install and configure Elasticsearch, Logstash, Kibana, and Filebeat on Amazon Linux 2023.
