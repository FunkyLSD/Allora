
# Allora Node Guide | Гайд по ноде Аллора

# Требования к серверу:
2CPU/4GB RAM/5 GB SSD

# Установка
Подготавливаем сервер:
sudo apt-get update -y && sudo apt upgrade -y && sudo apt-get install make build-essential unzip lz4 gcc git jq ncdu tmux cmake clang pkg-config libssl-dev python3-pip protobuf-compiler bc -y
#устанавливаем го 
sudo rm -rf /usr/local/go

curl -Ls https://go.dev/dl/go1.22.2.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local

eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)

eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)

go version
#ответ - go version go1.22.2 
Устанавливаем докер и докер компоуз:
sudo apt install -y ca-certificates curl gnupg lsb-release 

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io

sudo usermod -aG docker $USER

newgrp docker
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
# Выкачиваем репозиторий проекта и билдим:
git clone https://github.com/allora-network/allora-chain.git

cd allora-chain

make all

cd

allorad version
#ответ - v0.2.12
Создаем кошелек:
allorad keys add wallet
#дважды вводим пароль (придумываем)
#записываем адрес и мнемонику!!!
Идем сюда и запрашиваем токены на свой кошелек
git clone https://github.com/allora-network/basic-coin-prediction-node

cd basic-coin-prediction-node

mkdir worker-data && sudo chmod -R 777 worker-data

mkdir head-data && sudo chmod -R 777 head-data

sudo docker run -it --entrypoint=bash -v ./head-data:/data alloranetwork/allora-inference-base:latest -c "mkdir -p /data/keys && (cd /data/keys && allora-keys)"

sudo docker run -it --entrypoint=bash -v ./worker-data:/data alloranetwork/allora-inference-base:latest -c "mkdir -p /data/keys && (cd /data/keys && allora-keys)"

cat head-data/keys/identity
#запоминаем айдентити
rm -rf docker-compose.yml 

wget https://raw.githubusercontent.com/kulikovae/allora/55a7f69026339cb45b791a15718d8354cd173151/docker-compose.yml
nano docker-compose.yml
#заменяем ALLORA_HEAD_ID на айдентити, который вы скопировали ранее
#заменяем ALLORA_MNEMONIC на мнемонику вашего кошелька

docker compose build

docker compose up -d
# Проверка логов:
docker compose logs
