# Installing and configuring notifications using TenderDuty for OKP4 Network
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

- `change name network "Osmosis" on "Okp4"`
- `chainid: okp4-nemeton-1`
- `valoper_address: <your valoder address>`

![1](https://user-images.githubusercontent.com/49861610/212419657-ac18d179-b797-4f45-b6bb-752841a5c589.png)

- URL for the endpoint: `https://okp4-rpc.cagie.tech:443, https://okp4-testnet.rpc.kjnodes.com:443 (or your RPC address)`
- Delete example repeat host url `https://some-other-node:443`

![2](https://user-images.githubusercontent.com/49861610/212422043-6f5b9ce5-b6a4-412e-88fe-549e00f5ab09.png)

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
![3](https://user-images.githubusercontent.com/49861610/212422230-d66bbf96-5ab7-4e32-8e8d-636dfee6ece0.png)

## Configuring Discord notifications
1. In the Discord application, create a new server or channel in the group.
2. In the server or channel settings, go to the "Integrations" item.
3. Create a webhook, copy the URL, and save it.

![4](https://user-images.githubusercontent.com/49861610/212422620-d8b765c7-2257-4911-a194-710f750c182d.png)

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
