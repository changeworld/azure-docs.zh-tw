---
title: 包含檔案
description: 包含檔案
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 2af9c1dfd13d3aeafa7cf1ac76537117ecc15aff
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "84317789"
---
## <a name="run-the-web-application"></a>執行 Web 應用程式

1. 若要簡化您的用戶端測試，請在瀏覽器上開啟我們的範例單頁 Web 應用程式 [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/)。 

    > [!NOTE]
    > HTML 檔案的來源位於 [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html)。 如果您想要自行裝載 HTML，請啟動本機 HTTP 伺服器，例如 */docs/demo/chat-v2* 目錄中的 [http-server](https://www.npmjs.com/package/http-server) (英文)。 請確定原點已新增到 *local.settings.json* 中的 `CORS` 設定，如範例所示。
    > 
    > ```javascript
    > "Host": {
    >  "LocalHttpPort": 7071,
    >  "CORS": "http://localhost:8080,https://azure-samples.github.io",
    >  "CORSCredentials": true
    > }
    >
    > ```

1. 當系統提示您輸入函數應用程式基底 URL 時，請輸入 `http://localhost:7071`。

1. 在收到系統提示時輸入使用者名稱。

1. Web 應用程式會呼叫函數應用程式中的 *GetSignalRInfo* 函式，以擷取連線至 Azure SignalR 服務的連線資訊。 連線完成時，螢幕會出現聊天訊息輸入方塊。

1. 輸入訊息，然後按 Enter。 應用程式會將該訊息傳送至 Azure 函數應用程式中的 *SendMessage* 函式，該函式會使用 SignalR 輸出繫結將該訊息廣播至所有已連線的用戶端。 如果一切皆運作正常，該訊息應該會出現在應用程式中。

    ![執行應用程式](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. 在不同的瀏覽器視窗中開啟另一個 Web 應用程式執行個體。 您將會看見所有已傳送的訊息都會出現在該應用程式的所有執行個體中。
