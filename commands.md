# Commands Used During Troubleshooting

В этом файле содержатся команды, использованные в ходе расследования, и описание назначения каждой команды.

## Check IP Configuration

```cmd
ipconfig /all
```

Используется для проверки:

- IPv4 address
- Subnet mask
- Default gateway
- DNS servers
- DNS suffix
- DHCP status

## Check Configured DNS Servers

```cmd
netsh interface ipv4 show dnsservers
```

Используется для подтверждения того, настроены ли DNS-серверы IPv4 на сетевом адаптере Ethernet.

## Test Gateway Reachability

```cmd
ping 10.10.40.161
tracert 10.10.40.161
```

Используется для проверки возможности рабочей станции установить связь со своим шлюзом по умолчанию.

## Test DNS Servers

```cmd
ping 10.10.50.21
ping 10.10.51.21
tracert 10.10.50.21
```

Используется для проверки доступности внутренних DNS-серверов с проблемной рабочей станции.

## Test Domain Name Resolution

```cmd
nslookup corp.local
ping corp.local
```

Используется для проверки возможности разрешения доменного имени компании.

## Discover Domain Controller

```cmd
nltest /dsgetdc:corp.local
```

Используется для проверки того, может ли рабочая станция обнаружить контроллер домена.

## Check Routes

```cmd
route print
```

Используется для проверки таблицы маршрутизации IPv4.

## Check ARP Table

```cmd
arp -a
```

Используется для подтверждения того, что адреса локальных шлюзов видны на уровне 2.

## Check Current User

```cmd
whoami
net user %username%
```

Используется для подтверждения наличия у текущего пользователя локальных административных привилегий.

## Check Local Administrators

```cmd
net localgroup administrators
net user administrator
```

Используется для идентификации локальных учетных записей администратора и проверки активности встроенной учетной записи администратора.
