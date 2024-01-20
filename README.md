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
$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.9.1-x86_64.rpm 
$ sudo yum install elasticsearch-8.9.1-x86_64.rpm
```

### Step 4: Configure Elasticsearch

Edit the Elasticsearch configuration file

```console
$ sudo vim /etc/elasticsearch/elasticsearch.yml
```

Edit the following lines

```yml
cluster.name: my-application
network.host: 0.0.0.0
http.port: 9200
xpack.security.enabled: false
xpack.security.enrollment.enabled: false
xpack.security.http.ssl.enabled: false
xpack.security.transport.ssl.enabled: false
```

Enable and start Elasticsearch

```console
$ sudo systemctl enable --now elasticsearch
```

### Step 5: Install Logstash

```console
$ wget https://artifacts.elastic.co/downloads/logstash/logstash-8.9.1-x86_64.rpm
$ sudo yum install logstash-8.9.1-x86_64.rpm
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
$ wget https://artifacts.elastic.co/downloads/kibana/kibana-8.9.1-x86_64.rpm
$ sudo yum install kibana-8.9.1-x86_64.rpm
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
$ wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-8.9.1-x86_64.rpm 
$ sudo yum install filebeat-8.9.1-x86_64.rpm
```

### Step 10: Configure Filebeat
Enable the system module and set up Filebeat

```console
$ sudo filebeat modules enable system
$ sudo filebeat setup
$ sudo systemctl enable --now filebeat
```

This guide should help you install and configure Elasticsearch, Logstash, Kibana, and Filebeat on Amazon Linux 2023.
