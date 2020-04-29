---
title: 教學課程：使用語音 SDK 將您的 bot 啟用-語音服務
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將使用 Microsoft Bot Framework 建立 Echo Bot、將其部署至 Azure，並向 Bot Framework Direct Line 語音通道註冊。 接著，您將設定適用于 Windows 的範例用戶端應用程式，讓您可以對 bot 說話，並知道它會回復您。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.openlocfilehash: b2c119f6552773bce7bb93a503c22324278ac0bc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399466"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>教學課程：使用語音 SDK 為您的 bot 啟用語音

您現在可以使用語音服務的強大功能，輕鬆地啟用聊天機器人的語音功能。

在本教學課程中，您將使用 Microsoft Bot Framework 建立 Echo Bot、將其部署至 Azure，並向 Bot Framework Direct Line 語音通道註冊。 接著，您將設定適用于 Windows 的範例用戶端應用程式，讓您可以對 bot 說話，並知道它會回復您。

本教學課程是專為剛開始使用 Azure、Bot Framework bot、Direct Line Speech 或 Speech SDK 的開發人員所設計，而且想要快速建立程式碼有限的工作系統。 不需要經驗或熟悉這些服務。

在此練習結束時，您將會設定系統，其運作方式如下：

1. 範例用戶端應用程式已設定為連接到 Direct Line 語音通道和 Echo Bot
1. 當按下按鈕時，會從預設的麥克風錄製音訊（如果已啟用自訂關鍵字，則會持續記錄）
1. 選擇性地進行自訂關鍵字偵測，將音訊串流控制到雲端
1. 使用語音 SDK，應用程式會連線到 Direct Line 語音通道和串流音訊
1. （選擇性）在服務上發生高精確度關鍵字驗證
1. 音訊會傳遞至語音辨識服務並轉譯為文字
1. 已辨識的文字會以 Bot Framework 活動的形式傳遞至 Echo Bot 
1. 文字轉換語音（TTS）服務會將回應文字轉換成音訊，然後串流回到用戶端應用程式進行播放

![圖表-標記](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "語音通道流程")

> [!NOTE]
> 本教學課程中的步驟不需要付費服務。 身為新的 Azure 使用者，您將能夠使用免費 Azure 試用訂用帳戶的信用額度和語音服務的免費層來完成本教學課程。

本教學課程涵蓋下列內容：
> [!div class="checklist"]
> * 建立新的 Azure 資源
> * 建立、測試 Echo Bot 範例，並將其部署至 Azure App Service
> * 向 Direct Line Speech 頻道註冊您的 bot
> * 建立並執行 Windows 語音助理用戶端，以與您的 Echo Bot 互動
> * 新增自訂關鍵字啟用
> * 瞭解如何變更可辨識和說話語音的語言

## <a name="prerequisites"></a>先決條件

以下是完成本教學課程所需的事項：

- 具有正常運作麥克風和喇叭（或耳機）的 Windows 10 電腦
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)或更高版本
- [.NET Framework 執行時間 4.6.1](https://dotnet.microsoft.com/download)或更高版本
- 一個 Azure 帳戶。 [免費註冊](https://azure.microsoft.com/free/ai/)。
- [GitHub](https://github.com/)帳戶
- [適用于 Windows 的 Git](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>建立資源群組

您在本教學課程中建立的用戶端應用程式會使用少數 Azure 服務。 若要減少 bot 回應的來回時間，請確定這些服務都位於相同的 Azure 區域中。 在本節中，您將在**美國西部**區域建立資源群組。 建立 Bot Framework、Direct Line 語音通道和語音服務的個別資源時，將會使用此資源群組。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">建立資源群組<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. 系統會提示您提供一些資訊：
   * 將**訂**用帳戶設定為**免費試用**（您也可以使用現有的訂用帳戶）。
   * 輸入**資源群組**的名稱。 我們建議**SpeechEchoBotTutorial-ResourceGroup**。
   * 從 [**區域**] 下拉式選單中，選取 [**美國西部**]。
1. 按一下 [檢閱及建立]****。 您應該會看到已通過讀取**驗證**的橫幅。
1. 按一下 [建立]  。 可能需要幾分鐘的時間來建立資源群組。
1. 就像您稍後在本教學課程中所建立的資源一樣，將此資源群組釘選到儀表板以方便存取，是個不錯的主意。 如果您想要釘選此資源群組，請按一下儀表板右上方的釘選圖示。

### <a name="choosing-an-azure-region"></a>選擇 Azure 區域

如果您想要在本教學課程中使用不同的區域，這些因素可能會限制您的選擇：

* 請確定您使用的是[支援的 Azure 區域](regions.md#voice-assistants)。
* Direct Line 語音通道會使用文字轉換語音服務，其具有標準和類神經語音。 類神經語音[僅限於特定的 Azure 區域](regions.md#standard-and-neural-voices)。
* 免費試用金鑰可能會限制在特定區域。

如需區域的詳細資訊，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。

## <a name="create-resources"></a>建立資源

現在您已在支援的區域中有資源群組，下一個步驟是為您將在本教學課程中使用的每個服務建立個別的資源。

### <a name="create-a-speech-service-resource"></a>建立語音服務資源

請依照下列指示來建立語音資源：

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">建立語音服務資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 系統會提示您提供一些資訊：
   * 為您的資源**命名**。 我們建議**SpeechEchoBotTutorial-Speech**
   * 針對 [**訂**用帳戶]，請確定已選取 [**免費試用**]。
   * 針對 [**位置**]，選取 [**美國西部**]。
   * 針對 [**定價層**]，選取 [ **F0**]。 這是免費層。
   * 針對 [**資源群組**]，選取 [ **SpeechEchoBotTutorial-ResourceGroup**]。
5. 輸入所有必要的資訊之後，請按一下 [**建立**]。 可能需要幾分鐘的時間來建立您的資源。
6. 稍後在本教學課程中，您將需要此服務的訂用帳戶金鑰。 您可以隨時從資源的**總覽**（管理金鑰）或**金鑰**存取這些金鑰。

此時，請檢查您的資源群組（**SpeechEchoBotTutorial-ResourceGroup**）是否有語音資源：

| 名稱 | 類型  | Location |
|------|-------|----------|
| SpeechEchoBotTutorial-語音 | 認知服務 | 美國西部 |

### <a name="create-an-azure-app-service-plan"></a>建立 Azure App Service 方案

下一個步驟是建立 App Service 計畫。 App Service 方案會針對要執行的 Web 應用程式定義一組計算資源。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">建立 Azure App Service 方案<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 系統會提示您提供一些資訊：
   * 將**訂**用帳戶設定為**免費試用**（您也可以使用現有的訂用帳戶）。
   * 針對 [**資源群組**]，選取 [ **SpeechEchoBotTutorial-ResourceGroup**]。
   * 為您的資源**命名**。 我們建議**SpeechEchoBotTutorial-AppServicePlan**
   * 針對 [**作業系統**]，選取 [ **Windows**]。
   * 針對 [**地區**]，選取 [**美國西部**]。
   * 針對 [**定價層**]，請確定已選取 [**標準 S1** ]。 這應該是預設值。 如果不是，請確定您已將**作業系統**設定為**Windows** ，如上所述。
5. 按一下 [檢閱及建立]****。 您應該會看到已通過讀取**驗證**的橫幅。
6. 按一下 [建立]  。 可能需要幾分鐘的時間來建立資源群組。

此時，請檢查您的資源群組（**SpeechEchoBotTutorial-ResourceGroup**）有兩個資源：

| 名稱 | 類型  | Location |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service 方案 | 美國西部 |
| SpeechEchoBotTutorial-語音 | 認知服務 | 美國西部 |

## <a name="build-an-echo-bot"></a>建立 Echo Bot

既然您已建立一些資源，讓我們來建立 bot。 我們要開始使用 Echo Bot 範例，顧名思義，它會將您輸入的文字回顯為其回應。 別擔心，範例程式碼已準備好讓您在沒有任何變更的情況下使用。 它會設定為使用 Direct Line 語音通道，我們會在我們將 bot 部署至 Azure 之後進行連接。

> [!NOTE]
> 您可以在[GitHub 上的範例讀我檔案](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)中，取得接下來的指示，以及有關 Echo Bot 的其他資訊。

### <a name="run-the-bot-sample-on-your-machine"></a>在您的電腦上執行 bot 範例

1. 複製範例存放庫：

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. 啟動 Visual Studio。
3. 從工具列中 **，選取** > [檔案] [**開啟** > **專案/方案**]，然後開啟 [Echo Bot 專案] 方案：

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. 載入專案之後，按<kbd>F5</kbd>以建立並執行專案。
5. 瀏覽器應該會啟動，而您會看到看起來像這樣的畫面。
    > [!div class="mx-imgBorder"]
    > [![echobot-執行中-localhost](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "在 localhost 上執行的 EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>使用 Bot Framework Emulator 測試 bot 範例

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator)是桌面應用程式，可讓 Bot 開發人員在本機或透過通道從遠端對其 bot 進行測試和偵測。 模擬器支援輸入的文字做為輸入（非語音）。 Bot 會以文字回應。 請遵循下列步驟，使用 Bot Framework Emulator 來測試您在本機執行的 Echo Bot，其中包含文字輸入和文字輸出。 將 bot 部署至 Azure 之後，我們會使用語音輸入和語音輸出進行測試。

1. 安裝4.3.0 或更新版本的[Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest)
2. 啟動 Bot Framework Emulator 並開啟您的 Bot：
   * **File**檔案開啟**Bot。**  -> 
3. 輸入您 bot 的 URL。 例如：

   ```
   http://localhost:3978/api/messages
   ```
   然後按 [連線]。
4. Bot 應該會立即歡迎您 "Hello，歡迎！" 訊息。 輸入任何文字訊息，並確認您收到來自 bot 的回應。
5. 這是與 Echo Bot 實例的通訊交換可能如下所示： [ ![Bot-架構-模擬器](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework 模擬器")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>將您的 bot 部署到 Azure App Service

下一個步驟是將 Echo Bot 部署至 Azure。 有幾種方式可以部署 bot，但在本教學課程中，我們將著重于直接從 Visual Studio 發佈。

> [!NOTE]
> 或者，您可以使用[Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli)和[部署範本](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)來部署 bot。

1. 從 Visual Studio，開啟已設定為與 Direct Line 語音通道搭配使用的 Echo Bot：

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. 在 [**方案總管**中，以滑鼠右鍵按一下**EchoBot**專案，然後選取 [**發佈 ...** ]。
1. 標題為 [**挑選發行目標**] 的新視窗隨即開啟。
1. 從 [ **Azure 服務**] 導覽選取 [ **App Service** ]，選取 **[新建]，然後**按一下 [**建立設定檔**]。
1. 當 [**建立 App Service** ] 視窗出現時：
   * 按一下 [**新增帳戶**]，然後使用您的 Azure 帳號憑證登入。 如果已經登入，請從下拉式清單中選取要使用的帳戶。
   * 針對 [**應用程式名稱**]，您必須輸入 Bot 的全域唯一名稱。 這個名稱是用來建立唯一的 bot URL。 預設值將會填入，包括日期和時間（例如： "EchoBot20190805125647"）。 您可以使用本教學課程的預設名稱。
   * 針對 [**訂**用帳戶]，將其設定為**免費試用**
   * 針對 [**資源群組**]，選取 [ **SpeechEchoBotTutorial-ResourceGroup** ]
   * 針對**主控方案**，選取 [ **SpeechEchoBotTutorial-AppServicePlan** ]
   * 針對 [ **Application Insights**]，保留為 [**無**]
1. 按一下 [建立] 
1. 您應該會看到 Visual Studio 中的成功訊息，如下所示：

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. 您的預設瀏覽器應該會開啟並顯示一頁：「您的 bot 已就緒！」。
1. 此時，請檢查 Azure 入口網站中的資源群組**SpeechEchoBotTutorial-ResourceGroup** ，並確認有三個資源：

| 名稱 | 類型  | Location |
|------|-------|----------|
| EchoBot20190805125647 | App Service 方案 | 美國西部 |
| SpeechEchoBotTutorial-AppServicePlan | App Service 方案 | 美國西部 |
| SpeechEchoBotTutorial-語音 | 認知服務 | 美國西部 |

## <a name="enable-web-sockets"></a>啟用 Web 通訊端

您將需要進行小型的設定變更，讓您的 bot 可以使用 web 通訊端與 Direct Line 語音通道進行通訊。 請遵循下列步驟來啟用 web 通訊端：

1. 流覽至 [ [Azure 入口網站](https://portal.azure.com)]，然後找出您的 App Service。 資源的名稱應該與**EchoBot20190805125647** （您的唯一應用程式名稱）類似。
2. 在**Azure 服務**流覽的 [設定] 底下，**按一下 [****設定**]。
3. 選取 [**一般設定**] 索引標籤。
4. 找出**Web 通訊端**的切換，並將其設定為 [**開啟**]。
5. 按一下 **[儲存]** 。

> [!TIP]
> 您可以使用 Azure App Service 頁面頂端的控制項來停止或重新開機服務。 進行疑難排解時，這可能會很有用。

## <a name="create-a-channel-registration"></a>建立通道註冊

既然您已建立 Azure App Service 來裝載 bot，下一步就是建立**Bot 通道註冊**。 建立通道註冊是使用 Bot Framework 通道註冊 bot 的必要條件，包括 Direct Line 語音通道。

> [!NOTE]
> 如果您想要深入瞭解 bot 如何運用通道，請參閱[將 bot 連線至通道](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)。


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">建立 Azure Bot 通道註冊<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. 系統會提示您提供一些資訊：
   * 針對 [ **Bot 控制碼**]，輸入**SpeechEchoBotTutorial-BotRegistration**。
   * 針對 [**訂**用帳戶]，選取 [**免費試用**]。
   * 針對 [**資源群組**]，選取 [ **SpeechEchoBotTutorial-ResourceGroup**]。
   * 針對 [**位置**]，選取 [**美國西部**]。
     * 針對 [**定價層**]，選取 [ **F0**]。
     * 針對 [**訊息端點**]，輸入 web 應用程式的 URL， `/api/messages`並在結尾附加路徑。 例如：如果您的全域唯一應用程式名稱是**EchoBot20190805125647**，則您的訊息端點`https://EchoBot20190805125647.azurewebsites.net/api/messages/`會是：。
     * 針對**application insights**，您可以將此設為**Off**。 如需詳細資訊，請參閱[Bot analytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0)。
     * 忽略**自動建立應用程式識別碼和密碼**。
5. 在 [ **Bot 通道註冊**] 分頁底部，按一下 [**建立**]。

此時，請檢查 Azure 入口網站中的資源群組**SpeechEchoBotTutorial-ResourceGroup** 。 它現在應該會顯示四個資源：

| 名稱 | 類型  | Location |
|------|-------|----------|
| EchoBot20190805125647 | App Service 方案 | 美國西部 |
| SpeechEchoBotTutorial-AppServicePlan | App Service 方案 | 美國西部 |
| SpeechEchoBotTutorial-BotRegistration | Bot 通道註冊 | 全域 |
| SpeechEchoBotTutorial-語音 | 認知服務 | 美國西部 |

> [!IMPORTANT]
> 即使您選取 [美國西部]，Bot 通道註冊資源仍會顯示全域區域。 這是預期的結果。

## <a name="register-the-direct-line-speech-channel"></a>註冊 Direct Line 語音訊道

現在可以使用 Direct Line Speech 通道來註冊您的 bot。 此通道是用來在您的 echo bot 和使用語音 SDK 編譯的用戶端應用程式之間建立連線的。

1. 在[Azure 入口網站](https://portal.azure.com)中找出並開啟您的**SpeechEchoBotTutorial-BotRegistration**資源。
1. 從**Azure 服務**導覽中，選取 [**通道**]。
   * 尋找**更多頻道**，找出並按一下 [ **Direct Line 語音**]。
   * 檢查標題為 [**設定 Direct Line Speech**] 頁面上的文字，然後展開標示為 [認知服務帳戶] 的下拉式功能表。
   * 從功能表中選取您稍早建立的語音資源（例如， **SpeechEchoBotTutorial-speech**），以將您的 bot 與您的語音訂用帳戶金鑰建立關聯。
   * 按一下 **[儲存]** 。

1. 從**Bot 管理**導覽中，按一下 [**設定**]。
   * 選取標示為 [**啟用串流端點**] 的方塊。 這是為了讓您的 bot 與 Direct Line Speech 通道之間的 web 通訊端建立通訊協定所需。
   * 按一下 **[儲存]** 。

> [!TIP]
> 如果您想要深入瞭解，請參閱[將 bot 連線至 Direct Line 語音](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)。 此頁面包含其他資訊和已知問題。

## <a name="run-the-windows-voice-assistant-client"></a>執行 Windows 語音助理用戶端

在此步驟中，您將執行 Windows 語音助理用戶端。 用戶端是 c # 中的 Windows Presentation Foundation （WPF）應用程式，它會使用[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk)來管理使用 Direct Line speech 通道與 bot 的通訊。 在撰寫自訂用戶端應用程式之前，使用它來與您的 bot 互動並加以測試。 它是開放原始碼，因此您可以下載可執行檔並加以執行，或自行建立。

Windows 語音助理用戶端具有簡單的 UI，可讓您設定與 bot 的連線、查看文字交談、以 JSON 格式來查看 Bot 架構活動，以及顯示調適型卡片。 它也支援使用自訂關鍵字。 您會使用此用戶端來與您的 bot 交談並接收語音回應。

在我們繼續之前，請確定您的麥克風和喇叭已啟用並正常運作。

1. 流覽至[Windows 語音助理用戶端](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)的 GitHub 存放庫。
1. 依照此處提供的指示進行
   * 下載包含要執行之可執行檔的 ZIP 套件，或
   * 藉由複製存放庫並建立專案，自行建立可執行檔。

1. 啟動用戶端應用程式並加以設定。
1. 按一下 [**重新連接**]，並確定您看到訊息**按下 mic 按鈕，或輸入以開始與您的 bot 交談**。
1. 讓我們來測試一下。按一下 [麥克風] 按鈕，並說出幾個英文文字。 當您說話時，會顯示已辨識的文字。 當您說完時，bot 會以自己的聲音回復，並在後面加上可辨識的文字。
1. 您也可以使用文字與 bot 進行通訊。 只要在底部列中輸入文字就行了。 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>針對 Windows 語音助理用戶端中的錯誤進行疑難排解

如果您在主要應用程式視窗中收到錯誤訊息，請使用此資料表來識別錯誤並進行疑難排解：

| 錯誤 | 您應該怎麼做？ |
|-------|----------------------|
|錯誤 AuthenticationFailure： WebSocket 升級失敗，發生驗證錯誤（401）。 檢查是否有正確的訂用帳戶金鑰（或授權權杖）和區功能變數名稱稱| 在應用程式的 [設定] 頁面中，請確定您已正確輸入語音訂用帳戶金鑰和其區域。<br>請確定已正確輸入您的語音金鑰和金鑰區域。 |
|錯誤 ConnectionFailure：遠端主機已關閉連接。 錯誤碼：1011。 錯誤詳細資料：我們無法在傳送訊息之前連線到 bot | 請確定您[已核取 [啟用串流端點]](#register-the-direct-line-speech-channel)方塊及/或已將[ **Web 通訊端**切換](#enable-web-sockets)為 [開啟]。<br>請確定您的 Azure App Service 正在執行。 如果是，請嘗試重新開機您的 App Service。|
|錯誤 ConnectionFailure：遠端主機已關閉連接。 錯誤碼：1011。 錯誤詳細資料：回應狀態碼未指出成功：500（InternalServerError）| 您的 bot 在其 [[輸出活動]](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)欄位中指定了類神經語音，但與語音訂用帳戶金鑰相關聯的 Azure 區域不支援類神經語音。 請參閱[標準和類神經語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)。|
|錯誤 ConnectionFailure：遠端主機已關閉連接。 錯誤碼：1000。 錯誤詳細資料：超過 web 通訊端連接閒置持續時間上限（> 300000 毫秒）| 當通道的連線保持開啟且非使用中超過五分鐘時，這是預期的錯誤。 |

如果您的問題未在表格中解決，請參閱[語音助理：常見問題](faq-voice-assistants.md)。

### <a name="view-bot-activities"></a>查看 bot 活動

每個 bot 都會傳送及接收**活動**訊息。 在 Windows 語音助理用戶端的 [**活動記錄**] 視窗中，您會看到有時間戳記的記錄，其中包含用戶端從 bot 收到的每個活動。 您也可以使用[`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync)方法，查看用戶端傳送至 bot 的活動。 當您選取記錄檔專案時，它會將相關聯活動的詳細資料顯示為 JSON。

以下是用戶端收到的活動範例 json：

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
        "id":"SpeechEchoBotTutorial-BotRegistration"
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

若要深入瞭解 JSON 輸出中傳回的內容，請參閱[活動中的欄位](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)。 基於本教學課程的目的，您可以將焦點放在[文字](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text)和[說話](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)欄位。

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>查看語音 SDK 呼叫的用戶端原始程式碼

Windows 語音助理用戶端會使用[CognitiveServices](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)的 NuGet 套件，其中包含語音 SDK。 開始審查範例程式碼的好地方是檔案中的方法 InitSpeechConnector （） [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)，它會建立這兩個語音 SDK 物件：
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)-針對設定（例如語音訂用帳戶金鑰、金鑰區域）
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)-管理通道連線和用戶端訂閱事件，以處理可辨識的語音和 bot 回應。

## <a name="add-custom-keyword-activation"></a>新增自訂關鍵字啟用

語音 SDK 支援自訂關鍵字啟用。 類似于 Microsoft 小幫手的「嘿 Cortana」，您可以撰寫應用程式，以持續接聽您選擇的關鍵字。 請記住，關鍵字可以是單一單字或多字片語。

> [!NOTE]
> 詞彙*關鍵字*通常與「*喚醒字*」一詞互換使用，您可能會在 Microsoft 檔中看到這兩者。

已在用戶端應用程式上完成關鍵字偵測。 如果使用關鍵字，只有在偵測到關鍵字時，才會將音訊串流處理至 Direct Line 的語音訊道。 Direct Line 語音通道包含一個稱為*關鍵字驗證（KWV）* 的元件，它會在雲端中執行更複雜的處理，以確認您所選擇的關鍵字位於音訊串流的開頭。 如果成功驗證關鍵字，則通道會與 bot 通訊。

請遵循下列步驟來建立關鍵字模型、設定 Windows 語音助理用戶端使用此模型，最後再使用您的 bot 進行測試。

1. 遵循這些指示，[使用語音服務來建立自訂關鍵字](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)。
2. 將您在上一個步驟中下載的模型檔案解壓縮。 它應該以您的關鍵字命名。 您正在尋找名為`kws.table`的檔案。
3. 在 Windows 語音助理用戶端中，找出 [**設定**] 功能表（尋找右上方的齒輪圖示）。 尋找**Model file path** [ `kws.table`模型檔案路徑]，然後輸入步驟2中檔案的完整路徑名稱。
4. 請務必核取標示為 [**已啟用**] 的方塊。 您應該會在核取方塊旁看到此訊息：「將在下一次連接時接聽關鍵字」。 如果您提供錯誤的檔案或不正確路徑，您應該會看到錯誤訊息。
5. 輸入您的語音訂用帳戶**金鑰**[訂用帳戶**金鑰區域**]，然後按一下 **[確定]** 以關閉 [**設定**] 功能表。
6. 按一下 [**重新連接**]。 您應該會看到一則訊息：「新對話已開始-輸入，按下麥克風按鈕，或說關鍵字」。 應用程式現在會持續接聽。
7. 說出以關鍵字開頭的任何片語。 例如： "**{您的關鍵字}**，有什麼時間？"。 您不需要在 uttering 關鍵字之後暫停。 完成時，會發生兩件事：
   * 您會看到您輪輻的文字記錄
   * 在不久之後，您會聽到 bot 的回應
8. 繼續實驗您的 bot 支援的三種輸入類型：
   * 輸入-底部列中的文字
   * 按下麥克風圖示和說話
   * 說出以關鍵字開頭的片語

### <a name="view-the-source-code-that-enables-keyword"></a>查看啟用關鍵字的原始程式碼

在 Windows 語音助理用戶端原始程式碼中，請查看這些檔案，以查看用來啟用關鍵字偵測的程式碼：

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)包含語音 SDK 方法[`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)的呼叫，它是用來從磁片上的本機檔案具現化模型。
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)包含語音 SDK 方法[`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)的呼叫，它會啟動連續關鍵字偵測。

## <a name="optional-change-the-language-and-bot-voice"></a>選擇性變更語言和 bot 的聲音

您所建立的 bot 會以英文來接聽和回應，並使用預設的美國英文文字轉換語音語音。 不過，您不限於使用英文或預設語音。 在本節中，您將瞭解如何變更 bot 將接聽並回應的語言。 您也將瞭解如何為該語言選取不同的語音。

### <a name="change-the-language"></a>變更語言

您可以選擇[語音轉換文字](language-support.md#speech-to-text)資料表中所述的任何一種語言。 在下列範例中，我們會將語言變更為德文。

1. 開啟 Windows 語音助理用戶端應用程式，按一下 [設定] 按鈕（右上方的齒輪圖示），然後`de-de`在 [語言] 欄位中輸入（這是[語音轉換文字](language-support.md#speech-to-text)資料表中所述的地區設定值）。 這會設定要辨識的語音語言，並覆寫`en-us`預設值。 這也會指示 Direct Line 語音通道使用 Bot 回復的預設德文語音。
2. 關閉 [設定] 頁面，然後按一下 [重新連接] 按鈕，以建立與您 echo bot 的新連線。
3. 按一下 [麥克風] 按鈕，然後在德文中說出一個片語。 您會看到已辨識的文字，而 echo bot 會以預設的德文語音回復。

### <a name="change-the-default-bot-voice"></a>變更預設的 bot 語音

如果 Bot 以[語音合成標記語言](speech-synthesis-markup.md)（SSML）的形式指定回復，而不是簡單文字，則選取文字轉換語音的語音和控制發音可以完成。 Echo bot 不會使用 SSML，但我們可以輕鬆地修改程式碼來執行此動作。 在下列範例中，我們會將 SSML 新增至 echo bot 回復，這樣就會使用德文 voice Stefan Apollo （男性語音），而不是預設的女性聲音。 請參閱您的語言支援的[標準語音](language-support.md#standard-voices)和類[神經語音](language-support.md#neural-voices)清單。

1. 讓我們從開啟`samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`開始。
2. 找出這兩行：
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. 取代為：
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. 在 Visual Studio 中建立您的解決方案，並修正任何組建錯誤。

方法 ' MessageFactory ' 中的第二個引數會設定 bot 回復中的[活動說話欄位](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)。 在上述變更中，已將其從簡單的文字取代為 SSML，以指定非預設的德文語音。

### <a name="redeploy-your-bot"></a>重新部署您的 bot

既然您已對 bot 進行必要的變更，下一步就是將它重新發佈至您的 Azure App Service 並試試看：

1. 在 [方案總管] 視窗中，以滑鼠右鍵按一下**EchoBot**專案，然後選取 [**發佈**]。
2. 先前的部署設定已載入為預設值。 只要按一下 [ **EchoBot20190805125647-Web Deploy**] 旁的 [**發佈**] 即可。
3. [**發佈成功**] 訊息會出現在 [Visual Studio 輸出] 視窗中，而網頁會啟動並顯示「您的 bot 已就緒！」訊息。
4. 開啟 Windows 語音助理用戶端應用程式，按一下 [設定] 按鈕（右上方的齒輪圖示），並確定 [語言] `de-de`欄位中仍然有。
5. 依照執行[Windows 語音助理用戶端](#run-the-windows-voice-assistant-client)中的指示，重新連線到您新部署的 bot，以新的語言說話，並以新的語音收聽 bot 的回復。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用在本教學課程中部署的 echo bot，您只要刪除 Azure 資源群組**SpeechEchoBotTutorial**，就可以移除它及其所有相關聯的 azure 資源。

1. 從[Azure 入口網站](https://portal.azure.com)按一下 [ **Azure 服務**] 導覽中的 [**資源群組**]。
2. 尋找名為： **SpeechEchoBotTutorial-ResourceGroup**的資源群組。 按一下三個點（...）。
3. 選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用語音 SDK 建立您自己的用戶端應用程式](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>請參閱

* 部署到[您附近的 Azure 區域](https://azure.microsoft.com/global-infrastructure/locations/)，以查看 bot 回應時間改進
* 部署至[支援高品質神經 TTS 語音的 Azure 區域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* 與 Direct Line 語音通道相關聯的定價：
  * [Bot 服務定價](https://azure.microsoft.com/pricing/details/bot-service/)
  * [語音服務](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 建立及部署您自己的已啟用語音的 bot：
  * 建立[Bot Framework bot](https://dev.botframework.com/)。 向[Direct Line 語音訊道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)註冊，並[自訂您的 bot 以進行語音](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * 探索現有的[Bot Framework 解決方案](https://microsoft.github.io/botframework-solutions/index)：建立[虛擬](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/)小幫手，並[將其擴充為 Direct Line 語音](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
