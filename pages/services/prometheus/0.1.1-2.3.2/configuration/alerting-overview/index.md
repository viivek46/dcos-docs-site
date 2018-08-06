---
layout: layout.pug
navigationTitle: Alerting Overview
title:  Alerting Overview
menuWeight: 25
excerpt: DC/OS Prometheus Alerting Overview
featureMaturity:
enterprise: false
---

# Working with AlwaysOn SQL :

 Alerting with Prometheus is divided into the following parts:

  1. Set up and configure the Alertmanager

  2. Configure Prometheus to talk to the Alertmanager

  3. Create alerting rules in Prometheus

  4. Send notification to Slack, PagerDuty, or email

## Set up and configure the AlwaysOn SQL

  The AlwaysOn SQL configurable options are present in Configuration section for `dse` has options for setting the ports, timeout values, log location, and other Spark or Hive configuration settings.

The template for AlwaysOn SQL Configuration is below:

```
# alwayson_sql_options:
#     enabled: false
#     thrift_port: 10000
#     web_ui_port: 9077
#     reserve_port_wait_time_ms: 100
#     alwayson_sql_status_check_wait_time_ms: 500
#     workpool: alwayson_sql
#     log_dsefs_dir: /spark/log/alwayson_sql
#     auth_user: alwayson_sql
#     runner_max_errors: 10
```

## Enabling AlwaysOn SQL

Set enabled to true in the AlwaysOn SQL options in `dse` configuration section.

### Starting and stopping AlwaysOn SQL

 If you have enabled AlwaysOn SQL, it will start when the cluster is started. You only need to explicitly start the server if it has been stopped, for example for a configuration change.
 
 To start AlwaysOn SQL service:

```
dse client-tool alwayson-sql start
```

 To completely stop AlwaysOn SQL service:
 
```
dse client-tool alwayson-sql stop
```
 The server must be manually started after issuing a stop command.
 
 To restart a running server:
 
```
dse client-tool alwayson-sql restart
```

### Create alerting rules in Prometheus
  Alerting rules allow you to define alert conditions based on Prometheus language expressions and to send notifications about firing alerts to an external service like Slack, PagerDuty, and email.

The default alert rules are below. Rules files are accessed in the Prometheus configuration .yml.

```
rules:
groups:
- name: cpurule
 rules:
 - alert: highcpu
   expr: cpu_total > 2
   annotations:
     DESCRIPTION: 'High CPU Utilization'
     SUMMARY: 'This is to notify for high cpu utilization'
```
The following example checks which instance is down :

```
alert: InstanceDown
expr: up == 0
for: 5m
labels:
  severity: page
annotations:
  summary: "Instance {{$labels.instance}} down"
  description: "{{$labels.instance}} of job {{$labels.job}} has been down for more than 5 minutes."

```
### Send notification to Slack, PagerDuty, and email

 Alertmanager manages sending alerts to Slack, PagerDuty, and email.

### Slack

  To configure Slack with Alertmanager, the Alertmanager uses the Incoming Webhooks feature of Slack.

  The default configuration below sends alerts to Slack to be configured under the Alertmanager configuration yml.

```
route:
group_by: [cluster]
receiver: webh
group_interval: 1mreceivers:
- name: webh
 webhook_configs:
 - url: http://webhook.marathon.l4lb.thisdcos.directory:1234
```

### PagerDuty
To configure PagerDuty with Alertmanager :

1. Create a service in PagerDuty, and obtain an integration key.

2. Go to the “Services” page in PagerDuty:

3. Click “+ Add New Service”:

4. Note down the Integration Key:

Here is a sample configuration for adding a PagerDuty setup to the Alertmanager configuration yml.

```
receivers:
- name: team-pager
  pagerduty_configs:
  - service_key: $INTEGRATION_KEY
```

### Email
  Here is a sample configuration for adding an email alert setup to the Alertmanager configuration yml.

```
GMAIL_ACCOUNT=me@example.com # Substitute your full gmail address here.
GMAIL_AUTH_TOKEN=XXXX        # Substitute your app password

receivers:
- name: email-me
  email_configs:
  - to: $GMAIL_ACCOUNT
    from: $GMAIL_ACCOUNT
    smarthost: smtp.gmail.com:587
    auth_username: "$GMAIL_ACCOUNT"
    auth_identity: "$GMAIL_ACCOUNT"
    auth_password: "$GMAIL_AUTH_TOKEN"
```
