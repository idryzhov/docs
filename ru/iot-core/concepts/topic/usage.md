# Использование топиков

Вы можете подписывать устройства и реестры на топики `$<devices или registries>/<ID устройства или реестра>/events` и `$<devices или registries>/<ID устройства или реестра>/commands`.

Если у вас есть устройства, на показания датчиков которых нужно оперативно реагировать, а в вашей сети возможны перебои со связью и разрыв соединения между устройствами и [MQTT-сервером](../../../glossary/mqtt-server.md), подписывайте устройства и реестры на перманентные топики `$<devices или registries>/<ID устройства или реестра>/state` и `$<devices или registries>/<ID устройства или реестра>/config`. В перманентном топике сохраняется последнее сообщение, отправленное в этот топик, и отображается при возобновлении соединения (даже если в момент подключения в топик не пишут устройства и реестры). После возобновления соединения перманентные топики работают как обычные топики, информация в них появляется когда устройство или реестр в них пишет.

В таблице описаны действия, которые устройства и реестры совершают с топиками:

Топики | Устройство | Реестр 
----|----|----
`$devices/<ID устройства>/events` <br/><br/>`$devices/<ID устройства>/state` | Отправляет телеметрические данные. | Получает телеметрические данные. <br/>Устройство известно.
`$devices/<ID устройства>/commands` <br/><br/>`$devices/<ID устройства>/config` | Получает команды. | Отправляет команды определенному устройству.
`$registries/<ID реестра>/events` <br/><br/>`$registries/<ID реестра>/state` | Отправляет телеметрические данные. | Получает телеметрические данные от всех устройств в реестре.<br/>Устройство неизвестно.
`$registries/<ID реестра>/commands` <br/><br/>`$registries/<ID реестра>/config` | Получает команды. | Отправляет команды всем устройствам в реестре. 
`$monitoring/<ID устройства>/json` | Получает данные мониторинга другого устройства в формате JSON. | Получает данные мониторинга устройства в формате JSON.

## Использование алиасов для топиков {#aliases}

_Алиас_ — это псевдоним [топика устройства](./devices-topic.md), присвоенный пользователем. Алиасы можно присваивать стандартным топикам, которые уже есть в сервисе, а также топикам с произвольными сабтопиками. 

{% include [monitoring-topic](../../../_includes/iot-core/monitoring-topic.md) %}

Алиас задается в формате ключ-значение:

```
<алиас>='<топик устройства>'
```

Алиасы можно использовать для отправки сообщений и подписки на сообщения наравне с обычными топиками устройств. Также вы можете использовать в алиасах символы подстановки при подписке на сообщения, при этом проверяется формат исходного топика, которому был присвоен алиас. 

Алиас должен однозначно определять устройство, то есть топик, которому присваивается алиас, должен содержать уникальный идентификатор устройства.

> Если [создать алиас](../../operations/device/alias/alias-create.md) `my/alias/=$devices/<ID устройства>/`, то можно использовать топик `my/alias/events`. Он будет эквивалентен `$devices/<ID устройства>/events`. По аналогии можно использовать и другие топики, начинающиеся с `$devices/<ID устройства>/`.

В рамках одного реестра алиасы не могут совпадать с префиксами других алиасов.

> Если вы создали алиас `my/alias/=...`, в этом реестре вы не сможете создать алиасы `my/=...`, `my/alias/2/=...`, `my/ali=...`.

> Вы можете создать алиасы `my/alias1/=...`, `my/alias2/=...` или `my/ali/=...`.

## Использование системных алиасов $me в $me-топиках {#mealias}

Чтобы каждый раз не вводить идентификатор устройства, от имени которого открыта MQTT-сессия, вы можете использовать $me-топики на основе алиасов `$me`, уже созданных в сервисе.

$me-топик | Эквивалентный топик 
----|----
`$me/device/events` | `$devices/<ID устройства>/events`
`$me/device/commands` | `$devices/<ID устройства>/commands`
`$me/device/state` | `$devices/<ID устройства>/state`
`$me/device/config` | `$devices/<ID устройства>/config`
`$me/registry/commands` | `$registries/<ID устройства>/commands`
`$me/registry/config` | `$registries/<ID устройства>/config`
`$me/monitoring/json`| `$monitoring/<ID устройства>/json`

При отправке сообщений и подписке на сообщения, $me-топики преобразуются в топики с <ID устройства> на уровне MQTT.
Если вы подписались на $me-топик, данные вы тоже получите в $me-топике.

## Подписка на топики с использованием символов подстановки {#wildcards}

Вы можете использовать специальные символы подстановки `#` и `+`, которые позволяют фильтровать подписки на топики. 

Если в начале фильтра стоит `$devices/`, то в фильтр попадают топики устройств, а если `$registries/`, то топики реестров. В остальных случаях в фильтр попадают только алиасы.

{% note info %}

Подписка на алиасы перманентных топиков с использованием символов подстановки работает как подписка на обычные топики. При возобновлении подключения к MQTT-серверу текущее состояние топика не отправляется подписанным на него реестрам или устройствам.

Если при подписке на перманентные топики с использованием символов подстановки в фильтр попадают более тысячи топиков, доставка данных по всем топикам не гарантируется.

{% endnote %}

### Символ # {#sharp}

Означает подстановку одной или нескольких частей топика, а также пустой строки. Всегда является последним символом в фильтре.

Примеры использования символа **#**:

* `#` — подписаться на все алиасы топиков.
* `$devices/#` — подписаться на все топики всех устройств.
* `$devices/<ID устройства>/#` — подписаться на все топики определенного устройства.
* `$devices/<ID устройства>/events/#` — подписаться на все топики определенного устройства с телеметрическими данными.
* `$devices/<ID устройства>/state/#` — подписаться на все перманентные топики с телеметрическими данными устройства с указанным уникальным идентификатором.

### Символ + {#plus}

Означает подстановку одной части топика между символами `/` либо в конце после символа `/`.

Например, по подписке `$registries/<ID реестра>/commands/+` устройства получат команды, отправленные в топики `$registries/<ID реестра>/commands/bedroom` и `$registries/<ID реестра>/commands/kitchen`, но проигнорируют команду в топик `$registries/<ID реестра>/commands/bedroom/entrance`.

Примеры использования символа **+**:

* `$devices/<ID устройства>/+` — подписаться на все топики определенного устройства с телеметрическими данными и командами.
* `$devices/<ID устройства>/events/+` — подписаться на все топики определенного устройства с телеметрическими данными из всех помещений. Предполагается, что в примере символ `+` замещает помещение.
* `$devices/+/events/bedroom/temperature` — подписаться на все топики всех устройств с телеметрическими данными о температуре в спальне.
* `$devices/+/events/bedroom/+` — подписаться на все топики всех устройств с телеметрическими данными в спальне. Предполагается, что в примере символы `+` замещают уникальный идентификатор устройства и тип датчика.

Фильтры `$devices/+` и `$registries/+` не работают, т.к. топик должен состоять из трех частей: `$<devices или registries>/<ID>/<events или commands>`.

### Совместное использование символов + и # {#join}

После `+` можно использовать `#`, чтобы подставить остальную часть топика или сабтопика:

* `$devices/+/#` — подписаться на все топики всех устройств. Эквивалентен фильтру `$devices/#`.

* `$devices/+/events/#` — подписаться на все топики всех устройств с телеметрическими данными.

При фильтрации также учитываются общие правила подписки на топики, например:

* Подписка на фильтр `$devices/#` с [сертификатом реестра](../../operations/certificates/registry-certificates.md) эквивалентна подписке на `$devices/+/events/#`.

   При этом будут получены и все сообщения, отправленные в `$devices/<DeviceID>/events`.

* Подписка на фильтр `$registries/#` с [сертификатом устройства](../../operations/certificates/device-certificates.md) эквивалентна подписке на `$registries/<ID реестра>/commands/#`.

   При этом будут получены и все сообщения, отправленные в `$registries/<ID реестра>/commands`.

## Триггеры для топиков {#trigger}

_Триггер_ — условие, при наступлении которого автоматически запускается определенная функция или контейнер.

Триггер для {{ iot-short-name }} предназначен для управления сообщениями, которыми обмениваются устройства и реестры. Он создается для топиков: принимает из них копии сообщений и передает в [функцию](../../../functions/concepts/function.md) {{ sf-name }} или [контейнер](../../../serverless-containers/concepts/container.md) {{ serverless-containers-name }} для обработки.  
 
{% include [trigger](../../../_includes/iot-core/trigger.md) %}
 
Триггеру для {{ iot-short-name }} необходим [сервисный аккаунт](../../../iam/concepts/users/service-accounts.md) для вызова функции или контейнера. 

Подробнее о триггерах читайте в документации [{{ sf-name }}](../../../functions/concepts/trigger/index.md) и [{{ serverless-containers-name }}](../../../serverless-containers/concepts/trigger/index.md).
