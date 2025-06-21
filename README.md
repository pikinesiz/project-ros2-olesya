# Примеры работы с ROS 2 и Docker (Алексей Александрович, в readme все своровано)

Этот репозиторий демонстрирует различные способы организации взаимодействия между узлами ROS 2 в следующих сценариях:

- работа на одном компьютере или нескольких машинах
- использование узлов с Docker и без него
- подключение узлов ROS 2 через локальную сеть или интернет

## Основные примеры

### [Пример 0] Запуск без Docker
Запуск ROS 2 узлов напрямую на хостовой машине:
```bash
cd ros2_ws
colcon build
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 launch my_turtle_bringup turtlesim_demo.launch.py
```

### [Пример 1] Запуск в одном контейнере
Сборка и запуск всех узлов в едином Docker-контейнере:
```bash
docker build -t turtle_demo -f eg1/Dockerfile .
xhost local:root
docker run --rm -it --env DISPLAY --volume /tmp/.X11-unix:/tmp/.X11-unix:rw turtle_demo \
  ros2 launch my_turtle_bringup turtlesim_demo.launch.py
```

### [Пример 2] Запуск в двух контейнерах
Использование docker-compose для организации многоконтейнерного приложения:
```bash
cd eg2
docker-compose up --build
```

### [Пример 3] Решение проблемы соединения через разные сети
Использование VPN (Husarnet) для соединения ROS 2 узлов в разных сетях:
1. Настройка Join Code в файлах .env
2. Запуск на первом устройстве:
```bash
cd eg3/dev1
docker-compose up --build
```
3. Запуск на втором устройстве:
```bash
cd eg3/dev2
docker-compose up --build
```

### [Пример 4] Использование отдельного VPN-контейнера
Более гибкое решение с выделенным контейнером для VPN:
```bash
# На устройстве 1:
cd eg4/dev1
docker-compose up --build

# На устройстве 2:
cd eg4/dev2
docker-compose up --build
```

## Технические требования

Перед началом работы убедитесь, что у вас установлены:
- Docker и Docker-Compose
- ROS 2 Foxy (только для примера 0)

Инструкция по установке Docker для Linux:
```bash
sudo apt-get install apt-transport-https ca-certificates software-properties-common
curl -sL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
arch=$(dpkg --print-architecture)
sudo add-apt-repository "deb [arch=${arch}] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce docker-compose
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## Особенности реализации

Решение масштабируемо и может быть применено к сложным распределённым системам ROS 2. В репозитории представлены различные архитектурные подходы - от простого одноконтейнерного решения до сложных распределённых систем с автоматическим обновлением конфигурации DDS.
