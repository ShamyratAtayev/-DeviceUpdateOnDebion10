# Azure Device Update

# Manual installation of ADU on Debian 10

## Documentation

 - [Create and provision an IoT Edge device on Linux using symmetric keys](https://learn.microsoft.com/en-us/azure/iot-edge/how-to-provision-single-device-linux-symmetric?view=iotedge-1.4&amp%3Btabs=azure-portal%2Cubuntu&tabs=azure-portal%2Cubuntu)
 - [Tutorial: Device Update for Azure IoT Hub](https://learn.microsoft.com/en-us/azure/iot-hub-device-update/device-update-simulator)
 - [Device Update for IoT Hub documentation](https://learn.microsoft.com/en-us/azure/iot-hub-device-update/)
## Installation

 Install IoT Edge
```bash
curl https://packages.microsoft.com/config/debian/10/packages-microsoft-prod.deb > ./packages-microsoft-prod.deb
sudo apt install ./packages-microsoft-prod.deb
```
Install a container engine
```bash
sudo apt-get update; \
  sudo apt-get install moby-engine
```

Install nano if it is not installed already
```bash
sudo apt install nano
```
- Create (if the file's not there already) or open the Docker daemon's config file at /etc/docker/daemon.json.

- Set the default logging driver to the local logging driver as shown in the example below.
```bash
sudo nano /etc/docker/daemon.json
```
Put inside the following:
```javascript
{
    "log-driver": "local"
}
```
#### Installing aziot identity and edge for debian 10 (Manually)

``` Istalling Azure-identity ```
```bash
curl -L https://github.com/Azure/azure-iotedge/releases/download/1.4.10/aziot-identity-service_1.4.4-1_debian10_arm64.deb -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
```
``` Istalling Azure-edge ```
```bash
curl -L https://github.com/Azure/azure-iotedge/releases/download/1.4.10/aziot-edge_1.4.10-1_debian10_arm64.deb -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
```
Provision the device with its cloud identity
```bash
sudo iotedge config mp --connection-string 'PASTE_DEVICE_CONNECTION_STRING_HERE'
```
- Apply the configuration changes.
```bash
sudo iotedge config apply
```
- To view the configuration file, you can open it:
```bash
sudo nano /etc/aziot/config.toml
```

#### Installing Docker ce cli

``` Install software-properties-common ```
```bash
sudo apt-get install software-properties-common
```

``` Install Docker ce cli ```
```bash
sudo add-apt-repository "deb [arch=arm64] https://download.docker.com/linux/debian buster stable"
sudo apt update
sudo apt-get install docker-ce-cli
```

#### Check docker and iotedge status
```bash
sudo docker ps
sudo docker ps -a
sudo iotedge system works
sudo iotedge system logs
sudo iotedge list
sudo iotedge check
```

#### Installing device update

``` Install du ```
```bash
sudo apt install deviceupdate-agent
```
``` Change configurations in /etc/adu/du-config.json ```

```bash
sudo nano /etc/adu/du-config.json
```

- Example of du-config
```bash
{
   "schemaVersion": "1.0",
   "aduShellTrustedUsers": [
      "adu",
      "do"
   ],
   "manufacturer": "contoso",
   "model": "virtual-vacuum-v2",
   "agents": [
      {
      "name": "main",
      "runas": "adu",
      "connectionSource": {
         "connectionType": "string",
         "connectionData": "HostName=example-connection-string.azure-devices.net;DeviceId=example-device;SharedAccessKey=M5oK/rOP12aB5678YMWv5vFWHFGJFwE8YU6u0uTnrmU=" 
      },
      "manufacturer": "contoso",
      "model": "virtual-vacuum-v2"
      }
   ]
}  
```

- Start ADU
```bash
sudo systemctl start deviceupdate-agent
```

- Check status of ADU
```bash
sudo -u adu /usr/bin/AducIotAgent -h 
```

- See logs
```bash
sudo -u adu /usr/bin/AducIotAgent -l0 -e
```
