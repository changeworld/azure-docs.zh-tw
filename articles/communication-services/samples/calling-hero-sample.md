---
title: 群組通話 Hero 範例
titleSuffix: An Azure Communication Services sample overview
description: 概述使用 Azure 通訊服務的通話 Hero 範例，讓開發人員可以深入了解範例的內部運作。
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c872c2a2799eee3ae9e39653b0046b70ca5d7e8b
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887530"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>開始使用群組通話 Hero 範例

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

> [!IMPORTANT]
> [您可在 GitHub 上取得此範例。](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Azure 通訊服務的 **群組通話 Hero 範例** 會示範如何使用通訊服務的通話 Web 用戶端程式庫，來建立群組通話體驗。

在此範例快速入門中，我們將先了解範例如何運作，然後在您的本機電腦上執行範例。 接著，我們會使用您自己的 Azure 通訊服務資源，將範例部署至 Azure。

## <a name="overview"></a>概觀

此範例同時包含用戶端應用程式和伺服器端應用程式。 **用戶端應用程式** 是使用 Microsoft Fluent UI 架構的 React/Redux Web 應用程式。 此應用程式會將要求傳送至 ASP.NET Core 的 **伺服器端應用程式**，以協助用戶端應用程式連線至 Azure。 

範例如下所示：

:::image type="content" source="./media/calling/landing-page.png" alt-text="顯示範例應用程式登陸頁面的螢幕擷取畫面。":::

當您按下 [開始通話] 按鈕時，Web 應用程式就會從伺服器端應用程式提取使用者存取權杖。 然後，此權杖會用來將用戶端應用程式連線到 Azure 通訊服務。 一旦取得權杖之後，系統就會提示您指定想要使用的相機和麥克風。 您將能夠使用切換控制項來停用/啟用您的裝置：

:::image type="content" source="./media/calling/pre-call.png" alt-text="顯示範例應用程式通話前準備畫面的螢幕擷取畫面。":::

設定顯示名稱和裝置之後，您就可以加入通話的會話中。 現在您會看到核心通話體驗所在的主要通話畫布。

:::image type="content" source="./media/calling/main-app.png" alt-text="顯示範例應用程式主畫面的螢幕擷取畫面。":::

主要通話畫面的元件：

1. **媒體庫**：顯示參與者的主要階段。 如果參與者已啟用其相機，此處會顯示其影片摘要。 每個參與者都有個別圖格，其中顯示他們的顯示名稱和影片串流 (如果有的話)
2. **標頭**：這是主要通話控制項所在的位置，可切換設定和參與者側邊欄、開啟/關閉影片和混合功能、共用畫面及離開通話。
3. **側邊欄**：使用標頭上的控制項進行切換時，此處會顯示參與者和設定資訊。 您可以使用右上角的 [X] 來關閉該元件。 參與者側邊欄會顯示參與者清單，以及邀請更多使用者加入聊天的連結。 [設定] 側邊欄可讓您設定麥克風和相機設定。

您可以在下面找到有關設定範例的必要條件及步驟的詳細資訊。

## <a name="prerequisites"></a>必要條件

- 建立具有有效訂閱的 Azure 帳戶。 如需詳細資訊，請參閱[免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 及更新版本)](https://nodejs.org/en/download/)
- [Visual Studio (2019 及更新版本)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (請務必安裝與您 Visual Studio 執行個體 (32 與 64 位元) 對應的版本)
- 建立 Azure 通訊服務資源。 如需詳細資訊，請參閱[建立 Azure 通訊資源](../quickstarts/create-communication-resource.md)。 您必須在本快速入門中記錄您的資源 **連接字串**。

## <a name="locally-deploy-the-service--client-applications"></a>在本機部署服務與用戶端應用程式

群組通訊範例基本上是兩個應用程式：ClientApp 和 Service.NET 應用程式。

當我們想要在本機部署時，必須同時啟動這兩個應用程式。 從瀏覽器造訪伺服器應用程式時，將會使用本機部署的 ClientApp 來提供使用者體驗。

若要在本機測試範例，您可以透過通話 URL 開啟多個瀏覽器工作階段來模擬多使用者通話。

## <a name="before-running-the-sample-for-the-first-time"></a>第一次執行範例之前

1. 開啟 [PowerShell]、[Windows 終端機]、[命令提示字元] 或 [對等] 的執行個體，然後瀏覽至您想要將範例複製到其中的目錄。
2. `git clone https://github.com/Azure-Samples/communication-services-web-calling-hero.git`
3. 從 Azure 入口網站取得 `Connection String`。 如需連接字串的詳細資訊，請參閱[建立 Azure 通訊資源](../quickstarts/create-communication-resource.md)
4. 取得 `Connection String` 之後，請將連接字串新增至在服務 .NET 資料夾底下的 **Calling/appsetting.json** 檔案。 在變數中輸入您的連接字串：`ResourceConnectionString`。

### <a name="local-run"></a>本機執行

1. 移至 Calling 資料夾，然後在 Visual Studio 中開啟 `Calling.csproj` 解決方案
2. 執行 `Calling` 專案。 瀏覽器將會在 localhost:5001 上開啟

#### <a name="troubleshooting"></a>疑難排解

- 解決方案不會建置，其會在 NPM 安裝/建置建期間擲回錯誤。

   嘗試清除/重建專案。

## <a name="publish-the-sample-to-azure"></a>將範例發行至 Azure

1. 以滑鼠右鍵按一下 `Calling` 專案，然後選取 [發佈]。
2. 建立新的發行設定檔，並選取您的 Azure 訂用帳戶。
3. 發行之前，請使用 `Edit App Service Settings` 新增您的連接字串，然後以金鑰形式填入 `ResourceConnectionString`，並提供您的連接字串 (從 appsettings.json 複製) 做為值。

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除通訊服務訂閱，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。 深入了解如何[清除資源](../quickstarts/create-communication-resource.md#clean-up-resources)。

## <a name="next-steps"></a>後續步驟

>[!div class="nextstepaction"] 
>[從 GitHub 下載範例](https://github.com/Azure-Samples/communication-services-web-calling-hero)

如需詳細資訊，請參閱下列文章：

- 熟悉如何[使用通話用戶端程式庫](../quickstarts/voice-video-calling/calling-client-samples.md)
- 深入了解[通話的運作方式](../concepts/voice-video-calling/about-call-types.md)

## <a name="additional-reading"></a>延伸閱讀

- [Azure 通訊 GitHub](https://github.com/Azure/communication) - 在官方 GitHub 頁面上尋找更多範例和資訊
- [Redux](https://redux.js.org/) - 用戶端狀態管理
- [FluentUI](https://aka.ms/fluent-ui) - Microsoft 支援的 UI 程式庫
- [React](https://reactjs.org/) - 用來建置使用者介面的程式庫
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - 用來建置 Web 應用程式的架構