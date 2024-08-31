# ZGS Storage Node Update Guide (v0.4.6)

## 1. Stop the ZGS Service and Backup the Configuration

Before proceeding with the update, stop the ZGS service and create a backup of the current configuration file.

```bash
sudo systemctl stop zgs
cp $HOME/0g-storage-node/run/config.toml $HOME/0g-storage-node/run/config.toml.backup
```

## 2. Update the ZGS Node

Navigate to the storage node directory and pull the latest updates from the repository.

```bash
cd $HOME/0g-storage-node
git stash
git fetch --all --tags
git checkout e3c199d
git submodule update --init
cargo build --release
```

- **`git stash`**: Stashes any local changes.
- **`git fetch --all --tags`**: Fetches all branches, tags, and updates.
- **`git checkout e3c199d`**: Switches to the specified commit or branch (replace `e3c199d` with the appropriate commit hash or branch name).
- **`git submodule update --init`**: Initializes and updates the submodules.
- **`cargo build --release`**: Compiles the project in release mode.

## 3. Restore the Configuration

Restore the backup configuration file to its original location.

```bash
mv $HOME/0g-storage-node/run/config.toml.backup $HOME/0g-storage-node/run/config.toml
```

## 4. Create the ZGS Service

Create or update the systemd service for the ZGS node.

```bash
sudo tee /etc/systemd/system/zgs.service > /dev/null <<EOF
[Unit]
Description=ZGS Node
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/0g-storage-node/run
ExecStart=$HOME/0g-storage-node/target/release/zgs_node --config $HOME/0g-storage-node/run/config.toml
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## 5. Restart the ZGS Service

Reload the systemd manager configuration, enable the service, and start the ZGS node.

```bash
sudo systemctl daemon-reload
sudo systemctl enable zgs
sudo systemctl start zgs
```

## 6. Check the Logs

Monitor the ZGS service logs to ensure everything is running smoothly.

```bash
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d)
```

## 7. Check Block Synchronization Status

You can check the synchronization status by running the following loop, which continuously checks the `logSyncHeight` and `connectedPeers`.

```bash
while true; do 
    response=$(curl -s -X POST http://localhost:5678 -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"zgs_getStatus","params":[],"id":1}')
    logSyncHeight=$(echo $response | jq '.result.logSyncHeight')
    connectedPeers=$(echo $response | jq '.result.connectedPeers')
    echo -e "logSyncHeight: \033[32m$logSyncHeight\033[0m, connectedPeers: \033[34m$connectedPeers\033[0m"
    sleep 5; 
done
```

- **`logSyncHeight`**: Represents the current block height of the node.
- **`connectedPeers`**: Shows the number of connected peers.
