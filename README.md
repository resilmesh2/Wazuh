# Wazuh - Integration

## Introduction
These are dependency configuration files needed by the Resilmesh framework integration with Wazuh, follow instructions below, also there are comments within the sub directories config files themselves.

>Note: we assume in this guide that 'localhost' correspond to your Wazuh instance

## Wazuh Installation
You'll need a Wazuh instance up and running. Follow the installation instructions at https://documentation.wazuh.com/current/installation-guide/index.html

## Wazuh Configuration
Follow the README's:
   - [RSyslog](./RSyslog/README.md)
   - [Decoders](./Decoders/README.md)
   - [Rules](./Rules/README.md)

## Restart Wazuh
Restart your Wazuh Manager so the changes can be applied
```shell
sudo systemctl restart wazuh-manager
```
If everything goes as planned, you should see the Resilmesh events in the Wazuh Dashboard.

## Support
Ping if you need any further help: <Jorgeley [jorgeley@silentpush.com](jorgeley@silentpush.com)>
