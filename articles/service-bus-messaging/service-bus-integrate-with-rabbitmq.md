---
title: 如何整合 RabbitMQ 與 Azure 服務匯流排
description: 如何整合 RabbitMQ 與 Azure 服務匯流排的逐步指南
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: cf21030fbf1aaa9f36e4d34aac918c4604066ec2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071629"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>如何整合 RabbitMQ 與 Azure 服務匯流排

在本指南中，我們將瞭解如何將訊息從 RabbitMQ 傳送至 Azure 服務匯流排。

以下是一些可利用這些功能的案例：

- **Edge**設定：我們有 edge 設定，我們會將訊息傳送至 RabbitMQ，但我們想要將這些訊息轉送至[Azure 服務匯流排](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview)以進行進一步的處理，因此我們可以使用許多[Azure Big Data 功能](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/big-data)。
- **混合式雲端**：您的公司剛取得使用 RabbitMQ 的協力廠商，以滿足其訊息需求。 它們位於不同的雲端。 當他們轉換至 Azure 時，您可以藉由將 RabbitMQ 與 Azure 服務匯流排橋接來開始共用資料。
- **協力廠商整合**：協力廠商使用 RabbitMQ 作為代理人，而且想要將資料傳送給我們，但它們不在我們的組織外部。 我們可以提供 SAS 金鑰給他們，讓他們能夠存取一組有限的 Azure 服務匯流排佇列，以便將訊息轉送到其中。

此清單已開啟，但我們可以藉由將 RabbitMQ 橋接至 Azure 來解決大部分的使用案例。

首先，您必須在[這裡](https://azure.microsoft.com/free/)註冊來建立免費的 Azure 帳戶

一旦您登入您的帳戶，請移至[Azure 入口網站](https://portal.azure.com/)，然後建立新的 Azure 服務匯流排[命名空間](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)。 命名空間是我們的訊息元件會存留的範圍容器，例如佇列和主題。

## <a name="adding-a-new-azure-service-bus-namespace"></a>加入新的 Azure 服務匯流排命名空間

在 Azure 入口網站中，按一下大的加號按鈕以加入新的資源

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="建立資源":::

然後選取 [整合]，然後按一下 [Azure 服務匯流排] 以建立訊息命名空間：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="選取 Azure 服務匯流排":::

系統會提示您輸入命名空間資訊。 選取您要使用的 Azure 訂用帳戶。 如果您沒有[資源群組](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)，您可以建立一個新的。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="建立命名空間":::

用於 `rabbitmq` `Namespace name` ，但可能是您想要的任何專案。 然後設定 `East US` 位置的。 選擇 `Basic` 作為 [定價層]。

如果一切順利，您應該會看到下列確認畫面：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="建立命名空間確認":::

然後回到 Azure 入口網站您會看到新的 `rabbitmq` 命名空間列在該處。 按一下以存取資源，讓您可以在其中新增佇列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="具有新命名空間的資源清單":::

## <a name="creating-our-azure-service-bus-queue"></a>建立我們的 Azure 服務匯流排佇列

現在您已有 Azure 服務匯流排命名空間，請按一下左側的按鈕，然後再 `Queues` `Entities` 加入新的佇列：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="建立佇列":::

佇列的名稱將會是訊息來源的 `from-rabbitmq` 提醒。 您可以將所有其他選項保留為預設值，但您可以變更它們以符合應用程式的需求。

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>啟用 RabbitMQ 鏟外掛程式

若要將訊息從 RabbitMQ 傳送至 Azure 服務匯流排，我們將使用隨附于 RabbitMQ 的[鏟外掛程式](https://www.rabbitmq.com/shovel.html)。 您可以使用下列命令來啟用外掛程式和其視覺化介面：

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>您可能需要以 root 身分執行該命令。

現在可以取得將 RabbitMQ 連接到 Azure 所需的認證。

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>將 RabbitMQ 連接到 Azure 服務匯流排

您必須為您的佇列建立[共用存取原則](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)（SAS），讓 RabbitMQ 可以發佈訊息給它。 SAS 原則可讓您指定允許哪些外部合作物件處理您的資源。 其概念是 RabbitMQ 可以傳送訊息，但無法接聽或管理佇列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="新增 SAS 原則":::

勾選 `Send` 核取方塊，然後按一下 `Create` 以備妥 SAS 原則。

建立原則之後，請按一下它以查看**主要連接字串**。 我們將使用它來讓 RabbitMQ 與 Azure 服務匯流排交談：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="取得 SAS 原則":::

在您可以使用該連接字串之前，您必須將它轉換成 RabbitMQ 的 AMQP 連接格式。 因此，請移至[連接字串轉換器工具](https://red-mushroom-0f7446a0f.azurestaticapps.net/)，並在表單中貼上您的連接字串，然後按一下 [轉換]。 您將會取得 RabbitMQ 就緒的連接字串。 （該網站會在瀏覽器中執行本機所有專案，因此您的資料不會透過網路傳送）。 您可以在[GitHub](https://github.com/videlalvaro/connstring_to_amqp)上存取其原始程式碼。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="轉換連接字串":::

現在，在瀏覽器中開啟 RabbitMQ 管理外掛程式 `http://localhost:15672/#/dynamic-shovels` 並移至 `Admin -> Shovel Management` ，您可以在其中新增鏟，以將訊息從 RabbitMQ 佇列傳送到 Azure 服務匯流排佇列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="新增 RabbitMQ 鏟":::

這裡會呼叫您 `azure` 的鏟，並選擇 `AMQP 0.9.1` 作為來源通訊協定。 在螢幕擷取畫面中，我們有 `amqp://` ，這是將我們連接到本機 RabbitMQ 伺服器的預設 URI。 請務必將其調整為您目前的部署。

在佇列的端，您可以使用 `azure` 做為佇列的名稱。 如果該佇列不存在，RabbitMQ 會為您建立。 您也可以選擇已經存在的佇列名稱。 您可以將其他選項保留為預設值。

然後在 [ `destination` 專案] 旁，選擇 `AMQP 1.0` 作為 [通訊協定]。 在 `URI` 欄位中，輸入您從上一個步驟中得到的連接字串，是否已將您的 Azure 連接字串轉換為 RabbitMQ 格式。 它看起來應該像這樣：

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

在欄位中， `Address` 我們會輸入**Azure 服務匯流排佇列**的名稱，在此案例中稱為 `from-rabbitmq` 。 按一下 `Add Shovel` ，您的安裝程式應該已準備好開始接收訊息。

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>將訊息從 RabbitMQ 發行至 Azure 服務匯流排

在 RabbitMQ 管理介面中，我們可以移至 `Queues` ， `azure` 並選取佇列，然後搜尋 `Publish message` 面板。 隨即會出現一個表單，讓您將訊息直接發行到您的佇列。 在我們的範例中，我們只會新增 `fist message` 作為 `Payload` 並叫用 `Publish Message` ：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="發行第一個訊息":::

返回 Azure 並檢查您的佇列。 按一下 `Service Bus Explorer` 左側面板中的。 如果一切順利，您會看到您的佇列現在有一個訊息。 好耶，恭喜！

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Azure 服務匯流排佇列":::

但是，我們要確定該訊息是您從 RabbitMQ 傳送的訊息。 選取索引標籤 `Peek` ，然後按一下 [] `Peek` 按鈕來抓取佇列中的最後一則訊息。 按一下訊息以檢查其內容。 您應該會看到類似下圖的內容，其中列出您的 `first message` 。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="佇列查看":::

## <a name="lets-recap"></a>讓我們回顧一下

恭喜！ 您已經完成了許多工作！ 為了讓您的訊息從 RabbitMQ 到 Azure 服務匯流排，我們將回顧這些步驟：

1. 建立 Azure 服務匯流排命名空間
2. 將佇列新增至命名空間
3. 將 SAS 原則新增至您的佇列
4. 取得佇列連接字串
5. 啟用 RabbitMQ 鏟外掛程式 & 管理介面
6. 將 Azure 服務匯流排連接字串轉換為 RabbitMQ 的 AMQP 格式
7. 將新的鏟新增至 RabbitMQ & 將其連接到 Azure 服務匯流排
8. 發行訊息

藉由遵循先前的步驟，您的組織在 Azure 外部已整合區域。 鏟外掛程式可讓您將訊息從 RabbitMQ 傳送至 Azure 服務匯流排。 這有極大的優點，因為您現在可以允許受信任的協力廠商連接其應用程式與您的 Azure 部署。

最後，訊息是關於啟用連線，而使用這項技術時，我們剛剛開啟了新的連接。