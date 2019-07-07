# Fuse Network

## Network

### Docker
##### Build
```docker build -f docker/Dockerfile -t fusenetwork/fusenet .```
##### Pull
```docker pull fusenetwork/fusenet```
##### Push
```docker push fusenetwork/fusenet```

### Bootnode
```
# start
sudo ./quickstart.sh --role bootnode --node-key zeronet-bootnode-vm-<N>

# or, start docker manually
docker run -ti -v /home/fuse/fusenet/database:/data -v /home/fuse/fusenet/config:/config/custom -p 30300:30300 -p 8545:8545 -p 8546:8546 fusenetwork/fusenet --role bootnode --parity-args --node-key bootnode<N>
```

### MasterOfCeremony / Validator
```
# start
sudo ./quickstart.sh --role validator

# or, start dockers manually
sudo docker run --detach --name fusenet --volume /home/fuse/fusenet/database:/data --volume /home/fuse/fusenet/config:/config/custom -p 30303:30300 --restart=on-failure fusenetwork/fusenet --role validator --address <address>

sudo docker run --detach --name fuseapp --volume /home/fuse/fusenet/config:/config --restart=on-failure fusenetwork/fuseapp
```

### Explorer node
```
# start
sudo ./quickstart.sh --role explorer

# or, start docker manually
sudo docker run --detach --name fusenet --volume /home/fuse/fusenet/database:/data --volume /home/fuse/fusenet/config:/config/custom -p 30303:30300 -p 8545:8545 -p 8546:8546 --restart=on-failure fusenetwork/fusenet --role explorer --parity-args --node-key zeronet-explorer-vm
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

## App

### Docker
##### Build
```docker build -f docker/Dockerfile -t fusenetwork/fuseapp .```
##### Pull
```docker pull fusenetwork/fuseapp```
##### Push
```docker push fusenetwork/fuseapp```

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
fusenetwork/fuseapp

# as script
# from the `app` folder
CONFIG_DIR=../../../io.parity.ethereum/MasterOfCeremony/fusenet/config \
LOG_LEVEL=debug \
POLLING_INTERVAL=1000 \
RPC=http://127.0.0.1:8545 \
CONSENSUS_ADDRESS=0x301...f79 \
node index.js
```


