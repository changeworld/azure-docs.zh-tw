---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72e4318bea7245e440db4c7d95bf7f1f38bbe268
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095176"
---
### <a name="send-a-test-notification"></a>傳送測試通知

1. 在 [Postman](https://www.postman.com/downloads/) 中開啟新的索引標籤。

1. 將要求設定為 **POST**，然後輸入下列位址：

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. 如果您選擇完成[使用 API 金鑰驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節，請務必將要求標頭設定為包含 **apikey** 值。

   | Key                            | 值                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

   > [!NOTE]
   > 此值可以從 **PushDemo** 專案中的 **Config.local_secrets.cs** 中找到。

1. 選擇 [主體] 的**原始**選項，然後從格式選項清單中選擇 [JSON]，然後包含一些 **JSON** 內容的預留位置：

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. 選取 [程式碼] 按鈕，其位在視窗右上方的 [儲存] 按鈕底下。 以 **HTML** 顯示時，要求看起來應該類似下列範例 (視您是否包含 **apikey** 標頭而定)：

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. 在一或兩個目標平台 (**Android** 和 **iOS**) 上執行 **PushDemo** 應用程式。

    > [!NOTE]
    > 如果您要在 **Android 上進行測試**，請確定您不是在**偵錯**中執行，或如果應用程式已藉由執行應用程式來部署，請強制關閉應用程式，然後從啟動器重新將其啟動。

1. 在 **PushDemo** 應用程式中，點選 [註冊] 按鈕。

1. 回到 **[Postman](https://www.postman.com/downloads)** ，關閉 [產生程式碼片段] 視窗 (如果尚未這麼做)，然後按一下 [傳送] 按鈕。

1. 確認您在 **[Postman](https://www.postman.com/downloads)** 中收到「**200 正常**」回應，且出現在應用程式中的警示顯示 [收到 ActionA 動作]。  

1. 關閉 **PushDemo** 應用程式，然後在 **[Postman](https://www.postman.com/downloads)** 中，再次按一下 [傳送] 按鈕。

1. 請確認您在 **[Postman](https://www.postman.com/downloads)** 中收到「**200 正常**」回應。 確認 **PushDemo** 應用程式的通知區域中出現包含正確訊息的通知。

1. 點選通知以確認其會開啟應用程式，並顯示 [收到 ActionA 動作] 警示。

1. 回到 **[Postman](https://www.postman.com/downloads)** ，修改先前的要求本文以傳送無訊息通知，並針對 [動作] 值指定 *action_b*，而不是 *action_a*。

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. 在應用程式仍開啟的情況下，按一下 **[Postman](https://www.postman.com/downloads)** 中的 [傳送] 按鈕。

1. 確認您在 **[Postman](https://www.postman.com/downloads)** 中收到「**200 正常**」回應，且出現在應用程式中的警示顯示 [收到 ActionB 動作]，而不是 [收到 ActionA 動作]。

1. 關閉 **PushDemo** 應用程式，然後在 **[Postman](https://www.postman.com/downloads)** 中，再次按一下 [傳送] 按鈕。

1. 確認您在 **[Postman](https://www.postman.com/downloads)** 中取得「**200 正常**」回應，且無訊息通知不會出現在通知區域中。
