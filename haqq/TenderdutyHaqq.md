# Installing and configuring notifications using TenderDuty
## Preparing the server
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl build-essential git wget jq make gcc tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```
## Install Docker (one command)
```
apt update && \
apt install apt-transport-https ca-certificates curl software-properties-common -y && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable" && \
apt update && \
apt-cache policy docker-ce && \
sudo apt install docker-ce -y
```
## Install TenderDuty
```
mkdir tenderduty && cd tenderduty
docker run --rm ghcr.io/blockpane/tenderduty:latest -example-config >config.yml
```
## Open config TenderDuty
```
nano $HOME/tenderduty/config.yml
```
## Fill in the following parameters:
```
name network - change "aura" on "haqq"
chainid: haqq_54211-2
valoper_address: <your valoder address>
URL for the endpoint: https://rpc.tm.testedge2.haqq.network:443
Delete example repeat host (https://some-other-node:443) 
```
## Start TenderDurt and check logs
```
docker run -d --name tenderduty -p "8888:8888" -p "28686:28686" --restart unless-stopped -v $(pwd)/config.yml:/var/lib/tenderduty/config.yml ghcr.io/blockpane/tenderduty:latest
docker logs -f --tail 20 tenderduty
```
## Check monitoring
In the browser, go to the address of your server with port 8888. Example: http://<IP>:8888
The monitoring system for your validator will be displayed
  
## Configuring Discord notifications
1. In the Discord application, create a new server or channel in the group.
2. In the server or channel settings, go to the "Integrations" item.
3. Create a webhook, copy the URL, and save it.
4. Open config file TenderDuty
```
nano $HOME/tenderduty/config.yml
```
5. Enable notifications in the "Discord settings" block:
```
enabled: yes
```
6. In the webhook field, paste the link received in Discord when creating the webhook.
7. Save your changes
8. Restart TenderDuty:
```
docker restart tenderduty
```
As a result, notifications will be sent to Discord.
