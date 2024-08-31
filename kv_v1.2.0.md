# ZGS KV Storage Node Update Guide

## 1. Installation and Cleanup

Before starting the update, stop the existing 0gkv service, backup the current configuration, and remove the old installation.

```bash
sudo systemctl stop 0gkv
cp $HOME/0g-storage-kv/run/config.toml $HOME/config.toml.backup
rm -r $HOME/0g-storage-kv
```

- **`sudo systemctl stop 0gkv`**: Stops the running 0gkv service.
- **`cp $HOME/0g-storage-kv/run/config.toml $HOME/config.toml.backup`**: Creates a backup of the configuration file.
- **`rm -r $HOME/0g-storage-kv`**: Removes the existing 0g-storage-kv directory.

## 2. Update the ZGS KV Node

Clone the latest version of the ZGS KV repository and build the project.

```bash
git clone -b v1.2.0 https://github.com/0glabs/0g-storage-kv.git
cd $HOME/0g-storage-kv
git stash
git fetch --all --tags
git checkout 221e82a
git submodule update --init
cargo build --release
```

- **`git clone -b v1.2.0`**: Clones the repository from the specified branch (v1.2.0).
- **`git stash`**: Stashes any local changes.
- **`git fetch --all --tags`**: Fetches all branches, tags, and updates.
- **`git checkout 221e82a`**: Switches to the specified commit or branch (replace `221e82a` with the appropriate commit hash or branch name).
- **`git submodule update --init`**: Initializes and updates the submodules.
- **`cargo build --release`**: Compiles the project in release mode.

## 3. Restore the Configuration

Restore the backup configuration file to the new installation.

```bash
mv $HOME/config.toml.backup $HOME/0g-storage-kv/run/config.toml
```

- **`mv $HOME/config.toml.backup $HOME/0g-storage-kv/run/config.toml`**: Moves the backup configuration file to the new installation directory.

## 4. Create the ZGS KV Service

Create or update the systemd service for the ZGS KV node.

```bash
sudo tee /etc/systemd/system/0gkv.service > /dev/null <<EOF
[Unit]
Description=0G-KV Node
After=network.target

[Service]
User=root
WorkingDirectory=/root/0g-storage-kv/run
ExecStart=/root/0g-storage-kv/target/release/zgs_kv --config /root/0g-storage-kv/run/config.toml
Restart=always
RestartSec=10
LimitNOFILE=65535
StandardOutput=journal
StandardError=journal
SyslogIdentifier=zgs_kv

[Install]
WantedBy=multi-user.target
EOF
```

- **`/etc/systemd/system/0gkv.service`**: The location of the systemd service file.
- **`ExecStart`**: Defines the command to start the ZGS KV service with the appropriate configuration file.
- **`Restart=always`**: Ensures the service restarts automatically in case of failure.

## 5. Restart the ZGS KV Service

Reload the systemd manager configuration, enable the service, and start the ZGS KV node.

```bash
sudo systemctl daemon-reload
sudo systemctl enable 0gkv
sudo systemctl start 0gkv
```

- **`sudo systemctl daemon-reload`**: Reloads the systemd configuration.
- **`sudo systemctl enable 0gkv`**: Enables the service to start on boot.
- **`sudo systemctl start 0gkv`**: Starts the ZGS KV service.

## 6. Check the Logs

Monitor the ZGS KV service logs to ensure everything is functioning correctly.

```bash
sudo journalctl -u 0gkv -f -o cat
```

- **`sudo journalctl -u 0gkv -f -o cat`**: Continuously displays the service logs in real-time.

## 8. Check Status via Web

You can also check the status of the service through the web interface:

- **Web Interface**: [Storage Scan - Newton](https://storagescan-newton.0g.ai/)
