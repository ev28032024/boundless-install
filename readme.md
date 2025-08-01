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
sudo apt-get install -y build-essential dkms linux-headers-$(uname -r)
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
sudo nvidia-smi
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
sudo source ~/.bashrc
```

#### Проверка nvcc
```
sudo nvcc --version
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
sudo apt update -y
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose
```

#### Запуск сервисов Docker
```
sudo systemctl enable docker
sudo systemctl start docker
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
sudo apt update -y
sudo apt install -y nvidia-container-toolkit
```

#### Настройка и перезапуск Docker
```
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

#### Проверка установленного пакета
```
sudo nvidia-container-cli info
```

### 5. Установка Boundless

#### Клонируем репозиторий
```
cd ~ && git clone https://github.com/boundless-xyz/boundless
cd boundless
git checkout release-0.13
```

