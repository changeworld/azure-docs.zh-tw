---
title: 使用 ASP.NET 進行開發 - Azure SignalR Service
description: 適用於使用 Azure SignalR 服務搭配 ASP.NET 架構建立聊天室的快速入門。
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 8c7d7f84e02cdd2cd4f53bc9eb42c78f936146ae
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408364"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>快速入門：使用 ASP.NET 和 SignalR 服務建立聊天室

Azure SignalR 服務依據的是 [SignalR for ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1)，其並**不**完全相容於 ASP.NET SignalR。 Azure SignalR 服務會採用最新的 ASP.NET Core 技術來重新實作 ASP.NET SignalR 資料通訊協定。 對 ASP.NET SignalR 使用 Azure SignalR 服務時，不會再支援某些 ASP.NET SignalR 功能。例如當用戶端重新連線時，Azure SignalR 不會重播訊息。 此外，不支援 Forever Frame 傳輸和 JSONP。 需要變更某些程式碼並使用合適的相依程式庫版本，ASP.NET SignalR 應用程式才能搭配 SignalR 服務運作。

關於將 ASP.NET SignalR 及 ASP.NET Core SignalR 的功能比較的完整清單，請參閱[版本差異文件](https://docs.microsoft.com/aspnet/core/signalr/version-differences?preserve-view=true&view=aspnetcore-3.1)。

在本快速入門中，您會了解如何開始使用類似[聊天室應用程式](./signalr-quickstart-dotnet-core.md)的 ASP.NET 和 Azure SignalR 服務。


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Prerequisites

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

[有任何問題嗎？請告訴我們。](https://aka.ms/asrs/qsnet)

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。

[有任何問題嗎？請告訴我們。](https://aka.ms/asrs/qsnet)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*無伺服器*模式不支援 ASP.NET SignalR 應用程式。 對於 Azure SignalR 服務執行個體，務必使用*預設*或*傳統*。

您也可以使用[建立 SignalR 服務指令碼](scripts/signalr-cli-create-service.md)，建立此快速入門中使用的 Azure 資源。

[有任何問題嗎？請告訴我們。](https://aka.ms/asrs/qsnet)

## <a name="clone-the-sample-application"></a>複製範例應用程式

在服務部署期間，讓我們來處理程式碼。 複製 [GitHub 上的範例應用程式](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom) \(英文\)，設定 SignalR 服務連接字串，然後在本機執行該應用程式。

1. 開啟 git 終端機視窗。 變更至您想要複製範例專案的資料夾。

1. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

[有任何問題嗎？請告訴我們。](https://aka.ms/asrs/qsnet)

## <a name="configure-and-run-chat-room-web-app"></a>設定並執行聊天室的 Web 應用程式

1. 啟動 Visual Studio 並開啟複製之存放庫的 *aspnet-samples/ChatRoom/* 資料夾中的方案。

1. 在開啟 Azure 入口網站的瀏覽器中，尋找並選取您建立的執行個體。

1. 選取 [金鑰]**** 以檢視 SignalR 服務執行個體的連接字串。

1. 選取並複製主要連接字串。

1. 現在設定 *web.config* 檔案中的連接字串。

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. 在 *Startup.cs* 中，不需要呼叫 `MapSignalR()`，您需要呼叫 `MapAzureSignalR({YourApplicationName})` 並傳遞連接字串`使應用程式連線到服務，而非自行裝載 SignalR。 將 `{YourApplicationName}` 取代為您的應用程式名稱。 這個名稱是此應用程式與其他應用程式相區別的唯一名稱。 您可以使用 `this.GetType().FullName` 做為值。

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    您也需要參考服務 SDK，才能使用這些 API。 開啟 [工具] | [NuGet 套件管理員] | [套件管理員主控台]****，並執行命令：

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    除了這些變更之外，其他一切均保持不變，您仍然可以使用您已經熟悉中樞介面撰寫商務邏輯。

    > [!NOTE]
    > 在實作中，對於由 Azure SignalR 服務 SDK 進行的交涉，會公開端點 `/signalr/negotiate`。 用戶端嘗試連接並將用戶端重新導向至連接字串中定義的服務端點時，會傳回特殊的交涉回應。

1. 按 <kbd>F5</kbd> 以在偵錯模式中執行專案。 您可以看到應用程式在本機執行。 此時會連線到 Azure SignalR 服務，而不會裝載應用程式本身的 SignalR 執行階段。

[有任何問題嗎？請告訴我們。](https://aka.ms/asrs/qsnet)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作，資源群組和其內的所有資源將會永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 如果您是在包含有需要保留之資源的現有資源群組內，建立用來裝載此範例的資源，則可以從每個資源各自的刀鋒視窗中個別刪除每個資源，而不必刪除正個資源群組。

登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [資源群組]。

在 [依名稱篩選...] 文字方塊中，輸入您的資源群組名稱。 本快速入門的指示是使用名為 *SignalRTestResources* 的資源群組。 在結果清單中的目標資源群組上方，按一下 **...** ，然後按一下 [刪除資源群組]。

![刪除](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

片刻過後，系統便會刪除該資源群組及其所有內含的資源。

[有任何問題嗎？請告訴我們。](https://aka.ms/asrs/qsnet)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立新的 Azure SignalR 服務資源，並搭配 ASP.NET Core Web 應用程式使用該資源。 接下來將了解如何搭配 ASP.NET Core 使用 Azure SignalR 服務開發即時應用程式。

> [!div class="nextstepaction"]
> [Azure SignalR Service 搭配 ASP.NET Core](./signalr-quickstart-dotnet-core.md)

[有任何問題嗎？請告訴我們。](https://aka.ms/asrs/qsnet)
