## BGP-связность {#bgp-peering}

BGP-связность настраивается внутри каждого приватного или публичного соединения между клиентским оборудованием и оборудованием {{ yandex-cloud }} в [точке присутствия](../../interconnect/concepts/pops.md) для обмена информацией о подсетях (префиксах) между сторонами. После обмена этой маршрутной информацией стороны могут передавать IPv4-трафик между подсетями, о которых они сообщили друг другу.

{% note warning %}

Существует [лимит](../../interconnect/concepts/limits.md#interconnect-limits) со стороны оборудования {{ yandex-cloud }} на количество получаемых префиксов от маршрутизатора клиента по протоколу BGP.
Если количество префиксов от маршрутизатора клиента превысит установленный лимит, то BGP-сессия будет разорвана на 30 минут.

Для поддержания непрерывной BGP-связности рекомендуется на маршрутизаторе клиента настраивать политики агрегации маршрутной информации для минимизации количества префиксов анонсируемых по протоколу BGP в направлении оборудования {{ yandex-cloud }} до разумных и необходимых размеров.

{% endnote %}

### BGP ASN {#bgp-asn}

Для настройки BGP-связности с каждой из сторон нужно указать номер автономной системы (BGP ASN). Значение BGP ASN для {{ yandex-cloud }} постоянно и всегда равно **200350**.
На клиентском оборудовании можно настроить публичный номер BGP ASN (если он есть) или использовать любое значение из диапазона приватных номеров BGP ASN: `64512 — 65534`.

{% note warning %}

Со стороны {{ yandex-cloud }} используется `4х байтное` значение BGP ASN - **200350**. Часто на сетевом оборудовании разных производителей приоритет отдаётся использованию 2х байтных значений BGP ASN, как более распространённому варианту. 

При настройке BGP-взаимодействия со стороны клиентского маршрутизатора необходимо явно разрешить в его конфигурации использование `4х байтных` значений BGP ASN.

{% endnote %}

### BGP аутентификация (опционально) {#bgp-auth}
Для повышения уровня защиты BGP соединения можно использовать BGP аутентификацию с использованием механизма `BGP MD5 password`. При включении данной функциональности рекомендуется использовать в качестве пароля строку длинной более 20 символов, состоящую из латинских букв, цифр и специальных символов.

### Протокол BFD {#bfd}

Если у клиента нет возможности подключить свой маршрутизатор к оборудованию {{ yandex-cloud }} напрямую, допускается использование промежуточных сетевых устройств - коммутаторов. Для быстрого обнаружения отказов в схемах с использованием промежуточных сетевых устройств рекомендуется использовать [протокол BFD](https://en.wikipedia.org/wiki/Bidirectional_Forwarding_Detection).

Протокол BFD всегда включен со стороны оборудования {{ yandex-cloud }} cо следующими значениями параметров: 
* `timer` — 300ms
* `multiplier` — 3

По запросу в техническую поддержку допускается изменение значения параметра `timer` - только в сторону увеличения. Изменение значения параметра `multiplier` не допускается.

