---
title: 包含檔案
description: 包含檔案
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "67174350"
---
## <a name="run-the-web-application"></a>執行 Web 應用程式

1. GitHub 中有裝載範例的單一頁面 Web 應用程式，以方便您使用。 在瀏覽器中開啟 [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/)。

    > [!NOTE]
    > HTML 檔案的來源位於 [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html)。

1. 當系統提示您輸入函數應用程式基底 URL 時，請輸入 `http://localhost:7071`。

1. 在收到系統提示時輸入使用者名稱。

1. Web 應用程式會呼叫函數應用程式中的 *GetSignalRInfo* 函式，以擷取連線至 Azure SignalR 服務的連線資訊。 連線完成時，螢幕會出現聊天訊息輸入方塊。

1. 輸入訊息，然後按 Enter。 應用程式會將該訊息傳送至 Azure 函數應用程式中的 *SendMessage* 函式，該函式會使用 SignalR 輸出繫結將該訊息廣播至所有已連線的用戶端。 如果一切皆運作正常，該訊息應該會出現在應用程式中。

    ![執行應用程式](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. 在不同的瀏覽器視窗中開啟另一個 Web 應用程式執行個體。 您將會看見所有已傳送的訊息都會出現在該應用程式的所有執行個體中。

> [!IMPORTANT]
> HTML 頁面會使用 HTTPS 處理，但本機 Azure Functions 執行階段預設使用 HTTP，因此您的瀏覽器可能會強制使用混合內容原則，而封鎖從網頁向您的函式發出的要求。 為了解決這個問題，請使用沒有這項限制的瀏覽器，或在 */docs/demo/chat-v2* 目錄中啟動本機 HTTP 伺服器，例如 [http-server](https://www.npmjs.com/package/http-server)。 確定已將原點新增至 *local.settings.json* 中的 `CORS` 設定。