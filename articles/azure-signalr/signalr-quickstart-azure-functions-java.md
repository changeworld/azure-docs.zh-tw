---
title: 使用 Java 搭配 Azure Functions 與 SignalR Service 來建立聊天室
description: 說明如何使用 Azure SignalR 服務與 Azure Functions 來建立聊天室的快速入門。
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.custom: devx-track-java
ms.openlocfilehash: b67480f2b6de37822d74162f46caa4d7e50dc193
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321780"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>快速入門：使用 Java 搭配 Azure Functions 與 SignalR Service 來建立聊天室

Azure SignalR Service 可讓您輕鬆地在應用程式中新增即時功能，而 Azure Functions 是無伺服器平台，可讓您在不需管理任何基礎結構的情況下執行程式碼。 在此快速入門中，您會使用 Java 搭配 SignalR Service 和 Functions，建置無伺服器的即時聊天應用程式。

## <a name="prerequisites"></a>Prerequisites

- 程式碼編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)。
- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)。 用於在本機執行 Azure Function 應用程式。

   > [!NOTE]
   > 只有 Azure Function Core Tools 2.4.419 版 (主機版本 2.0.12332) 或更新版本支援 Java 中必要的 SignalR Service 繫結。

   > [!NOTE]
   > 為了安裝擴充功能，Azure Functions Core Tools 需要安裝 [.NET Core SDK](https://www.microsoft.com/net/download)。 不過，建置 JavaScript Azure Function 應用程式不需要 .NET 知識。

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) 第 8 版
- [Apache Maven](https://maven.apache.org) 3.0 版或更新版本

> [!NOTE]
> 此快速入門可以在 macOS、Windows 或 Linux 上執行。

## <a name="log-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 Azure 入口網站 (<https://portal.azure.com/>)。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>設定及執行 Azure Function 應用程式

1. 在開啟 Azure 入口網站的瀏覽器中，透過在入口網站頂端的搜尋方塊中搜尋您稍早部署的 SignalR 服務執行個體名稱，以確認該執行個體已成功建立。 選取該執行個體以開啟它。

    ![搜尋 SignalR 服務執行個體](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. 選取 [金鑰]  以檢視 SignalR 服務執行個體的連接字串。

1. 選取並複製主要連接字串。

    ![建立 SignalR 服務](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. 在您的程式碼編輯器中，開啟複製的存放庫中包含的 *src/chat/java* 資料夾。

1. 將 *local.settings.sample.json* 重新命名為 *local.settings.json*。

1. 在 **local.settings.json** 中，將連接字串貼到 **AzureSignalRConnectionString** 設定的值中。 儲存檔案。

1. 包含的函式的主要檔案位於 *src/chat/java/src/main/java/com/function/Functions.java*：

    - **negotiate** - 使用 *SignalRConnectionInfo* 輸入繫結來產生並傳回有效的連線資訊。
    - **sendMessage** - 在要求本文中接收聊天訊息，並使用 *SignalR* 輸出繫結將訊息廣播給所有已連線的用戶端應用程式。

1. 在終端機中，確定您處於 *src/chat/java* 資料夾中。 建置函式應用程式。

    ```bash
    mvn clean package
    ```

1. 在本機執行函式應用程式。

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已使用 Maven 建置並執行即時無伺服器應用程式。 接著，請了解如何從頭建立 Java Azure Functions。

> [!div class="nextstepaction"]
> [使用 Java 和 Maven 建立您的第一個函式](../azure-functions/functions-create-first-java-maven.md)