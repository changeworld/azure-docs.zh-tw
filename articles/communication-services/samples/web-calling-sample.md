---
title: Azure 通訊服務 - Web 通話範例
titleSuffix: An Azure Communication Services sample
description: 了解通訊服務 Web 通話範例
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8faa6533ba4dc7fad61c045278c5504c5f78d900
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888958"
---
# <a name="get-started-with-the-web-calling-sample"></a>開始使用 Web 通話範例

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

> [!IMPORTANT]
> [您可在 GitHub 上取得此範例](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)。

Azure 通訊服務 **Web 通話範例** 會示範如何使用通訊服務的通話用戶端程式庫，透過 JavaScript 建立群組通話體驗。

在此範例快速入門中，我們將先了解範例如何運作，然後在您的本機電腦上執行範例。 接著，我們會使用您自己的 Azure 通訊服務資源，將範例部署至 Azure。

## <a name="overview"></a>概觀

Web 通話範例是一個 Web 應用程式，可作為通訊服務 Web 通話用戶端程式庫所提供之各種功能的逐步解說。 

此範例是針對開發人員所建立，可讓您輕鬆開始使用通訊服務。 其使用者介面分為多個區段，各有一個 [顯示程式碼] 按鈕，可讓您將程式碼直接從瀏覽器複製到自己的通訊服務應用程式。

當 [Web 通話範例](https://github.com/Azure-Samples/communication-services-web-calling-tutorial)在的電腦上執行時，您會看到下列登陸頁面：

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Web 通話教學課程 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Web 通話教學課程 2" lightbox="./media/web-calling-tutorial-page-2.png":::


## <a name="user-provisioning-and-sdk-initialization"></a>使用者佈建與 SDK 初始化 

若要開始使用示範，請將 [通訊服務資源](../quickstarts/create-communication-resource.md) 中的連接字串輸入 `config.json`。 這會用來佈建 [使用者存取權杖](../concepts/authentication.md)，讓系統將您的通話 SDK 初始化。

在使用者身分識別輸入中，輸入您自己的個人識別碼。 如果此處未提供任何內容，則會產生隨機的使用者身分識別。 

按一下 [佈建使用者和初始化 SDK]，使用後端權杖佈建服務所佈建的權杖將您的 SDK 初始化。 這項後端服務位於 `/project/webpack.config.js` 中。

按一下 [顯示程式碼] 按鈕，查看您可以在自己的解決方案中使用哪些範例程式碼。

SDK 初始化之後，您應該會看到下列內容：

:::image type="content" source="./media/user-provisioning.png" alt-text="使用者佈建" lightbox="./media/user-provisioning.png":::

您現在已經準備好開始使用您的通訊服務資源來撥打電話！

## <a name="placing-and-receiving-calls"></a>撥打及接聽通話

通訊服務 Web 通話 SDK 允許 **1 對 1**、**1 對多** 和 **群組** 通話。

針對 1 對 1 或 1 對多的撥出電話，您可以使用逗號分隔值，指定多個通訊服務使用者識別來撥號。 您也可以使用逗號分隔值，指定要呼叫的傳統 (PSTN) 電話號碼。 

撥打 PSTN 電話號碼時，請指定替代的通話者識別碼。 按一下 [撥打電話] 按鈕，撥打電話：

:::image type="content" source="./media/place-a-call.png" alt-text="撥打電話" lightbox="./media/place-a-call.png":::

若要加入群組通話，請輸入可識別通話的 GUID，然後按一下 [加入群組] 按鈕：

:::image type="content" source="./media/join-a-group-call.png" alt-text="加入群組通話" lightbox="./media/join-a-group-call.png":::

按一下 [顯示程式碼] 按鈕，查看用來撥打電話、接聽通話和加入群組通話的範例程式碼。

進行中的通話看起來會像這樣：

:::image type="content" source="./media/group-call.png" alt-text="群組通話" lightbox="./media/group-call.png":::

此範例也會提供下列功能的程式碼片段：

  - 按一下影片圖示，開啟/關閉您的攝影機
  - 按一下麥克風圖示，開啟/關閉您的麥克風
  - 按一下播放圖示來保留/取消保留通話
  - 按一下螢幕圖示以開始/停止共用螢幕
  - 按一下人員圖示，通話在中新增參與者
  - 按一下參與者名單中的 [移除參與者]，從通話中移除特定的參與者


## <a name="next-steps"></a>後續步驟

>[!div class="nextstepaction"] 
>[從 GitHub 下載範例](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

如需詳細資訊，請參閱下列文章：

- 熟悉如何[使用通話用戶端程式庫](../quickstarts/voice-video-calling/calling-client-samples.md)
- 深入了解[通話的運作方式](../concepts/voice-video-calling/about-call-types.md)
- 檢閱 [API 參考文件](/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js)

## <a name="additional-reading"></a>延伸閱讀

- [Azure 通訊 GitHub](https://github.com/Azure/communication) - 在官方 GitHub 頁面上尋找更多範例和資訊
- [Redux](https://redux.js.org/) - 用戶端狀態管理
- [FluentUI](https://aka.ms/fluent-ui) - Microsoft 支援的 UI 程式庫
- [React](https://reactjs.org/) - 用來建置使用者介面的程式庫
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - 用來建置 Web 應用程式的架構