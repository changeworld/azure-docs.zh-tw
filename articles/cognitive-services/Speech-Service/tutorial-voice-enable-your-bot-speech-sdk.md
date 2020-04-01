---
title: 教學:語音使用語音 SDK 開啟您的自動程式 - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本教學中,您將使用 Microsoft 機器人架構創建 Echo Bot,將其部署到 Azure,並將其註冊到機器人框架直接行語音通道。 然後,您將為 Windows 配置一個範例用戶端應用,該應用允許您與機器人通話,並聽到它回應您。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 3c2d74eb7e46d9909d87a7ccadadd6129a3d48d8
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397888"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>教學:使用語音 SDK 啟用自動程式

現在,您可以使用語音服務的強大功能輕鬆啟用語音聊天機器人。

在本教學中,您將使用 Microsoft 機器人架構創建 Echo Bot,將其部署到 Azure,並將其註冊到機器人框架直接行語音通道。 然後,您將為 Windows 配置一個範例用戶端應用,該應用允許您與機器人通話,並聽到它回應您。

本教程專為剛剛開始使用 Azure、機器人框架機器人、直線語音或語音 SDK 的開發人員而設計,並希望快速構建編碼有限的工作系統。 無需體驗或熟悉這些服務。

在本練習結束時,您將設置一個系統,該系統將運行如下:

1. 範例客戶端應用程式設定為連接到直線語音頻道和回聲自動程式
1. 音訊從按鈕按下的預設麥克風錄製(如果啟動自訂關鍵字,則連續錄製)
1. 可選地,自定義關鍵字檢測發生,將音訊流門到雲
1. 使用語音 SDK,應用程式連線到直線語音頻道並流式傳輸音訊
1. 可選,更高的精度關鍵字驗證發生在服務上
1. 音訊傳遞給語音辨識服務並轉錄為文字
1. 識別的文字為機器人框架活動傳遞給回聲機器人 
1. 回應文字透過文字到語音 (TTS) 服務轉換為音訊,並流式傳輸回客戶端應用程式以播放

![圖表標記](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "語音通道串流")

> [!NOTE]
> 本教程中的步驟不需要付費服務。 作為新的 Azure 使用者,您可以使用免費 Azure 試用版訂閱的配額和語音服務的免費套餐來完成本教程。

本教學課程涵蓋下列內容：
> [!div class="checklist"]
> * 建立新的 Azure 資源
> * 產生、測試和將 Echo Bot 範例部署到 Azure 應用服務
> * 使用直線語音頻道註冊您的自動程式
> * 產生並執行 Windows 語音助理用戶端,以便與您的 Echo 機器人互動
> * 新增自訂關鍵字啟動
> * 學習改變公認的口語和口語的語言

## <a name="prerequisites"></a>Prerequisites

以下是完成本教學所需的內容:

- 帶有工作麥克風和揚聲器(或耳機)的 Windows 10 電腦
- [視覺工作室 2017](https://visualstudio.microsoft.com/downloads/)或更高版本
- [.NET 核心 SDK](https://dotnet.microsoft.com/download)版本 2.1 或更高版本
- 一個 Azure 帳戶。 [免費註冊](https://azure.microsoft.com/free/ai/)。
- [GitHub](https://github.com/)帳號
- [將視窗的 Git](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>建立資源群組

您將在本教學中創建的用戶端應用使用少量 Azure 服務。 要減少來自自動程式回應的往返時間,您需要確保這些服務位於同一 Azure 區域中。 在本節中,您將**在美國西部**區域創建一個資源組。 在為 Bot 框架、直線語音通道和語音服務創建單個資源時,將使用此資源群組。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">建立資源群組<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. 系統將提示您提供一些資訊:
   * 將**訂閱**設置為**免費試用**版(您還可以使用現有訂閱)。
   * 輸入**資源組**的名稱。 我們建議**語音回聲機器人教程-資源組**。
   * 從 **「區域**」下拉清單,選擇**美國西部**。
1. 按一下 [檢閱及建立]****。 您應該會看到一個橫幅,該橫幅讀取**驗證通過**。
1. 按一下頁面底部的 [新增]  。 創建資源組可能需要幾分鐘時間。
1. 與本教程稍後將創建的資源一樣,最好將此資源組固定到儀錶板以方便訪問。 如果要固定此資源組,請單擊儀錶板右上角的固定圖示。

### <a name="choosing-an-azure-region"></a>選擇 Azure 區域

如果您想在本教學中使用其他區域,這些因素可能會限制您的選擇:

* 確保使用[支援的 Azure 區域](regions.md#voice-assistants)。
* 直線語音通道使用文本到語音服務,該服務具有標準和神經語音。 神經語音[只限於特定的 Azure 區域](regions.md#standard-and-neural-voices)。
* 免費試用密鑰可能僅限於特定區域。

關於區域的詳細資訊,請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。

## <a name="create-resources"></a>建立資源

現在,您已在受支援的區域中擁有一個資源組,下一步是為本教程中要使用的每個服務創建單個資源。

### <a name="create-a-speech-service-resource"></a>建立語音服務資源

依以下說明建立語音資源:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">建立語音服務資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 系統將提示您提供一些資訊:
   * 指定名稱 **。** 我們建議**語音回聲機器人教程-語音**
   * 對於**訂閱**,請確保選擇了**免費試用**版。
   * 對於**位置**,請選擇**美國西部**。
   * 對於**定價層**,選擇**F0**。 這是免費套餐。
   * 對於**資源群組**,選擇**語音回聲機器人教程 -資源組**。
5. 輸入所有必需資訊后,按一下「**創建**」。。 創建資源可能需要幾分鐘時間。
6. 在本教學的後面部分,您將需要此服務的訂閱金鑰。 您可以隨時從資源的**概述**(管理金鑰)或**密鑰**訪問這些密鑰。

此時,請檢查您的資源群組 (**語音EchoBot教程-資源群組**) 具有語音資源:

| 名稱 | 類型  | Location |
|------|-------|----------|
| 語音回聲機器人教程-語音 | 認知服務 | 美國西部 |

### <a name="create-an-azure-app-service-plan"></a>建立 Azure App Service 方案

下一步是創建應用服務計劃。 App Service 方案會針對要執行的 Web 應用程式定義一組計算資源。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">建立 Azure 應用服務計劃<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 系統將提示您提供一些資訊:
   * 將**訂閱**設置為**免費試用**版(您還可以使用現有訂閱)。
   * 對於**資源群組**,選擇**語音回聲機器人教程 -資源組**。
   * 指定名稱 **。** 我們建議**語音回聲機器人教程-應用程式服務計劃**
   * 對**作業系統**,請選擇**Windows**。
   * 對於**區域**,選擇**美國西部**。
   * 對於**定價層**,請確保選擇了**標準 S1。** 這應該是預設值。 如果不是,請確保將**操作系統**設置為**Windows,** 如上所述。
5. 按一下 [檢閱及建立]****。 您應該會看到一個橫幅,該橫幅讀取**驗證通過**。
6. 按一下頁面底部的 [新增]  。 創建資源組可能需要幾分鐘時間。

此時,請檢查您的資源群組 (**語音EchoBot教程-資源群組**) 有兩個資源:

| 名稱 | 類型  | Location |
|------|-------|----------|
| 語音回聲機器人教學-應用程式服務計劃 | App Service 方案 | 美國西部 |
| 語音回聲機器人教程-語音 | 認知服務 | 美國西部 |

## <a name="build-an-echo-bot"></a>建構回聲機器人

現在,您已經創建了一些資源,讓我們構建一個自動程式。 我們將從 Echo Bot 示例開始,顧名思義,該示例與您輸入的文本迴響為回應。 別擔心,示例代碼已準備就緒,無需進行任何更改即可使用。 它配置為使用直接線路語音通道,我們將在將自動程式部署到 Azure 後連接該通道。

> [!NOTE]
> 以下說明以及有關 Echo Bot 的其他資訊可在[GitHub 上的 README 中](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)獲取。

### <a name="run-the-bot-sample-on-your-machine"></a>在電腦上執行自動程式範例

1. 克隆示例儲存庫:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. 啟動 Visual Studio。
3. 從工具列中,選擇 **「檔案** > **開啟** > **專案/解決方案**」,然後開啟回聲機器人專案解決方案:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. 載入專案後,按<kbd>F5</kbd>生成並運行專案。
5. 瀏覽器應啟動,您將看到一個螢幕如下所示。
    > [!div class="mx-imgBorder"]
    > [![本地主機上的回聲機器人執行](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "在本地主機上執行的 EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>使用機器人框架模擬器測試機器人範例

[機器人框架模擬器](https://github.com/microsoft/botframework-emulator)是一個桌面應用程式,允許機器人開發人員在本地或通過隧道遠端測試和調試其機器人。 模擬器支援鍵入的文本作為輸入(而不是語音)。 自動程式將用文本進行回應。 按照以下步驟使用機器人框架模擬器測試在本地運行的 Echo Bot,並帶有文本輸入和文本輸出。 將自動程式部署到 Azure 後,我們將使用語音輸入和語音輸出對其進行測試。

1. 安裝[機器人框架模擬器](https://github.com/Microsoft/BotFramework-Emulator/releases/latest)版本 4.3.0 或更高版本
2. 啟動機器人框架模擬器並開啟您的自動程式:
   * **檔案** -> **開啟機器人**。
3. 輸入自動程式的 URL。 例如：

   ```
   http://localhost:3978/api/messages
   ```
   然後按"連接」。
4. 機器人應該立即迎接你與"你好,歡迎! 訊息。 鍵入任何文字訊息,並確認您從自動程式獲得回應。
5. 這就是與 Echo Bot 實體交換通訊可能如下所示的內容:[![機器人框架-模擬器](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "機器人框架模擬器")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>將自動程式部署到 Azure 應用服務

下一步是將回聲自動程式部署到 Azure。 有幾種方法可以部署自動程式,但在本教程中,我們將專注於直接從 Visual Studio 發佈。

> [!NOTE]
> 或者,可以使用[Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli)和[部署範本](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)部署自動程式。

1. 從 Visual Studio,打開已設定為與直線語音頻道一起使用的回聲機器人:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. 在**解決方案資源管理器**中,右鍵單擊**EchoBot**專案並選擇 **"發布..."**
1. 將打開名為 **「選取發佈目標」** 的新視窗。
1. 從**Azure 服務**導航中選擇**應用服務**,選擇 **"新建**",然後單擊"**創建設定檔**"。
1. 使用 **「建立應用服務」** 視窗時:
   * 按下「**添加帳戶**」,然後使用 Azure 帳戶憑據登入。 如果已經登入，請從下拉式清單中選取要使用的帳戶。
   * 對於**應用名稱**,您需要為自動程式輸入全域唯一名稱。 此名稱用於創建唯一的自動程式 URL。 將填充一個預設值,包括日期和時間(例如:「EchoBot20190805125647」)。 您可以為本教程使用預設名稱。
   * 對於**訂閱**,將其設定為**免費試用**
   * 對於**資源組**,選擇**語音回聲機器人教程-資源組**
   * 對於**託管計劃**,選擇**語音EchoBot教程-應用程式服務計劃**
   * 對於**應用程式見解**,請保留為 **"無"**
1. 按下 **"創建"**
1. 您應該在 Visual Studio 中看到一條如下所示的成功消息:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. 默認瀏覽器應打開並顯示一個頁面,內容為:「您的自動程式已準備就緒!
1. 此時,請檢查 Azure 門戶中的資源組**語音EchoBot教程-資源組**,並確認有三種資源:

| 名稱 | 類型  | Location |
|------|-------|----------|
| EchoBot20190805125647 | App Service 方案 | 美國西部 |
| 語音回聲機器人教學-應用程式服務計劃 | App Service 方案 | 美國西部 |
| 語音回聲機器人教程-語音 | 認知服務 | 美國西部 |

## <a name="enable-web-sockets"></a>啟用 Web 通訊端

您需要進行一些小的配置更改,以便您的機器人可以使用 Web 套接字與直接語音通道進行通信。 依以下步驟啟用 Web 套接字:

1. 導航到[Azure 門戶](https://portal.azure.com),並找到應用服務。 資源應命名為**類似 EchoBot20190805125647(** 您的唯一應用名稱)。
2. 在**Azure 服務**導航中,在 **「設定」** 下單擊「**配置**」 。
3. 選擇「**常規設定」** 選項卡。
4. 找到**Web 套接字的**切換,並將其設置為 **「打開**」 。
5. 按一下 [儲存]****。

> [!TIP]
> 您可以使用 Azure 應用服務頁頂部的控制項停止或重新啟動服務。 在故障排除時,這可能派上用場。

## <a name="create-a-channel-registration"></a>建立通道註冊

現在,您已經建立了 Azure 應用服務來託管您的自動程式,下一步是建立**一個自動程式通道註冊**。 創建通道註冊是使用 Bot 框架頻道(包括直撥語音通道)註冊機器人的先決條件。

> [!NOTE]
> 如果您想瞭解有關機器人如何利用通道的資訊,請參閱[將機器人連接到通道](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)。


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">建立 Azure 自動程式通道註冊<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. 系統將提示您提供一些資訊:
   * 對於**自動處理**,輸入**語音回聲機器人教程-自動註冊**。
   * 此**訂閱**,選擇**免費試用**。
   * 對於**資源群組**,選擇**語音回聲機器人教程 -資源組**。
   * 對於**位置**,請選擇**美國西部**。
     * 對於**定價層**,選擇**F0**。
     * 對於**消息終結點**,輸入 Web 應用`/api/messages`的 URL, 末尾附加路徑。 例如:如果您的全域唯一應用程式名稱是**EchoBot20190805125647,** 則訊息傳遞終結點為`https://EchoBot20190805125647.azurewebsites.net/api/messages/`: 。
     * 對於**應用程式見解**,您可以將其設置為 **"關閉**"。 有關詳細資訊,請參閱[機器人分析](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0)。
     * 忽略**自動建立應用程式識別碼與密碼**。
5. 在**機器人通道註冊「 邊**欄選項卡的底部,按下」**創建**」。

此時,請在 Azure 門戶中檢查資源組**語音EchoBot教程-資源組**。 它現在應該顯示四個資源:

| 名稱 | 類型  | Location |
|------|-------|----------|
| EchoBot20190805125647 | App Service 方案 | 美國西部 |
| 語音回聲機器人教學-應用程式服務計劃 | App Service 方案 | 美國西部 |
| 語音回聲機器人教程-機器人註冊 | 機器人通道註冊 | 全域 |
| 語音回聲機器人教程-語音 | 認知服務 | 美國西部 |

> [!IMPORTANT]
> 即使選擇了美國西部,"機器人通道註冊"資源也會顯示全域區域。 這是預期行為。

## <a name="register-the-direct-line-speech-channel"></a>註冊直線語音通道

現在是時候註冊你的機器人到直接線語音頻道。 此通道用於在回聲機器人和使用語音 SDK 編譯的用戶端應用之間創建連接。

1. 在[Azure 門戶](https://portal.azure.com)中尋找並打開**語音EchoBot教程-自動註冊**資源。
1. 從**Azure 服務**導航中,選擇 **「通道**」。
   * 尋找**更多頻道**,找到並按下 **「直線語音**」。。
   * 查看標題為 **「設定直線語音**」的頁面上的文本,然後展開標記為「認知服務帳戶」的下拉菜單。
   * 從選單中選擇您之前創建的語音資源(例如,**語音EchoBotTutorial-語音**),將自動程式與語音訂閱鍵相關聯。
   * 按一下 [儲存]****。

1. 在**機器人管理**「導航中,按下」**設定**。
   * 選中標記為啟用**流式處理終結點**的框。 這是必需的,以啟用在自動程式與直線語音通道之間的 Web 套接字上構建的通信協定。
   * 按一下 [儲存]****。

> [!TIP]
> 如果您想瞭解更多資訊,請參閱[將自動程式連接到直線語音](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)。 此頁包括其他資訊和已知問題。

## <a name="build-the-windows-voice-assistant-client"></a>建構 Windows 語音助理用戶端

在此步驟中,您將構建 Windows 語音助理用戶端。 用戶端是 C# 中的 Windows 演示文稿基礎 (WPF) 應用,它使用[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk)使用直接線路語音頻道管理與機器人的通信。 在編寫自定義客戶端應用之前,使用它與機器人進行交互並測試。

Windows 語音助理用戶端具有一個簡單的 UI,允許您設定與自動程式的連接、查看文本對話、以 JSON 格式查看 Bot-Framework 活動以及顯示自適應卡。 它還支援使用自定義關鍵字。 您將使用此用戶端與您的機器人對話並接收語音回應。

在我們繼續之前,請確保您的麥克風和揚聲器已啟用並正常工作。

1. 導航到[Windows 語音助理用戶端](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)的 GitHub 儲存庫。
2. 按照提供的指令複製儲存庫、生成專案、配置用戶端和啟動用戶端。
3. 點選 **「重新連線**」並確保看到訊息 **「按麥克風」按鈕,或鍵入以開始與機器人對話**。
4. 讓我們測試一下。按下麥克風按鈕,用英語說幾句話。 您說話時將顯示識別的文字。 當你說完之後,機器人會用自己的聲音回答,說"回聲",然後是識別的單詞。
5. 您還可以使用文本與機器人通信。 只需在底部欄中鍵入文本。 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>解決 Windows 語音助理用戶端中的錯誤

如果在主應用視窗中收到錯誤訊息,請使用此表來識別和排除錯誤:

| 錯誤 | 您應該怎麼做？ |
|-------|----------------------|
|錯誤身份驗證失敗:WebSocket 升級失敗,身份驗證錯誤 (401)。 檢查正確的訂閱金鑰(或授權權杖)和地區名稱| 在應用的"設置"頁中,請確保正確輸入語音訂閱密鑰及其區域。<br>確保正確輸入語音鍵和關鍵區域。 |
|錯誤連接失敗:連接被遠端主機關閉。 錯誤代碼:1011。 錯誤詳細資訊:在送出訊息之前,我們無法連線到自動程式 | 請確保[選中「啟用流式處理終結點」](#register-the-direct-line-speech-channel)框和/或[將 Web**套接字**切換](#enable-web-sockets)為「打開」。<br>確保 Azure 應用服務正在運行。 如果是,請嘗試重新啟動應用服務。|
|錯誤連接失敗:連接被遠端主機關閉。 錯誤代碼:1011。 錯誤詳細資訊:回應狀態代碼不表示成功:500(內部伺服器錯誤)| 機器人在其輸出"活動[說話"](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)欄位中指定了神經語音,但與語音訂閱鍵關聯的 Azure 區域不支援神經語音。 請參考[標準與神經聲音](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)。|
|錯誤連接失敗:連接被遠端主機關閉。 錯誤代碼:1000。 錯誤詳細資訊:超過最大 Web so 接字連接空閒持續時間(> 300000 ms)| 當與通道的連接保持打開且非活動狀態超過五分鐘時,這是一個預期錯誤。 |

如果您的問題未在表中得到解決,請參閱[語音助理:常見問題](faq-voice-assistants.md)。

### <a name="view-bot-activities"></a>檢視機器人活動

每個自動程式發送和接收**活動**訊息。 在 Windows 語音助理用戶端**的活動日誌**視窗中,您將看到用戶端從自動程式收到的每個活動的時間戳日誌。 您還可以查看用戶端使用[`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync)方法 發送到自動程式的活動。 當您選擇日誌項時,它將以 JSON 的形式顯示關聯活動的詳細資訊。

下面是用戶端收到的活動的範例 json:

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

要瞭解有關 JSON 輸出中傳回的內容的更多內容,請參閱[活動中的欄位](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)。 為了本教程的目的,您可以專注於["文本](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text)"和["說話"](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)欄位。

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>檢視對語音 SDK 的呼叫的客戶端碼

Windows 語音助理用戶端使用 NuGet 包[Microsoft.認知服務.語音](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/),其中包含語音 SDK。 開始檢視範例代碼的一個好地方是檔案中[`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)的方法 InitIt 語音連接器(),它建立了以下兩個語音 SDK 物件:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)- 對於設定設定 (例如,語音訂閱金鑰、關鍵區域)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)- 管理通道連接和用戶端訂閱事件,以處理識別的語音和自動程式回應。

## <a name="add-custom-keyword-activation"></a>新增自訂關鍵字啟動

語音 SDK 支援自定義關鍵字啟動。 與 Microsoft 助手的「Hey Cortana」類似,您可以編寫一個應用,該應用將持續偵聽您選擇的關鍵字。 請記住,關鍵字可以是單個單詞或多字短語。

> [!NOTE]
> 術語*關鍵字*通常與術語*喚醒詞*互換使用,您可能會看到 Microsoft 文檔中都使用。

關鍵字檢測在用戶端應用上完成。 如果使用關鍵字,則僅在檢測到關鍵字時才會將音訊流式傳輸到"直線語音"通道。 直線語音通道包括一個稱為*關鍵字驗證 (KWV) 的*元件,該元件在雲端中執行更複雜的處理,以驗證您選擇的關鍵字是否位於音訊串流的開頭。 如果關鍵詞驗證成功,則通道將與機器人通信。

按照以下步驟創建關鍵字模型,將 Windows 語音助理用戶端配置為使用此模型,最後,使用自動程式對其進行測試。

1. 以這些說明[使用語音服務建立自訂關鍵字](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)。
2. 解壓縮您在上一步中下載的模型檔。 它應該為您的關鍵字命名。 您要尋找名為的檔案`kws.table`。
3. 在 Windows 語音助理用戶端中,找到 **「設定」** 功能表(尋找右上角的齒輪圖示)。 找到**模型檔路徑**,然後從步驟`kws.table`2 輸入 檔的完整路徑名稱。
4. 請確保選中標記為「**已啟用」 的**框。 您應該在複選框旁邊看到此消息:"將在下一次連接時偵聽關鍵字" 如果提供了錯誤的檔或無效的路徑,則應看到一條錯誤消息。
5. 輸入語音**訂閱鍵**,**訂閱鍵區域**,然後按下 **「確定**」 以關閉 **「設定」** 選單。
6. 按下 **「重新連接**」。 您應該會看到一條消息,內容是:「開始新的對話 - 鍵入、按下麥克風按鈕或說出關鍵字」。。 應用程式現在不斷偵聽。
7. 說出以關鍵字開頭的任何短語。 例如 **:"[你的關鍵字],** 它是什麼時間? 說出關鍵字后,無需暫停。 完成後,會發生兩件事:
   * 你會看到你說話的抄錄
   * 不久之後,您將聽到機器人的回應
8. 繼續試著自動程式支援的三種輸入類型:
   * 底部列中鍵入文字
   * 按下麥克風圖示並講話
   * 說出以關鍵字開頭的短語

### <a name="view-the-source-code-that-enables-keyword"></a>檢視啟用關鍵字的源碼

在 Windows 語音助理用戶端源碼中,檢視這些檔案以檢視用於啟用關鍵字偵測的代碼:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)包括對語音 SDK[`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)方法的調用,該方法用於從磁碟上的本地檔實例化模型。
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)包括對語音 SDK[`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)方法的調用,該方法可啟動連續關鍵字檢測。

## <a name="optional-change-the-language-and-bot-voice"></a>( 選擇性的 )變更語言與自動聲音

您建立的自動程式將用英語收聽和回應,預設使用美國英語文本到語音。 但是,您不限於使用英語或默認語音。 在本節中,您將學習如何更改自動程式將偵聽和回應的語言。 您還將學習如何為該語言選擇其他語音。

### <a name="change-the-language"></a>變更語言

可以從[語音到文本](language-support.md#speech-to-text)表中提及的任何一種語言中進行選擇。 在下面的示例中,我們將將語言更改為德語。

1. 打開 Windows 語音助理用戶端應用,按一下設定按鈕(右上齒輪圖示),並在`de-de`「語言」 欄位中輸入(這是[語音到文字](language-support.md#speech-to-text)表中提及的「區域設置」值)。 這會設定要辨識的口語,覆蓋預設值`en-us`。 這還指示直線語音通道使用預設的德語語音進行自動回復。
2. 關閉設置頁面,然後按兩下「重新連接」按鈕以建立與回聲自動程式的新連接。
3. 按下麥克風按鈕,用德語說一個短語。 您將看到識別的文本和回顯自動程式以預設的德語語音進行回復。

### <a name="change-the-default-bot-voice"></a>變更預設自動程式語音

如果 Bot 以[語音合成標記語言](speech-synthesis-markup.md)(SSML) 而不是簡單文本的形式指定回復,則可以選擇文本到語音語音和控制發音。 回聲機器人不使用 SSML,但我們可以輕鬆地修改代碼來執行此操作。 在下面的示例中,我們將 SSML 添加到回聲自動回應中,以便使用德國語音 Stefan Apollo(男性語音)而不是預設的女性語音。 請參閱支援您的語言[的標準語音](language-support.md#standard-voices)[和神經語音](language-support.md#neural-voices)清單。

1. 讓我們從打開`samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`開始。
2. 找到這兩行:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. 將它們取代為:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. 在可視化工作室中構建解決方案並修復任何生成錯誤。

方法「MessageFactory.Text」中的第二個參數設定自動程式答覆中的[「活動說話」 欄位](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)。 通過上述更改,它已被替換為從簡單文本到 SSML,以便指定非預設的德語語音。

### <a name="redeploy-your-bot"></a>重新部署自動程式

現在,您對自動程式進行了必要的更改,下一步是將其重新發佈到 Azure 應用服務並嘗試:

1. 在「解決方案資源管理器」視窗中,右鍵單擊**EchoBot**專案並選擇 **「發布」。**
2. 以前的部署配置已載入為預設值。 只需按下**Publish****EchoBot20190805125647 旁邊發布 - Web 部署**。
3. **"發佈成功'** 訊息將顯示在 Visual Studio 輸出視窗中,並且將啟動一個網頁,其中將顯示"您的自動程式已準備就緒!
4. 打開 Windows 語音助理用戶端應用,按一下設置按鈕(右上角的齒輪圖示),並確保`de-de`您仍然位於 「語言」欄位中。
5. 按照[「構建 Windows 語音助理用戶端](#build-the-windows-voice-assistant-client)」中的說明重新連接到新部署的自動程式,使用新語言說話,並聽到使用新語音用該語言進行自動回復。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用本教程中部署的回聲機器人,可以通過刪除 Azure 資源組**語音EchoBotTutorial-ResourceGroup**來刪除它及其所有相關的 Azure 資源。

1. 從[Azure 門戶](https://portal.azure.com)中,按下 Azure**服務**瀏覽中的**資源群組**。
2. 尋找名為「**語音回聲機器人教程-資源群組」 的資源群組**。 單擊三個點 (...)。
3. 選取 [刪除資源群組]****。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用語音 SDK 建立您自己的客戶端應用](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>另請參閱

* 部署到[您附近的 Azure 區域](https://azure.microsoft.com/global-infrastructure/locations/)以檢視自動程式回應時間改進
* 部署到[支援高品質神經 TTS 語音的 Azure 區域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* 與直線語音通道關聯的定價:
  * [Bot 服務定價](https://azure.microsoft.com/pricing/details/bot-service/)
  * [語音服務](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 建構與部署自己的啟用語音的機器人:
  * 建[構機器人框架機器人](https://dev.botframework.com/)。 使用[直線語音頻道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)註冊,並[自訂您的自動操作語音](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * 探索現有的[機器人框架解決方案](https://microsoft.github.io/botframework-solutions/index):建[構虛擬助手](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/)[並將其延伸到直線語音](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
