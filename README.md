# Configuration Files

## Introduction
These are dependency configuration files needed by the Resilmesh framework, follow instructions below, also there are comments within the sub directories config files themselves.

>Note: we assume in this guide that 'localhost' correspond to your Wazuh instance

## Wazuh Installation
You'll need a Wazuh instance up and running. Follow the installation instructions at https://documentation.wazuh.com/current/installation-guide/index.html

### Wazuh Rsyslog - Resilmesh integration
For the sake of simplicity, we will leverage the RSyslog server within Wazuh to send the events from the Resilmesh framework, since the RSyslog is automatically collected
by Wazuh, so no wazuh agent will be needed.

Add [resilmesh.conf](./wazuh/rsyslog/resilmesh.conf) to /etc/rsyslog.d/ directory in your Wazuh server, this will open a TCP port 10514 which will be used by Vector
to send the events.

Restart RSyslog with `systemctl restart rsyslog`

Test if RSyslog port is opened with `telnet localhost 10514`, type anything and check if outputs to /var/log/resilmesh.log

For Wazuh be able to understand the new logs generated, we need to add a localfile directive into `/var/ossec/etc/ossec.conf`,
go to https://localhost:4343/app/settings#/manager/?tab=configuration and add the following between <ossec_config> directive,
should be at the end of the file:
```xml
<ossec_config>
<!-- there might be other configurations around here, don't change them -->
    <localfile>
        <log_format>syslog</log_format>
        <location>/var/log/resilmesh.log</location>
    </localfile>
<!-- there might be other configurations around here, don't change them -->
</ossec_config>
```

### Wazuh Resilmesh Decoders
We need to add some decoders to match the events from Resilmesh Framework, go to: https://localhost:4343/app/decoders#/manager/?tab=decoders
and add the following decoders from file [resilmesh_decoders.xml](./wazuh/decoders/resilmesh_decoders.xml)

Save it, you can test it too, use the 'Decoders Test' button, and then paste this sample (it has to be in a single line):
```text
2024-10-17T15:06:48.093438+00:00 Vector.ph1_resilmesh_network  {"@timestamp":"2024-08-29T13:27:54.065982398Z","destination":{"ip":"149.171.126.17"},"ecs":{"version":"1.6.0"},"source":{"ip":"175.45.176.3"},"silent_push":{"wazuh_rule_level":9}}
```
if you got a response like the one below, we're done:
```text
**Phase 2: Completed decoding.
	name: 'resilmesh_decoders'
	@timestamp: '2024-08-29T13:27:54.065982398Z'
	destination.ip: '149.171.126.17'
	dstip: '149.171.126.17'
	ecs.version: '1.6.0'
	risk_score: '99'
	source.ip: '175.45.176.3'
	sp_risk_score: '99'
	srcip: '175.45.176.3'

**Phase 3: Completed filtering (rules).
	id: '99901'
	level: '3'
	description: 'Resilmesh'
	groups: '["Resilmesh"]'
	firedtimes: '1'
	mail: 'false'
**Alert to be generated.
```

### Wazuh Resilmesh Rules
In addition to the decoder above, we also need some rules, so alerts can be generated, go to
https://localhost:4343/app/rules#/manager/?tab=ruleset and add the following rules from file 
[resilmesh_rules.xml](./wazuh/rules/resilmesh_rules.xml)

To test the rule, if you want, best way to do it is by repeating the same previous test we did with the decoder.
#### Correlation Rules
Some additional rules for correlation you can also add from file [resilmesh_correlation_rules.xml](./wazuh/rules/resilmesh_correlation_rules.xml)

Follow the manual if you need more specific rules: https://documentation.wazuh.com/current/user-manual/ruleset/ruleset-xml-syntax/rules.html

### Restart Wazuh
Restart your Wazuh Manager so the changes can be applied
```shell
sudo systemctl restart wazuh-manager
```
If everything goes as planned, you should see the Resilmesh events in the Wazuh Dashboard.

## Support
Ping if you need any further help: <Jorgeley [jorgeley@silentpush.com](jorgeley@silentpush.com)>
