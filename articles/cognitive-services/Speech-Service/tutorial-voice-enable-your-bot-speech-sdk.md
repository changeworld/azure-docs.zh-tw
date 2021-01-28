---
title: 教學課程：使用語音 SDK 啟用 bot 的語音-語音服務
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將使用 Microsoft Bot Framework 建立 Echo Bot，並將其部署至 Azure，並向 Bot Framework Direct Line 語音通道註冊。 然後，您將設定適用于 Windows 的範例用戶端應用程式，讓您向 bot 對話並聆聽其回應。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ee7116efe931d101a1505bc2d9d866d8ea5b92a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943438"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>教學課程：使用語音 SDK 為您的 bot 啟用語音

您可以使用語音服務來啟用聊天機器人的語音功能。

在本教學課程中，您將建立會重複您所說的 bot。
您將使用 Microsoft Bot Framework 建立 bot、將它部署至 Azure，並向 Bot Framework Direct Line 語音通道註冊。
然後，您將設定適用于 Windows 的範例用戶端應用程式，讓您向 bot 說話，並聆聽其向您對話。

本教學課程是針對剛開始使用 Azure、Bot Framework bot、Direct Line 語音或語音 SDK 的開發人員所設計，而且想要快速建立具有有限程式碼的工作系統。 不需要經驗或熟悉這些服務。

您在本教學課程中所做的語音聊天機器人會遵循下列步驟：

1. 範例用戶端應用程式會設定為連接到 Direct Line 語音通道和 Echo Bot。
1. 當使用者按下按鈕時，來自麥克風的語音音訊串流。 使用自訂關鍵字時，會持續記錄 (或音訊。 ) 
1. 如果使用自訂關鍵字，則會在本機裝置上進行關鍵字偵測，以將音訊串流處理至雲端。
1. 使用語音 SDK 時，範例用戶端應用程式會連線到 Direct Line 語音通道並串流音訊。
1. （選擇性）在服務上進行較高的精確度關鍵字驗證。
1. 音訊會傳遞給語音辨識服務，並轉譯為文字。
1. 已辨識的文字會以 Bot Framework 活動的形式傳遞至 Echo Bot。
1. 回應文字會由文字轉語音 (TTS) 服務轉換成音訊，並串流回用戶端應用程式以進行播放。

<!-- svg src in User Story 1754106 -->
![圖表-標記](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "語音通道流程")

> [!NOTE]
> 本教學課程中的步驟不需要付費服務。 如果您是新的 Azure 使用者，您將能夠使用免費 Azure 試用版訂用帳戶的信用額度和語音服務的免費層，來完成本教學課程。

本教學課程涵蓋下列內容：
> [!div class="checklist"]
> * 建立新的 Azure 資源
> * 建立、測試 Echo Bot 範例，並將其部署到 Azure App Service
> * 使用 Direct Line 語音通道註冊您的 bot
> * 建立並執行 Windows 語音助理用戶端，以與您的 Echo Bot 互動
> * 新增自訂關鍵字啟用
> * 瞭解如何變更辨識和語音的語言

## <a name="prerequisites"></a>先決條件

以下是完成本教學課程所需的內容：

- 具有運作中麥克風和喇叭的 Windows 10 電腦 (或耳機) 
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 或更高版本， **並已安裝 ASP.NET 和 網頁程式開發** 工作負載
- [.NET Framework Runtime 4.6.1](https://dotnet.microsoft.com/download) 或更高版本
- 一個 Azure 帳戶。 [免費註冊](https://azure.microsoft.com/free/cognitive-services/)
- [GitHub](https://github.com/) 帳戶
- [Git for Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>建立資源群組

您將在本教學課程中建立的用戶端應用程式會使用少數的 Azure 服務。 若要減少 bot 回應的往返時間，您需要確定這些服務位於相同的 Azure 區域。 在本節中，您會在 **美國西部** 區域建立資源群組。 針對 Bot Framework、Direct Line 語音通道和語音服務建立個別資源時，將會使用此資源群組。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">建立資源群組 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. 系統會提示您提供一些資訊：
   * 將 **訂** 用帳戶設定為 **免費試用** (您也可以使用現有的訂用帳戶) 。
   * 輸入 **資源群組** 的名稱。 我們建議 **SpeechEchoBotTutorial-ResourceGroup**。
   * 從 [ **區域** ] 下拉式清單中，選取 [ **美國西部**]。
1. 按一下 [檢閱及建立]。 您應該會看到已通過讀取 **驗證** 的橫幅。
1. 按一下頁面底部的 [新增]  。 建立資源群組可能需要幾分鐘的時間。
1. 如同您稍後在本教學課程中建立的資源，建議您將此資源群組釘選到您的儀表板以方便存取。 如果您想要釘選此資源群組，請按一下資源組名右邊的釘選圖示。

### <a name="choosing-an-azure-region"></a>選擇 Azure 區域

如果您想要在本教學課程中使用不同的區域，這些因素可能會限制您的選擇：

* 確定您使用支援的 [Azure 區域](regions.md#voice-assistants)。
* Direct Line 語音通道使用具有標準和類神經語音的文字轉換語音服務。 類神經語音 [僅限特定的 Azure 區域](regions.md#standard-and-neural-voices)。

如需區域的詳細資訊，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。

## <a name="create-resources"></a>建立資源

現在您已在支援的區域中有資源群組，下一步是為您將在本教學課程中使用的每個服務建立個別資源。

### <a name="create-a-speech-service-resource"></a>建立語音服務資源

請遵循下列指示來建立語音資源：

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">建立語音服務資源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 系統會提示您提供一些資訊：
   * 為您的資源 **命名**。 我們建議 **SpeechEchoBotTutorial-語音**
   * 針對 **訂** 用帳戶，請確定已選取 [ **免費試用** ]。
   * 針對 [ **位置**]，選取 [ **美國西部**]。
   * 針對 [ **定價層**]，選取 [ **F0**]。 這是免費層。
   * 針對 [ **資源群組**]，選取 [ **SpeechEchoBotTutorial-ResourceGroup**]。
5. 輸入所有必要資訊之後，請按一下 [ **建立**]。 建立資源可能需要幾分鐘的時間。
6. 稍後在本教學課程中，您將需要此服務的訂用帳戶金鑰。 您可以隨時從資源的 **總覽** (管理金鑰) 或 **金鑰** 來存取這些金鑰。

此時，請確認您的資源群組 (**SpeechEchoBotTutorial** 資源群組) 具有語音資源：

| 名稱 | 類型  | Location |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | 認知服務 | 美國西部 |

### <a name="create-an-azure-app-service-plan"></a>建立 Azure App Service 方案

下一步是建立 App Service 計畫。 App Service 方案會針對要執行的 Web 應用程式定義一組計算資源。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">建立 Azure App Service 方案 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 系統會提示您提供一些資訊：
   * 將 **訂** 用帳戶設定為 **免費試用** (您也可以使用現有的訂用帳戶) 。
   * 針對 [ **資源群組**]，選取 [ **SpeechEchoBotTutorial-ResourceGroup**]。
   * 為您的資源 **命名**。 我們建議 **SpeechEchoBotTutorial-AppServicePlan**
   * 針對 [ **作業系統**]，請選取 [ **Windows**]。
   * 針對 [ **區域**]，請選取 [ **美國西部**]。
   * 針對 [ **定價層**]，請確定已選取 [ **標準 S1** ]。 這應該是預設值。 如果不是，請確定您已如上面所述將 **作業系統** 設定為 **Windows** 。
5. 按一下 [檢閱及建立]。 您應該會看到已通過讀取 **驗證** 的橫幅。
6. 按一下頁面底部的 [新增]  。 建立資源群組可能需要幾分鐘的時間。

此時，請確認您的資源群組 (**SpeechEchoBotTutorial** 資源群組) 有兩個資源：

| 名稱 | 類型  | Location |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service 方案 | 美國西部 |
| SpeechEchoBotTutorial-Speech | 認知服務 | 美國西部 |

## <a name="build-an-echo-bot"></a>建立 Echo Bot

現在您已建立一些資源，讓我們來建立 bot。 我們會從 Echo Bot 範例開始著手，顧名思義，它會回顯您輸入的文字作為其回應。 別擔心，範例程式碼可供您使用，而不需要進行任何變更。 它是設定為使用 Direct Line 語音通道，我們會在將 bot 部署至 Azure 之後進行連線。

> [!NOTE]
> [GitHub 上的範例讀我檔案](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)中提供了下列指示，以及 Echo Bot 的其他相關資訊。

### <a name="run-the-bot-sample-on-your-machine"></a>在您的電腦上執行 bot 範例

1. 複製範例存放庫：

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. 啟動 Visual Studio。
3. 從工具列 **中選取 [** 檔案  >  **開啟**  >  **專案/方案**]，然後開啟 Echo Bot 專案方案：

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. 載入專案之後，請按 <kbd>F5</kbd> 來建立並執行專案。
5. 瀏覽器隨即啟動，您會看到類似下面的畫面。
    > [!div class="mx-imgBorder"]
    > [![螢幕擷取畫面顯示 EchoBot 頁面，其中包含您的 bot 已就緒的訊息！](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "在 localhost 上執行的 EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>使用 Bot Framework Emulator 測試 bot 範例

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator)是一種傳統型應用程式，可讓 bot 開發人員在本機 (或從遠端透過通道) ，在本機測試和偵測其 bot。 模擬器接受輸入的文字做為輸入 (非語音) 。 Bot 也會以文字回應。 遵循這些步驟，使用 Bot Framework Emulator 測試您在本機執行的 Echo Bot，並提供文字輸入和文字輸出。 將 bot 部署至 Azure 之後，會使用語音輸入和聲音輸出來測試它。

1. 安裝 [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) 版本4.3.0 或更高版本
2. 啟動 Bot Framework Emulator，並開啟您的 Bot：
   * 檔案  -> **開啟 Bot**。
3. 輸入 bot 的 URL。 例如：

   ```
   http://localhost:3978/api/messages
   ```
   然後按 [連接]。
4. Bot 應該會歡迎您「Hello，歡迎！」 回應。 輸入任何文字訊息，並確認您收到來自 bot 的回應。
5. 這就是與 Echo Bot 實例的通訊交換的結果，如下所示：  [![螢幕擷取畫面會顯示 Bot Framework Emulator。](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework 模擬器")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>將您的 bot 部署到 Azure App Service

下一步是將 Echo Bot 部署到 Azure。 有幾種方式可以部署 bot，但是在本教學課程中，我們將著重于直接從 Visual Studio 發佈。

> [!NOTE]
> 或者，您也可以使用 [Azure CLI](/azure/bot-service/bot-builder-deploy-az-cli) 和 [部署範本](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot)來部署 bot。

> [!NOTE]
> 如果您在執行下列步驟時未顯示 [ **發佈 ...** ]，請使用 Visual Studio 安裝程式加入 **ASP.NET 和 網頁程式開發** 工作負載。

1. 從 Visual Studio 中，開啟已設定為與 Direct Line 語音通道搭配使用的 Echo Bot：

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. 在 [**方案總管** 中，以滑鼠右鍵按一下 **EchoBot** 專案，然後選取 [**發行**]。
1. 標題為 [ **發佈** ] 的新視窗隨即開啟。
1. 選取 [ **Azure**]，按 **[下一步]**，選取 **Azure App Service (Windows)**，按一下 **[下一步]**，然後按一下 [ **建立新的 Azure App Service** ...]，再按綠色加號。
1. 當 **App Service (Windows)** 視窗出現時：
   * 按一下 [ **新增帳戶**]，然後使用您的 Azure 帳號憑證登入。 如果已經登入，請從下拉式清單中選取要使用的帳戶。
   * 針對 [ **名稱**]，您必須輸入 Bot 的全域唯一名稱。 這個名稱是用來建立唯一的 bot URL。 將會填入預設值，包括日期和時間 (例如： "EchoBot20190805125647" ) 。 您可以在本教學課程中使用預設名稱。
   * 針對 **訂** 用帳戶，將其設定為 **免費試用**
   * 針對 **資源群組**，請選取 **SpeechEchoBotTutorial-ResourceGroup**
   * 若為 **主控方案**，請選取 **SpeechEchoBotTutorial-AppServicePlan**
1. 按一下頁面底部的 [新增]  。 在最後的 wizard 畫面上，按一下 **[完成**]。
1. 按一下 [發佈] 畫面右側的 [ **發行** ]。 Visual Studio 將 bot 部署到 Azure。
1. 您應該會在 Visual Studio 的輸出視窗中看到成功訊息，如下所示：

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. 您的預設瀏覽器應該會開啟並顯示一頁：「您的 bot 已準備就緒！」。
1. 此時，請檢查 Azure 入口網站中的資源群組 **SpeechEchoBotTutorial** 資源群組，並確認下列三個資源：

| 名稱 | 類型  | Location |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 美國西部 |
| SpeechEchoBotTutorial-AppServicePlan | App Service 方案 | 美國西部 |
| SpeechEchoBotTutorial-Speech | 認知服務 | 美國西部 |

## <a name="enable-web-sockets"></a>啟用 Web 通訊端

您將需要進行小規模的設定變更，讓您的 bot 可以使用 web 通訊端與 Direct Line 語音通道進行通訊。 遵循下列步驟來啟用 web 通訊端：

1. 流覽至 [Azure 入口網站](https://portal.azure.com)，然後按一下您的 App Service。 資源的命名應類似于 **EchoBot20190805125647** (您的唯一應用程式名稱) 。
2. 在左側流覽窗格的 [設定] 底下，**按一下 [****設定**]。
3. 選取 [ **一般設定** ] 索引標籤。
4. 找出 **Web 通訊端** 的切換，並將其設定為 [ **開啟**]。
5. 按一下 [儲存]。

> [!TIP]
> 您可以使用 Azure App Service 頁面頂端的控制項來停止或重新開機服務。 進行疑難排解時，這可能很有用。

## <a name="create-a-channel-registration"></a>建立通道註冊

現在您已建立用來裝載 bot 的 Azure App Service，下一步是建立 **Bot 通道註冊**。 建立通道註冊是使用 Bot Framework 通道（包括 Direct Line 語音通道）來註冊 bot 的先決條件。 如果您想要深入瞭解 bot 如何使用通道，請參閱 [將 bot 連線至通道](/azure/bot-service/bot-service-manage-channels)。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">建立 Azure Bot 通道註冊 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. 系統會提示您提供一些資訊：
   * 針對 **Bot 控制碼**，請輸入 **SpeechEchoBotTutorial-BotRegistration-# #** # #，並取代為 **####** 您所選擇的數。 請注意，Bot 控制碼必須是全域唯一的。 如果您輸入 Bot 控制碼，但卻收到錯誤訊息， _表示要求的 BOT 識別碼無法使用_，則挑選不同的號碼。 在下列範例中，我們使用8726
   * 針對 [ **訂** 用帳戶]，選取 [ **免費試用**]。
   * 針對 [ **資源群組**]，選取 [ **SpeechEchoBotTutorial-ResourceGroup**]。
   * 針對 [ **位置**]，選取 [ **美國西部**]。
     * 針對 [ **定價層**]，選取 [ **F0**]。
     * 針對 [ **訊息端點**]，輸入您的 web 應用程式 URL，並在 `/api/messages` 結尾附加路徑。 例如：如果您的全域唯一應用程式名稱是 **EchoBot20190805125647**，則您的訊息端點會是： `https://EchoBot20190805125647.azurewebsites.net/api/messages/` 。
     * 針對 **application insights**，您可以將此設為 **Off**。 如需詳細資訊，請參閱 [Bot 分析](/azure/bot-service/bot-service-manage-analytics)。
     * 略過 **自動建立應用程式識別碼和密碼**。
5. 按一下 [ **Bot 通道註冊** ] 分頁底部的 [ **建立**]。

此時，請檢查 Azure 入口網站中的資源群組 **SpeechEchoBotTutorial** 資源群組。 它現在應該會顯示至少四個資源：

| 名稱 | 類型  | Location |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 美國西部 |
| SpeechEchoBotTutorial-AppServicePlan | App Service 方案 | 美國西部 |
| SpeechEchoBotTutorial-BotRegistration-8726 | Bot 通道註冊 | 全球 |
| SpeechEchoBotTutorial-Speech | 認知服務 | 美國西部 |

> [!IMPORTANT]
> 即使您選取 [美國西部]，Bot 通道註冊資源仍會顯示全域區域。 這是預期行為。

## <a name="optional-test-in-web-chat"></a>選用：在網路聊天中測試

[Azure Bot 通道註冊] 頁面的 [ **Bot 管理**] 下有 **網路聊天** 選項的測試。 由於網路聊天需要針對您的 bot 進行驗證，因此預設不會與您的 bot 搭配運作。 如果您想要使用文字輸入來測試已部署的 bot，請遵循下列步驟。 請注意，這些步驟是選擇性的，並不是必要的步驟，才能繼續進行本教學課程的後續步驟。 

1. 在 [Azure 入口網站](https://portal.azure.com)中找出並開啟您的 **EchoBotTutorial-BotRegistration-# #** # # 資源
1. 從 **Bot 管理** 導覽中，選取 [ **設定**]。 複製 [ **Microsoft 應用程式識別碼**] 下的值
1. 開啟 Visual Studio EchoBot 方案。 在 [方案 explorer] 中，找出並按兩下 **appsettings.js**
1. 將 JSON 檔案中 **MicrosoftAppId** 旁的空字串取代為複製的識別碼值
1. 回到 Azure 入口網站，在 [ **Bot 管理**] 導覽中選取 [**設定**]，然後按一下 [ **Microsoft 應用程式識別碼**] **(管理)**
1. 按一下 [ **新增用戶端密碼**]。 新增描述 (例如「網路聊天」 ) 然後按一下 [ **新增**]。 複製新的秘密
1. 將 JSON 檔案中 **MicrosoftAppPassword** 旁的空字串取代為複製的秘密值
1. 儲存 JSON 檔案。 您應該會看到類似下面的畫面：
```json
{
  "MicrosoftAppId": "3be0abc2-ca07-475e-b6c3-90c4476c4370",
  "MicrosoftAppPassword": "-zRhJZ~1cnc7ZIlj4Qozs_eKN.8Cq~U38G"
}
```
9. 重新發佈應用程式 (在 Visual Studio 方案瀏覽器中的 **EchoBot** 專案上按一下滑鼠右鍵，選取 [ **發行** ]，然後按一下 [ **發佈** ] 按鈕) 
10. 現在您已準備好在網路聊天中測試 bot！

## <a name="register-the-direct-line-speech-channel"></a>註冊 Direct Line 語音通道

現在是時候向 Direct Line 語音通道註冊您的 bot。 此通道會建立您的 bot 與使用語音 SDK 編譯之用戶端應用程式之間的連線。

1. 在 [Azure 入口網站](https://portal.azure.com)中，找出並開啟您的 **SpeechEchoBotTutorial-BotRegistration-# #** # # 資源。
1. 從 **Bot 管理** 導覽中選取 [ **通道**]。
   * 在 [ **更多頻道**] 下，按一下 [ **Direct Line 語音**]。
   * 檢查標題為 [ **設定 Direct Line Speech**] 的頁面上的文字，然後展開 [ **認知服務帳戶** ] 下拉式功能表。
   * 從功能表選取您稍早建立的語音資源 (例如 **SpeechEchoBotTutorial-語音**) ，以將您的 bot 與您的語音訂用帳戶金鑰建立關聯。
   * 略過其餘的選擇性欄位。
   * 按一下 [儲存]。

1. 從 **Bot 管理** 流覽中，按一下 [ **設定**]。
   * 核取標示為 [ **啟用串流端點**] 的核取方塊。 若要建立以 web 通訊端為基礎的通訊協定，您的 bot 與 Direct Line 語音通道之間必須要有這種情況。
   * 按一下 [儲存]。

> [!TIP]
> 如果您想要深入瞭解，請參閱 [將 bot 連線到 Direct Line 語音](/azure/bot-service/bot-service-channel-connect-directlinespeech)。 此頁面包含其他資訊和已知問題。

## <a name="run-the-windows-voice-assistant-client"></a>執行 Windows 語音助理用戶端

在此步驟中，您將執行 Windows 語音助理用戶端。 用戶端是 c # 中 (WPF) 應用程式的 Windows Presentation Foundation，其使用 [語音 SDK](./speech-sdk.md) 來管理使用 Direct Line 語音通道與 bot 進行通訊的功能。 您可以使用它來與您的 bot 互動，並在撰寫自訂用戶端應用程式之前進行測試。 它是開放原始碼，因此您可以下載可執行檔並加以執行，或自行建立。

Windows 語音助理用戶端有一個簡單的 UI，可讓您設定 bot 的連線、觀看文字交談、觀看 Bot Framework JSON 格式的活動，以及顯示調適型卡片。 它也支援使用自訂關鍵字。 您將使用此用戶端來與您的 bot 交談，並接收語音回應。

> [!NOTE]
> 此時，請確認您的麥克風和喇叭已啟用且正常運作。

1. 流覽至 [Windows 語音助理用戶端](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)的 GitHub 存放庫。
1. 遵循該處提供的指示
   * 在 ZIP 套件中下載預先建立的可執行檔，以執行或
   * 複製存放庫並建立專案，自行建立可執行檔。

1. `VoiceAssistantClient.exe`遵循 GitHub 存放庫中的指示來啟動用戶端應用程式，並將其設定為連線至您的 bot。
1. 按一下 [ **重新連接** ]，並確定您看到 [ **新對話已啟動]-輸入或按下麥克風按鈕**。
1. 讓我們測試看看。按一下麥克風按鈕，並以英文說出幾個字。 當您說話時，會顯示已辨識的文字。 當您說話時，bot 會以自己的語音進行回復，並在後面加上辨識的單字，並說出「echo」。
1. 您也可以使用文字與 bot 進行通訊。 只需輸入底部列的文字。 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>針對 Windows 語音助理用戶端中的錯誤進行疑難排解

如果您在主要應用程式視窗中收到錯誤訊息，請使用此表格來識別錯誤並進行疑難排解：

| 錯誤 | 您應該做什麼？ |
|-------|----------------------|
|錯誤 (AuthenticationFailure) ： WebSocket 升級失敗，發生驗證錯誤 (401) 。 檢查是否有正確的訂用帳戶金鑰 (或授權權杖) 和區功能變數名稱稱| 在應用程式的 [設定] 頁面中，請確定您已正確輸入語音訂用帳戶金鑰和其區域。<br>請確定您的語音金鑰和金鑰區域已正確輸入。 |
|錯誤 (ConnectionFailure) ：遠端主機已關閉連接。 錯誤碼：1011。 錯誤詳細資料：我們無法在傳送訊息之前連接到 bot | 確定您 [已核取 [啟用串流端點]](#register-the-direct-line-speech-channel)方塊及/或已將 [ **Web 通訊端** 切換](#enable-web-sockets)為開啟。<br>確定您的 Azure App Service 正在執行。 如果是，請嘗試重新開機您的 App Service。|
|錯誤 (ConnectionFailure) ：遠端主機已關閉連接。 錯誤碼：1002。 錯誤詳細資料：當預期狀態碼 ' 101 ' 時，伺服器傳回的狀態碼為 ' 503 ' | 確定您 [已核取 [啟用串流端點]](#register-the-direct-line-speech-channel)方塊及/或已將 [ **Web 通訊端** 切換](#enable-web-sockets)為開啟。<br>確定您的 Azure App Service 正在執行。 如果是，請嘗試重新開機您的 App Service。|
|錯誤 (ConnectionFailure) ：遠端主機已關閉連接。 錯誤碼：1011。 錯誤詳細資料：回應狀態碼未指出成功： 500 (InternalServerError) | 您的 bot 在其輸出活動 [說話](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 欄位中指定了類神經語音，但與您的語音訂用帳戶金鑰相關聯的 Azure 區域不支援類神經語音。 請參閱 [標準和類神經語音](./regions.md#standard-and-neural-voices)。|

如果您的問題未在表格中解決，請參閱 [語音助理：常見問題](faq-voice-assistants.md)。 如果您在遵循本教學課程中的所有步驟之後仍無法解決問題，請在  [語音助理 GitHub 頁面](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/issues)中輸入新問題。

#### <a name="a-note-on-connection-time-out"></a>連接逾時的注意事項

如果您已連線至 bot，且過去5分鐘內沒有任何活動發生，服務會自動關閉與用戶端和 bot 的 websocket 連線。 這是原廠設定。 下方列中會出現一則訊息：「作用 *中的連線已超時，但已可依需求重新連接*」。 您不需要按下 [重新連接] 按鈕-只需按下麥克風按鈕並開始討論、輸入文字訊息，或說關鍵字 (（如果已啟用) ）。 連接將會自動重新建立。  
### <a name="view-bot-activities"></a>查看 bot 活動

每個 bot 都會傳送和接收 **活動** 訊息。 在 Windows 語音助理用戶端的 [ **活動記錄** ] 視窗中，您會看到有時間戳記的記錄，其中包含用戶端從 bot 收到的每個活動。 您也可以使用方法，查看用戶端傳送至 bot 的活動 [`DialogServiceConnector.SendActivityAsync`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync)  。 當您選取記錄專案時，它會將相關聯活動的詳細資料顯示為 JSON。

以下是用戶端所收到活動的 json 範例：

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration-8726"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

若要深入瞭解 JSON 輸出中傳回的內容，請參閱 [活動中的欄位](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)。 基於本教學課程的目的，您可以將焦點放在 [ [文字](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) ] 和 [ [朗讀](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) ] 欄位。

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>查看語音 SDK 呼叫的用戶端原始程式碼

Windows 語音助理用戶端會使用 NuGet 套件 [CognitiveServices](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)，其中包含語音 SDK。 開始查看範例程式碼的好地方，就是在 file 中 InitSpeechConnector ( # A1 的方法 [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) ，它會建立這兩個語音 SDK 物件：
- [`DialogServiceConfig`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) -針對設定設定 (例如，語音訂用帳戶金鑰、金鑰區域) 
- [`DialogServiceConnector`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) -管理通道連線和用戶端訂用帳戶事件，以處理已辨識的語音和 bot 回應。

## <a name="add-custom-keyword-activation"></a>新增自訂關鍵字啟用

語音 SDK 支援自訂關鍵字啟用。 類似于 Microsoft 助理的 "嗨 Cortana"，您可以撰寫應用程式，以持續接聽您所選擇的關鍵字。 請記住，關鍵字可以是單一單字或多字片語。

> [!NOTE]
> 字詞 *關鍵字* 通常會與「 *喚醒字*」一詞交換使用，您可能會在 Microsoft 檔中看到這兩種使用方式。

關鍵字偵測會在用戶端應用程式上完成。 如果使用關鍵字，則只有在偵測到關鍵字時，才會將音訊串流處理至 Direct Line 語音通道。 Direct Line 語音通道包含稱為 *關鍵字驗證的元件 (KWV)*，它會在雲端中執行更複雜的處理，以確認您所選擇的關鍵字是在音訊串流的開頭。 如果主要字組驗證成功，則通道會與 bot 進行通訊。

遵循下列步驟來建立關鍵字模型、將 Windows 語音助理用戶端設定為使用此模型，最後使用您的 bot 進行測試。

1. 請遵循下列指示， [使用語音服務建立自訂關鍵字](./custom-keyword-basics.md)。
2. 將您在上一個步驟中下載的模型檔案解壓縮。 它應該命名為關鍵字。 您正在尋找名為的檔案 `kws.table` 。
3. 在 Windows 語音助理用戶端中，找出 [ **設定** ] 功能表 (尋找右上方的齒輪圖示) 。 找出 **模型檔案路徑** ，然後輸入 `kws.table` 步驟2中檔案的完整路徑名稱。
4. 請務必勾選標示為 **已啟用** 的方塊。 您應該會在核取方塊旁邊看到此訊息：「在下次連線時，將會接聽關鍵字」。 如果您已提供錯誤的檔案或不正確路徑，您應該會看到錯誤訊息。
5. 輸入您的語音訂用帳戶 **金鑰**（訂用帳戶 **金鑰區域**），然後按一下 **[確定]** 以關閉 [ **設定** ] 功能表。
6. 按一下 [ **重新連接**]。 您應該會看到一則訊息，指出：「新增對話已開始-輸入，按下麥克風按鈕，或說關鍵字」。 應用程式現在會持續接聽。
7. 說出以關鍵字開頭的任何片語。 例如： "**{您的關鍵字}** 是什麼時候？"。 Uttering 關鍵字之後，您就不需要暫停。 完成時，會發生兩件事：
   * 您將會看到您輪輻的轉譯
   * 不久之後，您就會聽到 bot 的回應
8. 繼續試驗您的 bot 支援的三種輸入類型：
   * 輸入-在底部列中的文字
   * 按下麥克風圖示和說話
   * 說出以關鍵字開頭的片語

### <a name="view-the-source-code-that-enables-keyword"></a>查看啟用關鍵字的原始程式碼

在 Windows 語音助理用戶端原始程式碼中，查看這些檔案以查看用來啟用關鍵字偵測的程式碼：

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs) 包含語音 SDK 方法的呼叫 [`KeywordRecognitionModel.fromFile()`](/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel#fromfile-string-) ，這個方法可用來從磁片上的本機檔案具現化模型。
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) 包含語音 SDK 方法的呼叫 [`DialogServiceConnector.StartKeywordRecognitionAsync()`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) ，可啟用連續關鍵字偵測。

## <a name="optional-change-the-language-and-bot-voice"></a> (選擇性) 變更語言和 bot 語音

您所建立的 bot 將會接聽並以英文回應，並使用預設的美國英文文字轉換語音語音。 不過，您不一定要使用英文或預設語音。 在本節中，您將瞭解如何變更 bot 將接聽並回應的語言。 您也將學習如何針對該語言選取不同的聲音。

### <a name="change-the-language"></a>變更語言

您可以從 [語音轉換文字](language-support.md#speech-to-text) 資料表中所述的任何一種語言中選擇。 在下列範例中，我們會將語言變更為德文。

1. 開啟 Windows 語音助理用戶端應用程式，按一下 [設定] 按鈕 (右上方齒輪圖示) ，然後 `de-de` 在 [語言] 欄位中輸入 (這是 [語音轉換文字](language-support.md#speech-to-text) 資料表) 中提及的地區設定值。 這會設定要辨識的語音語言，並覆寫預設值 `en-us` 。 這也會指示 Direct Line 語音通道使用預設德文語音來進行 Bot 回復。
2. 關閉 [設定] 頁面，然後按一下 [重新連接] 按鈕，以建立與您的 echo bot 的新連接。
3. 按一下麥克風按鈕，然後說出德文中的片語。 您將會看到已辨識的文字，以及使用預設德文聲音回復的 echo bot。

### <a name="change-the-default-bot-voice"></a>變更預設的 bot 聲音

如果 Bot 以 [語音合成標記語言](speech-synthesis-markup.md) (SSML) （而非簡單文字）的形式指定回復，則可以選取文字轉換語音語音及控制發音。 Echo bot 不會使用 SSML，但我們可以輕鬆地修改程式碼來執行該動作。 在下列範例中，我們會將 SSML 新增至 echo bot 回復，讓德文 voice Stefan Apollo (男性語音) 將會用來取代預設的女性語音。 請參閱您的語言所支援的 [標準語音](language-support.md#standard-voices) 和類 [神經語音](language-support.md#neural-voices) 清單。

1. 讓我們從開啟開始 `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs` 。
2. 找出這兩行：
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. 將它們取代為：
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. 在 Visual Studio 中建立您的解決方案，並修正任何組建錯誤。

方法 ' MessageFactory ' 中的第二個引數會設定 bot 回復中的 [活動說話欄位](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 。 在上述變更中，已將其從簡單文字取代為 SSML，以指定非預設德文語音。

### <a name="redeploy-your-bot"></a>重新部署您的 bot

現在您已對 bot 進行必要的變更，下一步是將它重新發佈到您的 Azure App Service 並試用：

1. 在 [方案總管] 視窗中，以滑鼠右鍵按一下 **EchoBot** 專案，然後選取 [ **發行**]。
2. 您先前的部署設定已載入為預設值。 只要按一下 [ **EchoBot20190805125647-Web Deploy**] 旁的 [**發佈**] 即可。
3. [ **發行成功** ] 訊息將會出現在 Visual Studio 的 [輸出] 視窗中，而且網頁會啟動並顯示「您的 bot 已準備就緒！」訊息。
4. 開啟 Windows 語音助理用戶端應用程式，按一下 [設定] 按鈕 (右上方齒輪圖示) ，然後確定您 `de-de` 在 [語言] 欄位中仍有。
5. 遵循 [執行 Windows 語音助理用戶端](#run-the-windows-voice-assistant-client) 中的指示，以重新連線至新部署的 bot，以新的語言說話，並聆聽 bot 以該語言回復的新語音。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用在本教學課程中部署的 echo-bot，只要刪除 Azure 資源群組 **SpeechEchoBotTutorial-ResourceGroup**，即可將它和所有相關聯的 Azure 資源移除。

1. 從 [Azure 入口網站](https://portal.azure.com)中，按一下 [ **Azure 服務**] 導覽中的 [**資源群組**]。
2. 尋找名為： **SpeechEchoBotTutorial-ResourceGroup** 的資源群組。 按一下三個點 ( ... ) 。
3. 選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用語音 SDK 建立您自己的用戶端應用程式](./quickstarts/voice-assistants.md?pivots=programming-language-csharp)

## <a name="see-also"></a>另請參閱

* 部署到 [您附近的 Azure 區域](https://azure.microsoft.com/global-infrastructure/locations/) ，以查看 bot 回應時間改進
* 部署至 [支援高品質神經 TTS 語音的 Azure 區域](./regions.md#standard-and-neural-voices)
* 與 Direct Line 語音通道相關聯的定價：
  * [Bot 服務定價](https://azure.microsoft.com/pricing/details/bot-service/)
  * [語音服務](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 建立及部署您自己的具備語音功能的 bot：
  * 建立 [Bot Framework Bot](https://dev.botframework.com/)。 使用 [Direct Line 語音通道](/azure/bot-service/bot-service-channel-connect-directlinespeech) 來註冊，並 [自訂您的 bot 以進行語音](/azure/bot-service/directline-speech-bot)
  * 探索現有的 [Bot Framework 解決方案](https://microsoft.github.io/botframework-solutions/index)：建立 [虛擬](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) 小幫手，並將 [其擴充為 Direct Line 語音](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
