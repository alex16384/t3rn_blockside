#!/bin/bash
    sudo apt update -y
    sudo apt upgrade -y
    sudo apt install -y curl figlet jq build-essential gcc unzip wget lz4 bc

    install_dependencies

    echo -e "${CLR_INFO}Скачиваение и установка актуальной версии ноды t3rn...${CLR_RESET}"
    LATEST_VERSION=$(curl -s https://api.github.com/repos/t3rn/executor-release/releases/latest | grep 'tag_name' | cut -d\" -f4)
    EXECUTOR_URL="https://github.com/t3rn/executor-release/releases/download/${LATEST_VERSION}/executor-linux-${LATEST_VERSION}.tar.gz"
    curl -L -o executor-linux-${LATEST_VERSION}.tar.gz $EXECUTOR_URL
    tar -xzvf executor-linux-${LATEST_VERSION}.tar.gz
    rm -rf executor-linux-${LATEST_VERSION}.tar.gz

    echo -e "${CLR_WARNING}Введите private key EVM (нужен балланс в ETH Sepolia:${CLR_RESET}"
    read -r PRIVATE_KEY

    CONFIG_FILE="$HOME/executor/executor/bin/.t3rn"
    mkdir -p $HOME/executor/executor/bin/
    echo "NODE_ENV=testnet" > $CONFIG_FILE
    echo "LOG_LEVEL=debug" >> $CONFIG_FILE
    echo "LOG_PRETTY=false" >> $CONFIG_FILE
    echo "EXECUTOR_PROCESS_ORDERS=true" >> $CONFIG_FILE
    echo "EXECUTOR_PROCESS_CLAIMS=true" >> $CONFIG_FILE
    echo "PRIVATE_KEY_LOCAL=$PRIVATE_KEY" >> $CONFIG_FILE
    echo "ENABLED_NETWORKS='arbitrum-sepolia,base-sepolia,optimism-sepolia,l1rn'" >> $CONFIG_FILE
    echo "RPC_ENDPOINTS_BSSP='https://base-sepolia-rpc.publicnode.com'" >> $CONFIG_FILE

    # Создание systemd-сервиса
    sudo bash -c "cat <<EOT > /etc/systemd/system/t3rn.service
[Unit]
Description=t3rn Node
After=network.target

[Service]
EnvironmentFile=$HOME/executor/executor/bin/.t3rn
ExecStart=$HOME/executor/executor/bin/executor
WorkingDirectory=$HOME/executor/executor/bin/
Restart=on-failure
User=$(whoami)

[Install]
WantedBy=multi-user.target
EOT"

    # Запуск сервиса
    sudo systemctl daemon-reload
    sudo systemctl enable t3rn
    sudo systemctl start t3rn

    echo -e "${CLR_SUCCESS}Установка завершена!${CLR_RESET}"
    echo -e "Для просмотра логов наберите sudo journalctl -u t3rn -f"
