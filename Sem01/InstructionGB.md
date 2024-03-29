исходник https://gbcdn.mrgcdn.ru/uploads/asset/5358513/attachment/be2cd22a5846d82e5e1df970e4533e1e.pdf 

Семинар №1
Развёртывание своей системы
визуализации
1. Инструментарий:
1) ПО для виртуализации и создания виртуальных машин
a) Win/Linux (бесплатно для некоммерческого использования)
i) VMware Workstation Player 17:
https://customerconnect.vmware.com/en/downloads/info/slug/deskt
op_end_user_computing/vmware_workstation_player/17_0
b) MacOS (есть бесплатный Trial период)
i) VMware Fusion Player 13:
https://www.vmware.com/go/getfusion
2) SSH клиент:
a) Termius for Windows: https://termius.com/free-ssh-client-for-windows
b) Termius for Linux: https://termius.com/free-ssh-client-for-linux
c) Termius for MacOS: https://termius.com/free-ssh-client-for-mac-os
3) ОС Linux Debian 11.6 (скачать одно из двух):
a) Последний актуальный стабильный net-install образ для платформы
x86(amd64):
https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/
b) Либо последний актуальный тестовый net-install образ для
платформы x86(amd64):
https://cdimage.debian.org/cdimage/daily-builds/daily/arch-latest/amd6
4/iso-cd/debian-testing-amd64-netinst.iso
2. Цели семинара №1 – получить навыки по:
● развертыванию и настройке виртуальных машин;
● установке и настройке ОС Linux Debian
● работе с SSH
● Установке и настройке программных компонентов: Mosquitto,
Node-RED, Telegraf, InfluxDB, Grafana, Wireguard
● Объединению программных компонентов в систему визуализации
По итогам семинара №1 слушатель должен знать и уметь:
● Как разворачивать и настраивать виртуальные машины при
помощи решения от VMware
● Устанавливать и настраивать ОС Linux Debian
● Выполнять подключение по SSH к ВМ
● Устанавливать и настраивать программные компоненты системы
визуализации: Mosquitto, Node-RED, Telegraf, InfluxDB, Grafana,
Wireguard
● Объединять программные компоненты в систему визуализации



1. Оконечное оборудование.
Собирает телеметрию, доступом в интернет, разрабатываемое
студентами индивидуально. На оконечном оборудовании реализуется MQTT
клиент, который публикует в определённый топик MQTT брокера некую
информацию, например, температуру окружающей среды в градусах цельсия.
2. Mosquitto MQTT broker.
MQTT брокер служит неким посредником, который позволяет собирать
(принимать) информацию от устройств в определенные топики и сразу же
«отдавать» ее клиентам, которые эти топики слушают для сбора, обработки,
анализа, визуализации получаемых данных.
3. Telegraf.
Чтобы получить информацию от MQTT брокера, также необходим
клиент, который подписывается на топики брокера для получения данных,
реализуя тем самым паттерн Издатель-подписчик (англ. publisher-subscriber
или англ. pub/sub). Для этого и используется Telegraf. Он, как
клиент-подписчик, прослушивает заданные MQTT топики и, получив
сообщение, сразу же записывает информацию в InfluxDB. Вообще, Telegraf –
это не только MQTT клиент. Telegraf – это серверный агент для сбора,
обработки, агрегирования и записи метрик из различных стеков, датчиков и
систем в InfluxDB.
4. InfluxDB.
InfluxDB – это система баз данных, оптимизированная для
предоставления данных временных рядов и их хранения в привязке ко
времени и значению (иными словами база данных временных рядов (time
series database – TSDB)), разработанная компанией InfluxData.
Временной ряд – это совокупность наблюдений за четко
определенными элементами данных, полученных в результате повторяющихся
измерений в течение определенного времени. Данные временного ряда
индексируются во временном порядке, который представляет собой
последовательность точек данных.
В InfluxDB есть встроенный конструктор дашбордов для визуализации –
Chronograf, на котором можно было бы и остановиться, но он больше сделан
для быстрого прототипирования дашбордов и проверки отображения тех или
иных метрик в том или ином виде в самой InfluxDB. Гораздо удобнее
использовать платформу Grafana, которая является плюс минус стандартном в
вопросах визуализации (особенно в сфере IoT). Для этого будет настраиваться
интеграция в Grafana на подключение к InfluxDB.
5. Grafana.
Grafana – программное обеспечение с открытым исходным кодом,
позволяющее запрашивать, визуализировать и исследовать метрики (данные),
где бы они ни хранились. По сути, предоставляет пользователю инструменты
для преобразования информации из базы данных временных рядов (TSDB) в
удобные графики и визуализации.
6. Node-RED*.
Это инструмент визуального программирования для интернета вещей,
позволяющий подключать друг к другу устройства, API и онлайн-сервисы.
Выступает в роли многофункцильного бекэнда с поддержкой множества
интерфейсов и протоколов.
Звездочка здесь стоит только потому, что данный компонент не
обязателен, но всегда должна быть альтернатива, и Node-RED позволяет
собирать и передавать данные альтернативным от Telegraf способом, имея
мощный функционал промежуточной обработки.
7. WireGuard.
Современный VPN-протокол. Необходим, чтобы подключаться к
виртуальной машине из любой точки через домашнюю сеть. Приятное
дополнение – это возможность шифровать трафик в любой публичной Wi-Fi
сети без существенных ограничений по скорости и задержкам.
