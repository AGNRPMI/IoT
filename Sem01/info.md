## Linux команды

ip a - определение ip адреса
192.168.31.250/24

clear - очистка консоли или Ctrl + Shift + L

nano /etc/ssh/sshd_config - открыть блокнот по пути
затем найти строку #PermitRootLogin - заменить на PermitRootLogin yes

systemctl restart sshd - перезапуск службы sshd

df -h - статус системы
htop - оперативная память
Ctrl + C - прервать выполнеине последней команды



## создание конфигурации загрузчика
cat > /etc/default/grub <<EOF
GRUB_DEFAULT=0
GRUB_TIMEOUT=0
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet text mitigations=off nowatchdog processor.ignore_ppc=1 cpufreq.default_governor=performance ipv6.disable=1 apparmor=0 selinux=0 debug=-1"
GRUB_CMDLINE_LINUX=""
GRUB_DISABLE_LINUX_RECOVERY=true
GRUB_DISABLE_OS_PROBER=true
GRUB_TERMINAL=console
EOF


## настрока сети, выключение служб, переключение в серверный режим, установка утилит, перезапуск
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf && \
echo "net.ipv4.conf.all.forwarding=1" >> /etc/sysctl.conf && \
sysctl -p /etc/sysctl.conf && \
systemctl stop cron && \
systemctl stop apparmor && \
systemctl stop console-setup && \
systemctl stop keyboard-setup && \
systemctl disable cron && \
systemctl disable apparmor && \
systemctl disable console-setup && \
systemctl disable keyboard-setup && \
systemctl set-default multi-user.target && \
apt install -y sudo curl wget gnupg2 systemd-timesyncd htop && \
sed -i 's/#NTP=/NTP=1.ru.pool.ntp.org/' /etc/systemd/timesyncd.conf && \
systemctl restart systemd-timesyncd && \
update-grub && \
reboot


## установка Mosquito
sudo wget -qO- https://repo.mosquitto.org/debian/mosquitto-repo.gpg.key | sudo tee /etc/apt/trusted.gpg.d/mosquitto-repo.asc && \
sudo wget -O /etc/apt/sources.list.d/mosquitto-bookworm.list https://repo.mosquitto.org/debian/mosquitto-bookworm.list && \
sudo apt update && \
sudo apt-cache show mosquitto | grep Version && \
sudo apt install -y mosquitto=2.0.18-0mosquitto1~bookworm1 mosquitto-clients=2.0.18-0mosquitto1~bookworm1 && \
sudo mosquitto_passwd -c /etc/mosquitto/passwd IoT && \
sudo chmod 777 /etc/mosquitto/passwd


## Создание файла конфигурации Mosquito, отключение анонимного подключения, расположение файла с паролями, указание рабочего порта
sudo cat > /etc/mosquitto/conf.d/default.conf <<EOF
allow_anonymous false
password_file /etc/mosquitto/passwd
listener 1883
EOF


## перезапуск Mosquito
sudo systemctl restart mosquitto

## пересылка и прослушивание сообщений
mosquitto_sub -h 192.168.yyy.xxx -p 1883 -t GB -u "IoT" -P "student"
mosquitto_pub -h 192.168.yyy.xxx -p 1883 -t "GB" -m "Hello, GB!" -u "IoT" -P "student"
mosquitto_pub -h 192.168.yyy.xxx -p 1883 -t "GB" -m "21.3" -u "IoT" -P "student"

## установка Node.js node-red
sudo apt-get update && sudo apt-get install -y ca-certificates curl gnupg && \
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg && \
NODE_MAJOR=20 && \
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list && \
sudo apt-get update && sudo apt-get install nodejs -y && \
bash <(curl -sL https://raw.githubusercontent.com/node-red/linux-installers/master/deb/update-nodejs-and-nodered) && \
sudo systemctl enable nodered && \
sudo systemctl start nodered



## доступ к  node-red
в браузере ввести ip 192.168.xxx.yyy:1880

## установка WireGuard
sudo curl -O https://raw.githubusercontent.com/angristan/wireguard-install/master/wireguard-install.sh && \
sudo chmod +x wireguard-install.sh && \
sudo ./wireguard-install.sh


sudo nano /etc/wireguard/wg0.conf
там необходимо убрать все что касается IPv6


sudo systemctl restart wg-quick@wg0


reboot



## установка influx telegraf grafana
sudo wget -q https://repos.influxdata.com/influxdata-archive_compat.key && \
sudo echo '393e8779c89ac8d958f81f942f9ad7fb82a25e133faddaf92e15b16e6ac9ce4c influxdata-archive_compat.key' | sha256sum -c && cat influxdata-archive_compat.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/influxdata-archive_compat.gpg > /dev/null && \
sudo echo 'deb [signed-by=/etc/apt/trusted.gpg.d/influxdata-archive_compat.gpg] https://repos.influxdata.com/debian stable main' | sudo tee /etc/apt/sources.list.d/influxdata.list && \
sudo apt-get install -y adduser libfontconfig1 musl && \
sudo wget https://dl.grafana.com/oss/release/grafana_10.2.3_amd64.deb && \
sudo dpkg -i grafana_10.2.3_amd64.deb && \
sudo rm /root/grafana_10.2.3_amd64.deb && \
sudo systemctl enable grafana-server && \
sudo systemctl start grafana-server && \
sudo apt-get update && sudo apt-get install -y influxdb2 telegraf && \
sudo systemctl start influxd && \
sudo systemctl enable telegraf


## доступ к influx
в браузере ввести ip 192.168.xxx.yyy:8086
token
KuoESm3SZdK8x_sKz2ZTCaUTtY6umZsQZVxW7degZtplSERbR3gbpvdzMhzr2i_o8AXaYHv8JmLuRv7CuQwNgw==


## настройка telegraf
sudo cat > /etc/telegraf/telegraf.conf <<EOF
# Configuration for telegraf agent
[agent]
  interval = "15s"
  round_interval = true
  metric_batch_size = 250
  metric_buffer_limit = 2500
  collection_jitter = "0s"
  flush_interval = "15s"
  flush_jitter = "0s"
  precision = ""
  hostname = ""
  omit_hostname = false

[[outputs.influxdb_v2]]
  urls = ["http://192.168.31.250:8086"]
  token = "KuoESm3SZdK8x_sKz2ZTCaUTtY6umZsQZVxW7degZtplSERbR3gbpvdzMhzr2i_o8AXaYHv8JmLuRv7CuQwNgw=="
  organization = "IoT"
  bucket = "IoT"

[[inputs.mqtt_consumer]]
  servers = ["tcp://192.168.31.250:1883"]
  topics = ["#"]
  username = "IoT"
  password = "student"
  data_format = "value"
  data_type = "float"
EOF


## Перезагрузка telegrafa
sudo sed -i 14i\ 'RestartSec=2s' /lib/systemd/system/telegraf.service && \
sudo systemctl daemon-reload


## доступ к grafana
http://192.168.31.250:3000/login

задать новый пароль

## установка grafana
sudo sed -i 's/;http_port = 3000/http_port = 80/' /etc/grafana/grafana.ini && \
sudo sed -i 52i\ 'CapabilityBoundingSet=CAP_NET_BIND_SERVICE' /lib/systemd/system/grafana-server.service && \
sudo sed -i 53i\ 'AmbientCapabilities=CAP_NET_BIND_SERVICE' /lib/systemd/system/grafana-server.service && \
sudo sed -i 54i\ 'PrivateUsers=false' /lib/systemd/system/grafana-server.service && \
sudo systemctl daemon-reload && \
sudo systemctl restart grafana-server




