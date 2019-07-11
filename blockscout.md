# Blockscout

### Local
```
export \
NETWORK=Fuse \
LOGO=/images/fuse_logo.svg \
ETHEREUM_JSONRPC_VARIANT=parity \
ETHEREUM_JSONRPC_HTTP_URL=http://127.0.0.1:8545 \
ETHEREUM_JSONRPC_TRACE_URL=http://127.0.0.1:8545 \
ETHEREUM_JSONRPC_WS_URL=ws://127.0.0.1:8546
```

### Azure
```
git clone https://github.com/ColuLocalNetwork/blockscout.git

cd blockscout

git checkout fuse

cd docker

# if need to drop & migrate DB
sudo \
NETWORK=Fuse \
LOGO=/images/fuse_logo.svg \
ETHEREUM_JSONRPC_VARIANT=parity \
ETHEREUM_JSONRPC_HTTP_URL=https://znet-explorer-node.fuse.io \
ETHEREUM_JSONRPC_TRACE_URL=https://znet-explorer-node.fuse.io \
ETHEREUM_JSONRPC_WS_URL=wss://znet-explorer-node.fuse.io/ws \
DB_HOST=<host-name>:5432 \
DB_NAME=explorer \
DB_USER=<user> \
DB_PASS=<password> \
make migrate

sudo \
NETWORK=Fuse \
LOGO=/images/fuse_logo.svg \
ETHEREUM_JSONRPC_VARIANT=parity \
ETHEREUM_JSONRPC_HTTP_URL=https://znet-explorer-node.fuse.io \
ETHEREUM_JSONRPC_TRACE_URL=https://znet-explorer-node.fuse.io \
ETHEREUM_JSONRPC_WS_URL=wss://znet-explorer-node.fuse.io/ws \
DB_HOST=<host-name>:5432 \
DB_NAME=explorer \
DB_USER=<user> \
DB_PASS=<password> \
make start
```

#### Install dependencies on ubuntu v18.04-bionic
```
sudo su

apt install libsctp-dev
apt install inotify-tools
apt install autoconf build-essential libgmp3-dev libtool
apt install postgresql postgresql-contrib

# erlang
wget https://packages.erlang-solutions.com/erlang/debian/pool/esl-erlang_21.3.8.4-1~ubuntu~bionic_amd64.deb
dpkg -i esl-erlang_21.3.8.4-1~ubuntu~bionic_amd64.deb

# elixir
apt install elixir

# nodejs
curl -sL https://deb.nodesource.com/setup_10.x | bash -
apt install nodejs
```