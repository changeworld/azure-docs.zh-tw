---
title: 如何整合 RabbitMQ 與 Azure 服務匯流排
description: 如何整合 RabbitMQ 與 Azure 服務匯流排的逐步指南
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 6366824b8dc7f63f99ebda2a542d95d3eb1c6146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91301078"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>如何整合 RabbitMQ 與 Azure 服務匯流排

在本指南中，我們將瞭解如何將訊息從 RabbitMQ 傳送至 Azure 服務匯流排。

以下是一些可利用這些功能的案例：

- **Edge**設定：我們有 edge 設定，我們會將訊息傳送至 RabbitMQ，但我們想要將這些訊息轉送到 [Azure 服務匯流排](./service-bus-messaging-overview.md) 以進行進一步的處理，因此我們可以使用許多 [Azure Big Data 功能](/azure/architecture/guide/architecture-styles/big-data)。
- **混合式雲端**：您的公司已取得使用 RabbitMQ 的協力廠商，以滿足其訊息的需求。 它們位於不同的雲端。 當他們轉換至 Azure 時，您可以透過 Azure 服務匯流排橋接 RabbitMQ 來開始共用資料。
- **協力廠商整合**：協力廠商使用 RabbitMQ 作為訊息代理程式，而且想要將其資料傳送給我們，但不在我們的組織外部。 我們可以提供 SAS 金鑰給他們，讓他們能夠存取一組有限的 Azure 服務匯流排佇列，以便將訊息轉送至其中。

此清單已開啟，但我們可以藉由將 RabbitMQ 橋接至 Azure 來解決大部分的使用案例。

首先，您必須在[這裡](https://azure.microsoft.com/free/)註冊來建立免費的 Azure 帳戶

登入您的帳戶之後，請移至 [Azure 入口網站](https://portal.azure.com/) 並建立新的 Azure 服務匯流排 [命名空間](./service-bus-create-namespace-portal.md)。 命名空間是訊息元件的存留範圍容器，例如佇列和主題。

## <a name="adding-a-new-azure-service-bus-namespace"></a>加入新的 Azure 服務匯流排命名空間

在 Azure 入口網站中，按一下大型加號按鈕以新增資源

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="建立資源":::

然後選取 [整合]，然後按一下 [Azure 服務匯流排以建立訊息命名空間：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="建立資源":::

系統會提示您輸入命名空間資訊。 選取您要使用的 Azure 訂用帳戶。 如果您沒有資源群組，您可以建立一個新的 [資源群組](../azure-resource-manager/management/manage-resource-groups-portal.md)。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="建立資源":::

使用 `rabbitmq` for `Namespace name` ，但它可能是您想要的任何一個。 然後 `East US` 為位置設定。 選擇 `Basic` 定價層。

如果一切順利，您應該會看到下列確認畫面：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="建立資源":::

然後回到 Azure 入口網站您會看到新的 `rabbitmq` 命名空間列在那裡。 按一下該資源以存取資源，以便您可以在其中新增佇列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="建立資源":::

## <a name="creating-our-azure-service-bus-queue"></a>建立我們的 Azure 服務匯流排佇列

現在您已有 Azure 服務匯流排命名空間，請按一下左邊的按鈕，然後 `Queues` `Entities` 您可以加入新的佇列：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="建立資源":::

佇列的名稱只會提醒您 `from-rabbitmq` 訊息的來源。 您可以將所有其他選項保留為預設值，但您可以變更它們以符合您的應用程式需求。

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>啟用 RabbitMQ 鏟外掛程式

為了將訊息從 RabbitMQ 送出至 Azure 服務匯流排，我們將使用隨 RabbitMQ 封裝的 [鏟外掛程式](https://www.rabbitmq.com/shovel.html) 。 您可以使用下列命令來啟用外掛程式和其視覺化介面：

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>您可能需要以 root 的身份執行該命令。

現在可以取得將 RabbitMQ 連接到 Azure 所需的認證。

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>將 RabbitMQ 連接到 Azure 服務匯流排

您將需要為您的佇列建立 (SAS) 的 [共用存取原則](../storage/common/storage-sas-overview.md) ，讓 RabbitMQ 可以將訊息發佈到其中。 SAS 原則可讓您指定允許的外部合作物件使用您的資源。 其構想是 RabbitMQ 可以傳送訊息，但無法接聽或管理佇列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="建立資源":::

勾選方塊 `Send` ，然後按一下以備妥 `Create` SAS 原則。

建立原則之後，請按一下該原則，以查看 **主要連接字串**。 我們將使用它來讓 RabbitMQ 與 Azure 服務匯流排交談：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="建立資源":::

在您可以使用該連接字串之前，您必須先將其轉換為 RabbitMQ 的 AMQP 連接格式。 因此，請移至 [連接字串轉換器工具](https://red-mushroom-0f7446a0f.azurestaticapps.net/) ，並將您的連接字串貼到表單中，然後按一下 [轉換]。 您將會取得 RabbitMQ 就緒的連接字串。  (該網站會在您的瀏覽器中執行所有專案，如此您的資料就不會透過網路) 傳送。 您可以在 [GitHub](https://github.com/videlalvaro/connstring_to_amqp)上存取其原始程式碼。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="建立資源":::

現在，在瀏覽器中開啟 RabbitMQ 管理外掛程式 `http://localhost:15672/#/dynamic-shovels` ，然後移至 `Admin -> Shovel Management` ，您可以在其中新增新的鏟，以負責將訊息從 RabbitMQ 佇列傳送到 Azure 服務匯流排佇列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="建立資源":::

在此呼叫您 `azure` 的鏟，然後選擇 `AMQP 0.9.1` 作為來源通訊協定。 在螢幕擷取畫面中，我們有 `amqp://` ，也就是將我們連接到本機 RabbitMQ 伺服器的預設 URI。 請務必將其調整為您目前的部署。

在佇列端，您可以使用 `azure` 做為佇列的名稱。 如果該佇列不存在，RabbitMQ 就會為您建立該佇列。 您也可以選擇現有的佇列名稱。 您可以將其他選項保留為預設值。

然後，在 `destination` 專案的旁邊，選擇 `AMQP 1.0` 做為通訊協定。 在欄位中，輸入您在 `URI` 上一個步驟中取得的連接字串，您是否已將 Azure 連接字串轉換成 RabbitMQ 格式。 其看起來應該如下：

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

在欄位中， `Address` 我們會輸入您 **Azure 服務匯流排佇列**的名稱，在此案例中會呼叫它 `from-rabbitmq` 。 按一下 `Add Shovel` ，您的安裝程式應該已準備好開始接收訊息。

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>將訊息從 RabbitMQ 發佈到 Azure 服務匯流排

在 RabbitMQ 管理介面中，我們可以移至 `Queues` 並選取 `azure` 佇列，然後搜尋 `Publish message` 面板。 將會出現一個表單，可讓您將訊息直接發佈至您的佇列。 在我們的範例中，我們要新增 `fist message` 為 `Payload` ，然後點擊 `Publish Message` ：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="建立資源":::

返回至 Azure 並檢查您的佇列。 `Service Bus Explorer`在左面板中按一下，然後按一下 [_查看_] 按鈕。 如果一切順利，您將會看到您的佇列現在有一則訊息。 好耶，恭喜！

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="建立資源":::

但是，讓我們確定訊息是您從 RabbitMQ 傳送的訊息。 選取索引標籤 `Peek` ，然後按一下 `Peek` 按鈕以取出佇列中的最後一則訊息。 按一下訊息以檢查其內容。 您應該會看到如下圖所示的內容 `first message` 。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="建立資源":::

## <a name="lets-recap"></a>讓我們回顧

恭喜！ 您已達到許多！ 您可以從 RabbitMQ 將訊息傳送到 Azure 服務匯流排，讓我們來回顧這些步驟：

1. 建立 Azure 服務匯流排命名空間
2. 將佇列新增至命名空間
3. 將 SAS 原則新增至您的佇列
4. 取得佇列連接字串
5. 啟用 RabbitMQ 鏟外掛程式 & 管理介面
6. 將 Azure 服務匯流排連接字串轉換成 RabbitMQ 的 AMQP 格式
7. 將新的鏟新增至 RabbitMQ & 將它連接到 Azure 服務匯流排
8. 發佈訊息

遵循先前的步驟，您的組織在 Azure 外部的區域已整合。 鏟外掛程式可讓您將訊息從 RabbitMQ 送出至 Azure 服務匯流排。 這有許多優點，因為您現在可以允許受信任的協力廠商將其應用程式與您的 Azure 部署連線。

最後，訊息是關於啟用連線，而使用這項技術時，我們剛剛開啟了新的連接。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 服務匯流排](./service-bus-messaging-overview.md)
- 深入瞭解 [服務匯流排中的 AMQP 1.0 支援](./service-bus-amqp-overview.md)
