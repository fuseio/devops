# Fuse Network

## Network

### Docker
##### Build
```docker build -t fusenet/node .```
##### Pull
```docker pull fusenet/node```
##### Push
```docker push fusenet/node```

### Bootnode

##### VM specs
* OS - `Linux (ubuntu 18.04)`
* Size - `Standard B2ms (2 vcpus, 8 GiB memory)`
* Disk - `30 GiB Premium SSD`
* Networking

```
| Priority 	| Description                    	| Port  	| Protocol 	| Source                  	| Destination    	| Action 	|
|----------	|--------------------------------	|-------	|----------	|-------------------------	|----------------	|--------	|
| 1000     	| ssh	                            | 22    	| TCP      	| ip list comma-separated 	| Any            	| Allow  	|
| 1001     	| p2p                            	| 30303 	| TCP      	| Any                     	| Any            	| Allow  	|
| 1002     	| p2p udp                        	| 30303 	| UDP      	| Any                     	| Any            	| Allow  	|
| 1003     	| rpc                            	| 8545  	| TCP      	| Any                     	| Any            	| Allow  	|
| 1004     	| https                          	| 443   	| TCP      	| Any                     	| Any            	| Allow  	|
| 1005     	| http                           	| 80    	| TCP      	| Any                     	| Any            	| Allow  	|
| 65000    	| AllowVnetInBound               	| Any   	| Any      	| VirtualNetwork          	| VirtualNetwork 	| Allow  	|
| 65001    	| AllowAzureLoadBalancerInBound  	| Any   	| Any      	| AzureLoadBalancer       	| Any            	| Allow  	|
| 65500    	| DenyAllInBound                 	| Any   	| Any      	| Any                     	| Any            	| Deny   	|
```

##### Run
```
# start
sudo ./quickstart.sh --role bootnode --node-key zeronet-bootnode-vm-<N>

# or, start docker manually
docker run -ti -v /home/fuse/fusenet/database:/data -v /home/fuse/fusenet/config:/config/custom -p 30300:30300 -p 8545:8545 -p 8546:8546 fusenet/node --role bootnode --parity-args --node-key bootnode<N>
```

### MasterOfCeremony / Validator

##### VM specs
* OS - `Linux (ubuntu 18.04)`
* Size - `Standard D2s v3 (2 vcpus, 8 GiB memory)`
* Disk - `30 GiB Premium SSD`
* Networking

```
| Priority 	| Description                    	| Port  	| Protocol 	| Source                  	| Destination    	| Action 	|
|----------	|--------------------------------	|-------	|----------	|-------------------------	|----------------	|--------	|
| 1000     	| ssh	                            | 22    	| TCP      	| ip list comma-separated 	| Any            	| Allow  	|
| 1001     	| p2p                            	| 30303 	| TCP      	| Any                     	| Any            	| Allow  	|
| 1002     	| p2p udp                        	| 30303 	| UDP      	| Any                     	| Any            	| Allow  	|
| 65000    	| AllowVnetInBound               	| Any   	| Any      	| VirtualNetwork          	| VirtualNetwork 	| Allow  	|
| 65001    	| AllowAzureLoadBalancerInBound  	| Any   	| Any      	| AzureLoadBalancer       	| Any            	| Allow  	|
| 65500    	| DenyAllInBound                 	| Any   	| Any      	| Any                     	| Any            	| Deny   	|
```

##### Run
```
# start
sudo ./quickstart.sh --role validator

# or, start dockers manually
sudo docker run --detach --name fusenet --volume /home/fuse/fusenet/database:/data --volume /home/fuse/fusenet/config:/config/custom -p 30303:30300 --restart=on-failure fusenet/node --role validator --address <address>

sudo docker run --detach --name fuseapp --volume /home/fuse/fusenet/config:/config --restart=on-failure fusenet/validator-app
```

### Explorer node

##### VM specs
Same as Bootnode

##### Run
```
# start
sudo ./quickstart.sh --role explorer

# or, start docker manually
sudo docker run --detach --name fusenet --volume /home/fuse/fusenet/database:/data --volume /home/fuse/fusenet/config:/config/custom -p 30303:30300 -p 8545:8545 -p 8546:8546 --restart=on-failure fusenet/node --role explorer --parity-args --node-key zeronet-explorer-vm
```

### Network reset
```
# stop and remove all docker containers & images
sudo ./clean-docker.sh

# update quickstart script if needed 
rm -rf quickstart.sh
wget -O quickstart.sh https://raw.githubusercontent.com/ColuLocalNetwork/fuse-network/dev/scripts/quickstart.sh
chmod 777 quickstart.sh

# delete the chain database
sudo rm -rf fusenet/database
```

## RPC

Application Gateway

##### Specs
* Tier - `Standard`
* SKU size - `small`
* Instance count - `1`
* HTTP/2 - `Disabled`
* Backend pools - list of bootnode ips
* HTTP settings - http - port 80 & https - port 443
* Health probes - http://127.0.0.1/api/health & https://127.0.0.1/api/health

## App

### Docker
##### Build
```docker build -t fusenet/validator-app .```
##### Pull
```docker pull fusenet/validator-app```
##### Push
```docker push fusenet/validator-app```

#### Run locally
```
# as docker
docker run \
--detach \
--name fuseapp \
--volume /Users/me/dev/io.parity.ethereum/MasterOfCeremony/fusenet/config:/config \
--env POLLING_INTERVAL=1000 \
--env RPC=http://127.0.0.1:8545 \
--restart=on-failure \
fusenet/validator-app

# as script
# from the `app` folder
CONFIG_DIR=../../../io.parity.ethereum/MasterOfCeremony/fusenet/config \
LOG_LEVEL=debug \
POLLING_INTERVAL=1000 \
RPC=http://127.0.0.1:8545 \
CONSENSUS_ADDRESS=0x301...f79 \
node index.js
```


