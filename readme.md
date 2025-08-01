### 1. Установка NVIDIA Drivers

#### Удаление старого драйвера NVIDIA и пакеты CUDA
```
sudo apt-get remove --purge '^nvidia-.*' 'cuda-*' 'libcudnn*' && sudo apt-get autoremove && sudo apt-get autoclean
sudo rm -rf /var/lib/dkms/nvidia /usr/local/cuda* /usr/src/nvidia* /usr/bin/nvidia*
sudo ldconfig
```

#### Перезагрузка системы
```
sudo reboot
```

#### Установка зависимостей
```
sudo apt-get update -y
sudo apt-get install -y ubuntu-drivers-common build-essential dkms linux-headers-$(uname -r)
```

#### Установка PPA и драйвера
```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update -y
ubuntu-drivers devices // (копируем тот что рекомендованный и в следующей команде устанавливаем именно его)
sudo apt install -y nvidia-driver-<версия>
```

#### Перезагрузка системы
```
sudo reboot
```

#### Проверка загрузки драйвера
```
nvidia-smi
```

### 2. Установка CUDA Toolkit

#### Добавление репозитория CUDA
```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.9.1/local_installers/\
cuda-repo-ubuntu2204-12-9-local_12.9.1-575.57.08-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2204-12-9-local_12.9.1-575.57.08-1_amd64.deb
sudo cp /var/cuda-repo-ubuntu2204-12-9-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update -y
```

#### Установка CUDA Toolkit
```
sudo apt-get install -y cuda-toolkit-12-9
```

#### Переменные окружения  --- (добавить строки в самый низ файла ~/.bashrc)
```
export PATH=${PATH}:/usr/local/cuda-12.9/bin
export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda-12.9/lib64
```

#### Импортировать настройки
```
source ~/.bashrc
```

#### Проверка nvcc
```
nvcc --version
```

### 3. Установка Docker

#### Установка зависимостей
```
sudo apt-get update -y
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

#### Добавление репозитория Docker
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### Установка Docker
```
sudo apt-get update -y
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose
```

#### Запуск сервисов Docker
```
sudo systemctl enable docker
sudo systemctl start docker
```

#### Добавить активного пользователя в группу
```
sudo usermod -aG docker $USER
```

#### Проверка установленного Docker
```
docker -v
docker-compose -v
```

### 4. Установка NVIDIA Container Toolkit

#### Установка ключа и репозитория
```
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

#### Включаем эксперементальные пакеты
```
sed -i -e '/experimental/ s/^#//g' /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

#### Установка NVIDIA Container Toolkit
```
sudo apt-get update -y
export NVIDIA_CONTAINER_TOOLKIT_VERSION=1.17.8-1
  sudo apt-get install -y \
      nvidia-container-toolkit=${NVIDIA_CONTAINER_TOOLKIT_VERSION} \
      nvidia-container-toolkit-base=${NVIDIA_CONTAINER_TOOLKIT_VERSION} \
      libnvidia-container-tools=${NVIDIA_CONTAINER_TOOLKIT_VERSION} \
      libnvidia-container1=${NVIDIA_CONTAINER_TOOLKIT_VERSION}
```

#### Настройка и перезапуск Docker
```
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

#### Проверка установленного пакета
```
nvidia-container-cli info
```

### 5. Установка Boundless

#### Установка зависимостей
```
sudo apt update -y && sudo apt upgrade -y
sudo apt install curl iptables build-essential git wget lz4 jq make gcc gnupg ca-certificates lsb-release nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev tar clang bsdmainutils ncdu unzip libleveldb-dev libclang-dev ninja-build nvtop -y
```

#### Клонируем репозиторий
```
cd ~ && git clone https://github.com/boundless-xyz/boundless
cd boundless
git checkout release-0.13
```

#### Установить rustup
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source "$HOME/.cargo/env"
```

#### Обновить rustup
```
rustup update
```

#### Установить инструменты Rust
```
sudo apt update
sudo apt install -y cargo
```

#### Проверить версию Cargo
```
cargo --version
```

#### Установить rzup
```
curl -L https://risczero.com/install | bash
source ~/.bashrc
```

#### Проверить rzup
```
rzup --version
```

#### Установить RISC Zero Rust Toolchain
```
rzup install rust
```

#### Установить cargo-risczero
```
cargo install cargo-risczero
rzup install cargo-risczero
```

#### Повторно обновить rustup (на всякий случай)
```
rustup update
```

#### Установить Bento-клиент
```
cargo install --locked --git https://github.com/risc0/risc0 bento-client --branch release-2.3 --bin bento_cli
echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

#### Проверить Bento-клиент
```
bento_cli --version
```

#### Установить Boundless CLI (версия 13)
```
cargo install --locked boundless-cli
export PATH=$PATH:/root/.cargo/bin
source ~/.bashrc
```

#### Проверить boundless-cli
```
boundless -h
```

#### Установить Just (утилита для управления задачами)
```
curl --proto '=https' --tlsv1.2 -sSf https://just.systems/install.sh | sudo bash -s -- --to /usr/local/bin
```

#### Проверить Just
```
just --version
```

### 6. Включение перераспределения памяти (memory overcommit) в Linux
>Чтобы предотвратить сбои приложений при высокой нагрузке на память, рекомендуется включить перераспределение памяти, позволяющее системе выделять больше памяти, чем физически доступно.
```
grep -q '^vm.overcommit_memory=' /etc/sysctl.conf && sudo sed -i 's/^vm.overcommit_memory=.*/vm.overcommit_memory=1/' /etc/sysctl.conf || echo 'vm.overcommit_memory=1' | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```
