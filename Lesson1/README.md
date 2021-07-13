# Отчет по заданию к уроку 1


# Основное задание

* добавить нового пользователя, предоставить ему ограниченные права.
* настроить ступенчатую рассылку оповещений: для нового пользователя через 3 минуты, для админа через 10 минут

Развернут Zabbix, добавлен пользователь Alex, настроена ступенчатая рассылка оповещений

Скриншот:
![alt text](dashboard.png "Actions")

## Дополнительное задание

**\*Используя API Zabbix:**
* Получить все хосты и хост-группы;
* Создать новую хост-группу GeekBrains HostGroup и добавить в неё новый хост GeekBrains-host, подключить к этому хосту шаблон Template OS Linux by Zabbix agent.

### 1. Получение токена авторизации
<code>
curl http://127.0.0.1/api_jsonrpc.php -s -X POST -H 'Content-Type: application/json-rpc' \
-d '{"jsonrpc": "2.0", "method": "user.login", "params": {"user": "Admin", "password": "zabbix"}, "id": 1,"auth": null}'  | jq -r .result
</code>

<pre>
358a6686ecd633171e32dfc01f8305bb
</pre>
## 2. Получение списка узлов
<code>
curl http://127.0.0.1/api_jsonrpc.php -s -X POST -H 'Content-Type: application/json-rpc' \
-d '{"jsonrpc": "2.0", "method": "host.get", "params": {"output": ["hostid", "host"], "selectInterfaces": ["interfaceid", "ip"]}, "id": 2,"auth": "358a6686ecd633171e32dfc01f8305bb"}' | jq -r .result
</code>
<pre>
[
  {
    "hostid": "10084",
    "host": "Zabbix server",
    "interfaces": [
      {
        "interfaceid": "1",
        "ip": "127.0.0.1"
      }
    ]
  }
]
</pre>

## 3. Получение списка групп узлов
<code>
curl http://127.0.0.1/api_jsonrpc.php -s -X POST -H 'Content-Type: application/json-rpc' \
-d '{"jsonrpc": "2.0", "method": "hostgroup.get", "params": {"output": "extend"}, "id": 1,"auth": "358a6686ecd633171e32dfc01f8305bb"}' | jq -r .result
</code>
<pre>
[
  {
    "groupid": "1",
    "name": "Templates",
    "internal": "0",
    "flags": "0",
    "uuid": "7df96b18c230490a9a0a9e2307226338"
  },
  {
    "groupid": "2",
    "name": "Linux servers",
    "internal": "0",
    "flags": "0",
    "uuid": "dc579cd7a1a34222933f24f52a68bcd8"
  },
  {
    "groupid": "4",
    "name": "Zabbix servers",
    "internal": "0",
    "flags": "0",
    "uuid": "6f6799aa69e844b4b3918f779f2abf08"
  },
  {
    "groupid": "5",
    "name": "Discovered hosts",
    "internal": "1",
    "flags": "0",
    "uuid": "f2481361f99448eea617b7b1d4765566"
  },
  {
    "groupid": "6",
    "name": "Virtual machines",
    "internal": "0",
    "flags": "0",
    "uuid": "137f19e6e2dc4219b33553b812627bc2"
  },
  {
    "groupid": "7",
    "name": "Hypervisors",
    "internal": "0",
    "flags": "0",
    "uuid": "1b837a3c078647049a0c00c61b4d57b5"
  },
  {
    "groupid": "8",
    "name": "Templates/Modules",
    "internal": "0",
    "flags": "0",
    "uuid": "57b7ae836ca64446ba2c296389c009b7"
  },
  {
    "groupid": "9",
    "name": "Templates/Network devices",
    "internal": "0",
    "flags": "0",
    "uuid": "36bff6c29af64692839d077febfc7079"
  },
  {
    "groupid": "10",
    "name": "Templates/Operating systems",
    "internal": "0",
    "flags": "0",
    "uuid": "846977d1dfed4968bc5f8bdb363285bc"
  },
  {
    "groupid": "11",
    "name": "Templates/Server hardware",
    "internal": "0",
    "flags": "0",
    "uuid": "e960332b3f6c46a1956486d4f3f99fce"
  },
  {
    "groupid": "12",
    "name": "Templates/Applications",
    "internal": "0",
    "flags": "0",
    "uuid": "a571c0d144b14fd4a87a9d9b2aa9fcd6"
  },
  {
    "groupid": "14",
    "name": "Templates/Virtualization",
    "internal": "0",
    "flags": "0",
    "uuid": "02e4df4f20b848e79267641790f241da"
  },
  {
    "groupid": "15",
    "name": "Templates/Telephony",
    "internal": "0",
    "flags": "0",
    "uuid": "1d12408342854fd5a4436dd6d5d1bd4a"
  },
  {
    "groupid": "16",
    "name": "Templates/SAN",
    "internal": "0",
    "flags": "0",
    "uuid": "7c2cb727f85b492d88cd56e17127c64d"
  },
  {
    "groupid": "17",
    "name": "Templates/Video surveillance",
    "internal": "0",
    "flags": "0",
    "uuid": "d37f71c7e3f7469bab645852a69a2018"
  },
  {
    "groupid": "18",
    "name": "Templates/Power",
    "internal": "0",
    "flags": "0",
    "uuid": "3dcd5bbe90534f9e8eb5c2d53756af63"
  }
]
</pre>

## 4. Создание группы
<code>
curl http://127.0.0.1/api_jsonrpc.php -s -X POST -H 'Content-Type: application/json-rpc' \
-d '{"jsonrpc": "2.0", "method": "hostgroup.create", "params": {"name": "GeekBrains HostGroup"},"auth": "358a6686ecd633171e32dfc01f8305bb", "id": 1}'
</code>
<pre>
{"jsonrpc":"2.0","result":{"groupids":["19"]},"id":1}
</pre>

## 5. Создание узла в группе

<code>
curl http://127.0.0.1/api_jsonrpc.php -s -X POST -H 'Content-Type: application/json-rpc' \
-d '{
    "jsonrpc": "2.0",
    "method": "host.create",
    "params": {
        "host": "GeekBrains-host",
        "interfaces": [
            {
                "type": 1,
                "main": 1,
                "useip": 1,
                "ip": "192.168.3.1",
                "dns": "",
                "port": "10050"
            }
        ],
        "groups": [
            {
                "groupid": "19"
            }
        ]
    },
    "auth": "358a6686ecd633171e32dfc01f8305bb",
    "id": 1
}' | jq -r .result
</code>
<pre>
{
  "hostids": [
    "10413"
  ]
}
</pre>

## 6. Получение данных шаблона

<code>
curl http://127.0.0.1/api_jsonrpc.php -s -X POST -H 'Content-Type: application/json-rpc' \
-d '{
    "jsonrpc": "2.0",
    "method": "template.get",
    "params": {
        "output": "extend",
        "filter": {
            "host": [
                "Linux by Zabbix agent"
            ]
        }
    },
    "auth": "358a6686ecd633171e32dfc01f8305bb",
    "id": 1
}' | jq -r .result
</code>
<pre>
[
  {
    "proxy_hostid": "0",
    "host": "Linux by Zabbix agent",
    "status": "3",
    "lastaccess": "0",
    "ipmi_authtype": "-1",
    "ipmi_privilege": "2",
    "ipmi_username": "",
    "ipmi_password": "",
    "maintenanceid": "0",
    "maintenance_status": "0",
    "maintenance_type": "0",
    "maintenance_from": "0",
    "name": "Linux by Zabbix agent",
    "flags": "0",
    "templateid": "10001",
    "description": "Official Linux template. Requires agent of Zabbix 3.0.14, 3.4.5 and 4.0.0 or newer.\r\n\r\nKnown Issues:\r\n\r\n  Description: Network discovery. Zabbix agent as of 4.2 doesn't support items such as net.if.status, net.if.speed.\r\n\r\nYou can discuss this template or leave feedback on our forum https://www.zabbix.com/forum/zabbix-suggestions-and-feedback/387225-discussion-thread-for-official-zabbix-template-for-linux\r\n\r\nTemplate tooling version used: 0.38",
    "tls_connect": "1",
    "tls_accept": "1",
    "tls_issuer": "",
    "tls_subject": "",
    "tls_psk_identity": "",
    "tls_psk": "",
    "proxy_address": "",
    "auto_compress": "1",
    "discover": "0",
    "custom_interfaces": "0",
    "uuid": "f8f7908280354f2abeed07dc788c3747"
  }
]
</pre>


## 7. Подключение шаблона к хосту
<code>
curl http://127.0.0.1/api_jsonrpc.php -s -X POST -H 'Content-Type: application/json-rpc' \
-d '{
    "jsonrpc": "2.0",
    "method": "host.update",
    "params": {
        "hostid": "10413",
        "templates": [
             {
                "templateid": "10001"
             }
         ]
    },
    "auth": "358a6686ecd633171e32dfc01f8305bb",
    "id": 1
}' | jq -r .result
</code>
<pre>
{
  "hostids": [
    "10413"
  ]
}
</pre>