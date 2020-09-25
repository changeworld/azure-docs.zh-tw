---
title: 群組聊天 Hero 範例
titleSuffix: An Azure Communication Services sample overview
description: 概述使用 Azure 通訊服務的聊天 Hero 範例，讓開發人員可以深入了解範例的內部運作，並了解如何加以修改。
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 295c4bde64ad21a19d21fd48f2556114b26b202d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943667"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>開始使用群組聊天 Hero 範例

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> links to our Hero Sample repo need to be updated when the sample is publicly available.
---->

Azure 通訊服務的**群組聊天 Hero 範例**會示範如何使用通訊服務的聊天 Web 用戶端程式庫，來組建群組通話體驗。

在此範例快速入門中，我們將先了解範例如何運作，然後在您的本機電腦上執行範例。 接著，我們會使用您自己的 Azure 通訊服務資源，將範例部署至 Azure。

> [!IMPORTANT]
> [從 GitHub 下載範例](https://github.com/Azure/Communication/tree/master/samples)

## <a name="overview"></a>概觀

此範例同時包含用戶端應用程式和伺服器端應用程式。 **用戶端應用程式**是使用 Microsoft Fluent UI 架構的 React/Redux Web 應用程式。 此應用程式會將要求傳送至 ASP.NET Core 的**伺服器端應用程式**，以協助用戶端應用程式連線至 Azure。 

範例如下所示：

:::image type="content" source="./media/chat/landing-page.png" alt-text="顯示範例應用程式登陸頁面的螢幕擷取畫面。":::

當您按下 [開始聊天] 按鈕時，Web 應用程式就會從伺服器端應用程式提取使用者存取權杖。 然後，此權杖會用來將用戶端應用程式連線到 Azure 通訊服務。 一旦擷取權杖之後，系統會提示您指定在聊天中代表您的名稱和表情。 

:::image type="content" source="./media/chat/pre-chat.png" alt-text="顯示應用程式聊天前畫面的螢幕擷取畫面。":::

設定顯示名稱和表情之後，您就可以加入聊天的工作階段中。 現在您會看到核心聊天體驗所在的主要聊天畫布。

:::image type="content" source="./media/chat/main-app.png" alt-text="顯示範例應用程式主畫面的螢幕擷取畫面。":::

主要聊天畫面的元件：

- **主要聊天區域**：這是使用者可以傳送和接收訊息的核心聊天體驗。 若要傳送訊息，您可以使用輸入區域，然後按 Enter 鍵 (或使用 [傳送] 按鈕)。 接收的核心訊息會依具有正確名稱和表情的寄件者分類。 您會在聊天區域中看到兩種類型的通知：1) 當使用者輸入時輸入通知，2) 傳送及讀取訊息的通知。
- **標頭**：使用者會在這個位置看到聊天對話標題，以及用來切換參與者和設定側邊欄的控制項，還有一個用來結束聊天工作階段的離開按鈕。
- **側邊欄**：使用標頭中的控制項進行切換時，會在此處顯示參與者和設定資訊。 參與者側邊欄包含聊天中的參與者清單，以及邀請參與者加入聊天工作階段的連結。 設定側邊欄可讓您設定聊天對話標題。 

您可以在下面找到有關設定範例的必要條件及步驟的詳細資訊。

## <a name="prerequisites"></a>必要條件

- 建立具有有效訂閱的 Azure 帳戶。 如需詳細資訊，請參閱[免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js (8.11.2 及更新版本)](https://nodejs.org/en/download/)
- [Visual Studio (2017 及更新版本)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2) (請務必安裝與您 Visual Studio 執行個體 (32 與 64 位元) 對應的版本)
- 建立 Azure 通訊服務資源。 如需詳細資訊，請參閱[建立 Azure 通訊資源](../quickstarts/create-communication-resource.md)。 您必須在本快速入門中記錄您的資源**連接字串**。

## <a name="locally-deploying-the-service--client-app"></a>在本機部署服務與用戶端應用程式

單一對話聊天範例基本上是用戶端和伺服器應用程式的兩個「應用程式」。

開啟 chat.csproj 上的 Visual Studio，然後在 [偵錯] 模式中執行，這樣將會啟動聊天前端服務。 從瀏覽器造訪伺服器應用程式時，其會將流量重新導向至本機部署的聊天前端服務。

您可以在本機測試範例，方法是開啟多個瀏覽器工作階段，其中包含聊天的 URL 來模擬多使用者聊天。

### <a name="before-running-the-sample-for-the-first-time"></a>第一次執行範例之前

1. 開啟 [PowerShell]、[Windows 終端機]、[命令提示字元] 或 [對等] 的執行個體，然後瀏覽至您想要將範例複製到其中的目錄。
2. `git clone`
3. 前往 **Chat/ClientApp** 資料夾並執行 `npm run setup`
   1. 如果您看到錯誤 1，請在輸出中查看您需要前往授權用戶端的 URL。 (URL 看起來會像這樣：`app.vssps.visualstudio.com/oauth2/authorize?clientid=...`) 當您造訪瀏覽器中的 URL 之後，請從瀏覽器視窗中複製命令並加以執行。
   2. 完成上一個步驟之後，請再次執行命令 `npm run setup`。
4. 從 Azure 入口網站取得 `Connection String`。 如需連接字串的詳細資訊，請參閱[建立 Azure 通訊資源](../quickstarts/create-communication-resource.md)
5. 取得 `Connection String` 之後，請將連接字串新增至聊天資料夾下找到的 **Chat/appsettings** 檔案。 在變數中輸入您的連接字串：`ResourceConnectionString`。

### <a name="local-run"></a>本機執行

1. 前往聊天資料夾
2. 在 Visual Studio 中開啟 `Chat.csproj` 解決方案
3. 執行 `Chat` 專案。*

\* 瀏覽器會在 localhost:5000 (節點部署用戶端應用程式之處) 開啟。 Internet Explorer 不支援應用程式。

#### <a name="troubleshooting"></a>疑難排解

- 解決方案不會組建，其會在 NPM 安裝/組建期間擲回錯誤

清除/重建 C# 解決方案

## <a name="publish-the-sample-to-azure"></a>將範例發行至 Azure

1. 以滑鼠右鍵按一下 `Chat` 專案，然後選取 [發佈]。
2. 建立新的發行設定檔，並選取您的 Azure 訂用帳戶。
3. 發行之前，請使用 `Edit App Service Settings` 新增您的連接字串，然後以金鑰形式填入 `ResourceConnectionString`，並提供您的連接字串 (從 appsettings.json 複製) 做為值。

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除通訊服務訂閱，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。 深入了解如何[清除資源](../quickstarts/create-communication-resource.md#clean-up-resources)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- 深入了解[聊天概念](../concepts/chat/concepts.md)
- 熟悉我們的[聊天用戶端程式庫](../concepts/chat/sdk-features.md)

## <a name="additional-reading"></a>延伸閱讀

- [Azure 通訊預覽](https://github.com/Azure/communication-preview) - 若要深入了解聊天 web SDK
- [Redux](https://redux.js.org/) - 用戶端狀態管理
- [FluentUI](https://developer.microsoft.com/fluentui#/) - Microsoft 支援的 UI 程式庫
- [React](https://reactjs.org/) - 組建使用者介面的程式庫
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) - 用來建置 Web 應用程式的架構
