# Создание тарифа

1. Авторизуйтесь в [кабинете партнера {{ marketplace-short-name }}]({{ link-cloud-partners }}).
1. Перейдите в продукт, для которого вы хотите добавить новый [тариф](../concepts/tariff.md).
1. На вкладке **Тарифы** нажмите кнопку **Создать тариф**. Если вы не видите кнопку **Создать тариф**, значит, ваша заявка на продукт еще не одобрена. Подробности и сроки вы можете уточнить в разделе **Тикет заявки**.
1. В открывшемся окне введите название тарифа и выберите ценовую политику. Для разных типов продуктов доступны разные политики.

   {% include [types](../../_includes/marketplace/types-of-charge.md) %}

1. Укажите цену за выбранную единицу измерения.
1. Нажмите кнопку **Создать**.

## Создать сложный тариф {#complex-tariff}

1. Авторизуйтесь в [кабинете партнера {{ marketplace-short-name }}]({{ link-cloud-partners }}).
1. Перейдите в продукт, для которого вы хотите добавить сложный [тариф](../concepts/tariff.md).
1. На вкладке **Тарифы** нажмите кнопку **Создать тариф**. Если вы не видите кнопку **Создать тариф**, значит, ваша заявка на продукт еще не одобрена. Подробности и сроки вы можете уточнить в разделе **Тикет заявки**.
1. Введите название тарифа.
1. Выберите тип `PAYG` ⟶ **Другая схема тарификации**.
1. В поле **Описание тарифа** опишите создаваемый тариф:
    * Укажите метрику, на основании которой будет учтено потребление продукта пользователями. Подробнее см. [{#T}](../concepts/api-usage.md).
    * Укажите цену использования одной единицы измерения.
1. Нажмите кнопку **Создать**.

Тариф будет отправлен на проверку для утверждения. После утверждения SKU вы получите `skuId`, соответствующий созданному тарифу. `skuId` используется в API для работы с [записями о потреблении приложения](../api-ref/).
