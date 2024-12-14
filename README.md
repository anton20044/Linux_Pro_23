Лабораторная работа №23 по курсу Linux Pro.

Задание:

1) Настроить VPN между двумя ВМ в tun/tap режимах, замерить скорость в туннелях, сделать вывод об отличающихся показателях
2) Поднять RAS на базе OpenVPN с клиентскими сертификатами, подключиться с локальной машины на ВМ
	Задание со звездочкой
3) Самостоятельно изучить и настроить ocserv, подключиться с хоста к ВМ


Решение:

1) Подготовлен стенд, состоящий из двух виртуальных машин server и client.
 
 - ansible-playbook -i ansible/hosts ansible/provision.yml -t openvpn - для выполнения задания по пункту 1, по умолчанию tap (см tap.jpg)
 - ansible-playbook -i ansible/hosts ansible/provision.yml -t change_type -e openvpn_type_int=tun - перенастраивает стенд под использование tun (см tun.jpg)
 - Разница в скорости объмена между узлами в режиме tap/tun примерно 10%, в пользу tun.

2) Поднять RAS на базе OpenVPN с клиентскими сертификатами, подключиться с локальной машины на ВМ
 
 - Установил на ВМ сервера и клиента openvpn и easy-rsa
 - Инициализировал PKI в каталоге /etc/openvpn/  /usr/share/easy-rsa/easyrsa init-pki
 - Сгенерировал CA echo 'rasvpn' | /usr/share/easy-rsa/easyrsa build-ca nopass
 - Создал запрос на сертификат для сервера echo 'rasvpn' | /usr/share/easy-rsa/easyrsa gen-req server nopass
 - Создал сертификат для сервера в по запросу echo 'yes' | /usr/share/easy-rsa/easyrsa sign-req server server
 - Сгенерировал ключ Диффи-Хеллмана /usr/share/easy-rsa/easyrsa gen-dh
 - Создал запрос на сертификат для клиента echo 'client' | /usr/share/easy-rsa/easyrsa gen-req client nopass
 - Сгенерировал сертификат для клиента по запросу echo 'yes' | /usr/share/easy-rsa/easyrsa sign-req client client
 - Записал маршрут для клиента echo 'iroute 10.10.10.0 255.255.255.0' > /etc/openvpn/client/client
 - Создал конфиг сервера (см. файл server.conf)
 - Запустил службу OpenVPN и добавил ее в автозагрузку systemctl start openvpn@server && systemctl enable openvpn@server
 - На ВМ клиента добавил конфиг службы в каталог /etc/openvpn (см файл client.conf)
 - Туда же положил сертификаты ca.crt, client.crt, client.key
 - Запустил клиента openvpn --config client.conf
 - Туннель поднялся (см скриншот 1)



Разворачивание ВМ и их настройка с помощью vagrant и ansible.