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
Fill in the following parameters:

- `change name network "aura" on "haqq"`
- `chainid: haqq_54211-2`
- `valoper_address: <your valoder address>`

![1](https://user-images.githubusercontent.com/49861610/190073105-6c0d94ca-6b29-4a23-9778-79a4e44d27ae.png)

- URL for the endpoint: `http://65.21.133.125:30657 (or your RPC address)`
- Delete example repeat host `https://some-other-node:443`
![2](https://user-images.githubusercontent.com/49861610/190073121-3175c0f6-d9b7-4ddb-9687-84e7f6124192.png)
## Start TenderDurt and check logs
```
docker run -d --name tenderduty -p "8888:8888" -p "28686:28686" --restart unless-stopped -v $(pwd)/config.yml:/var/lib/tenderduty/config.yml ghcr.io/blockpane/tenderduty:latest
docker logs -f --tail 20 tenderduty
```
## Check monitoring
Get your IP address on port 8888
```
echo -e "\033[0;32mhttp://$(wget -qO- eth0.me):8888/\033[0m"
```
Copy the resulting output and paste it into the browser. Example: http://1.1.1.1:8888
The monitoring system for your validator will be displayed
![3](https://user-images.githubusercontent.com/49861610/190073316-f380b2e4-f894-49b3-9e08-8c40910bb5c9.png)

## Configuring Discord notifications
1. In the Discord application, create a new server or channel in the group.
2. In the server or channel settings, go to the "Integrations" item.
3. Create a webhook, copy the URL, and save it.

![4](https://user-images.githubusercontent.com/49861610/190073720-5880f52c-f6ae-4266-8ece-1454eacdb768.png)

5. Open config file TenderDuty:
```
nano $HOME/tenderduty/config.yml
```
5. In a block with a chain enable notifications in the `Discord settings` block:
```
enabled: yes
```
6. In the webhook field, paste the link received in Discord when creating the webhook.

![1](https://user-images.githubusercontent.com/49861610/190135033-ae1afeb0-8df8-46e0-8961-883bb0e2659c.png)

8. Save your changes.
9. Restart TenderDuty:
```
docker restart tenderduty
```
As a result, notifications will be sent to Discord.
