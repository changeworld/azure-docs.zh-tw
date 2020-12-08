---
title: 包含檔案
description: 包含檔案
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d12df7197945a514ed8d3d0dca77271fb4bd0903
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509388"
---
## <a name="prerequisites"></a>必要條件
如果您沒有 [Azure 訂用帳戶](../articles/guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="create-a-service-bus-namespace"></a>建立服務匯流排命名空間
請遵循下列教學課程中的指示：[快速入門：使用 Azure 入口網站建立服務匯流排主題和主題的訂用帳戶](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)，以執行下列工作：

- 建立 **進階** 服務匯流排命名空間。 
- 取得連接字串。 
- 建立服務匯流排主題。
- 針對主題建立訂用帳戶。 在本教學課程中，您只需要一個訂用帳戶，因此不需要建立訂用帳戶 S2 和 S3。 

## <a name="send-messages-to-the-service-bus-topic"></a>對服務匯流排主題傳送訊息
在此步驟中，您會使用範例應用程式，將訊息傳送至您在上一個步驟中建立的服務匯流排主題。 

1. 複製 [GitHub azure-service-bus 存放庫](https://github.com/Azure/azure-service-bus/)。
2. 在 Visual Studio 中，移至 \samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration 資料夾，然後開啟 SBEventGridIntegration.sln 檔案。
3. 在方案總管視窗中，展開 **MessageSender** 專案，然後選取 [Program.cs]。
4. 將 `<SERVICE BUS NAMESPACE - CONNECTION STRING>` 取代為服務匯流排命名空間的連接字串；同時將 `<TOPIC NAME>` 取代為主題名稱。 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. 建置並執行程式，以將 5 個測試訊息 (`const int numberOfMessages = 5;`) 傳送至服務匯流排主題。 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="主控台應用程式輸出":::
