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

### Azure

##### PostgreSQL
* Server name - `xxx-postgresql.postgres.database.azure.com` is used as `DB_HOST` when running
* Admin username - `user@xxx-postgresql` is used as `DB_USER` when running
* PostgreSQL version - `10`
* Performance configuration - `Basic, 2 vCore(s), 100 GB`
* SSL enforce status - `DISABLED`
* Connection security - add explorer VM ip as well as other allowed like office/vpn

##### VM specs
* OS - `Linux (ubuntu 18.04)`
* Size - `Standard B2ms (2 vcpus, 8 GiB memory)`
* Disk - `30 GiB Premium SSD`
* Networking

```
| Priority  | Description                     | Port    | Protocol  | Source                    | Destination     | Action  |
|---------- |-------------------------------- |-------  |---------- |-------------------------  |---------------- |-------- |
| 1000      | ssh                             | 22      | TCP       | ip list comma-separated   | Any             | Allow   |
| 1001      | https                           | 443     | TCP       | Any                       | Any             | Allow   |
| 1002      | http                            | 80      | TCP       | Any                       | Any             | Allow   |
| 65000     | AllowVnetInBound                | Any     | Any       | VirtualNetwork            | VirtualNetwork  | Allow   |
| 65001     | AllowAzureLoadBalancerInBound   | Any     | Any       | AzureLoadBalancer         | Any             | Allow   |
| 65500     | DenyAllInBound                  | Any     | Any       | Any                       | Any             | Deny    |
```

##### Run

```
git clone https://github.com/fuseio/blockscout.git

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
DB_HOST=<server-name>:5432 \
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
DB_HOST=<server-name>:5432 \
DB_NAME=explorer \
DB_USER=<user> \
DB_PASS=<password> \
make start
```
