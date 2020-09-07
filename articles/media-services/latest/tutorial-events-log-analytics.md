---
title: 將 Azure 媒體服務事件儲存在 Azure Log Analytics 中
titleSuffix: Azure Media Services
description: 了解如何將 Azure 媒體服務事件儲存在 Azure Log Analytics 中。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: inhenkel
ms.openlocfilehash: dfd7b3dcc7e27dd1251c35c6272b5aa7f8036006
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88941691"
---
# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>教學課程：將 Azure 媒體服務事件儲存在 Azure Log Analytics 中

## <a name="azure-media-services-events"></a>Azure 媒體服務事件

Azure 媒體服務 v3 會在 [Azure 事件方格](media-services-event-schemas.md)上發出事件。 您可以透過許多方式來訂閱事件，並儲存在資料存放區中。 在本教學課程中，您將使用[記錄應用程式流程](https://azure.microsoft.com/services/logic-apps/)來訂閱媒體服務事件。 系統會針對每個事件觸發邏輯應用程式，並將事件的主體儲存在 Azure Log Analytics 中。 當事件位於 Azure Log Analytics 時，您就可以使用其他 Azure 服務來建立儀表板、監視和針對這些事件發出警示，但我們不會在本教學課程中說明這一點。

> [!NOTE]
> 如果您已經熟悉使用 FFmpeg 作為內部部署編碼器，這會很有幫助。  如果不熟悉也沒關係。 以下包含串流影片的命令列和指示。

您將了解如何：

> [!div class="checklist"]
> * 建立無程式碼邏輯應用程式流程
> * 訂閱 Azure 媒體服務事件主題
> * 剖析事件並儲存至 Azure Log Analytics
> * 從 Azure Log Analytics 查詢事件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

> * [Azure 訂用帳戶](how-to-set-azure-subscription.md)
> * [媒體服務](create-account-howto.md)帳戶和資源群組。
> * 適用於您作業系統的 [FFmpeg](https://ffmpeg.org/download.html) 安裝。
> * [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 工作區

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>使用邏輯應用程式訂閱媒體服務事件

1. 在 Azure 入口網站中建立 [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 工作區 (如果尚未建立)。 您需要工作區識別碼和其中一個金鑰，因此請將該瀏覽器視窗保持開啟。 然後，在另一個索引標籤或視窗中開啟入口網站。

1. 瀏覽至您的 Azure 媒體服務帳戶，然後選取 [事件]。 這會顯示訂閱 Azure 媒體服務事件的所有方法。
    > [!div class="mx-imgBorder"]
    > ![Azure 媒體服務入口網站](media/tutorial-events-log-analytics/select-events-01a.png)

1. 選取**Logic Apps 圖示**以建立邏輯應用程式。 這會開啟邏輯應用程式設計工具，您可以在其中建立擷取事件並推送至 Log Analytics 的流程。 
    > [!div class="mx-imgBorder"]
    > ![建立邏輯應用程式](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. 選取 **+ 圖示**，選取您要使用的租用戶，然後選取 [登入]。 您會看到 Microsoft 登入提示字元。
    > [!div class="mx-imgBorder"]
    > ![連線到 Azure 事件方格](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
![選取租用戶](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. 選取 [繼續] 以訂閱媒體服務事件。
    > [!div class="mx-imgBorder"]
    > ![已連線到 Azure 事件方格](media/tutorial-events-log-analytics/select-continue-04.png)

1. 在 [資源類型] 清單中尋找 "Microsoft.Media.MediaServices"。
    > [!div class="mx-imgBorder"]
    >![Azure 媒體服務資源群組事件](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. 選取要**事件類型項目**。 系統會列出 Azure 媒體服務發出的所有事件。 您可以選取想要追蹤的事件。您可以在這裡新增多個事件類型。 (稍後，您將對邏輯應用程式流程進行小幅變更，以將每個事件種類儲存在不同的 Log Analytics 記錄中，並以動態方式將事件類型名稱傳播到 Log Analytics 記錄名稱。)
    > [!div class="mx-imgBorder"]
    > ![Azure 媒體服務事件類型](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. 選取 [另存新檔]。

1. 為邏輯應用程式命名。  預設會選取資源群組。 保留其他設定內容，然後選取 [建立]。  系統會帶您會返回主畫面。
    > [!div class="mx-imgBorder"]
    > ![邏輯應用程式命名介面](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>建立動作

既然您已訂閱事件，請建立一個動作。

1. 如果入口網站已將您帶回主畫面，請在 [所有資源] 中搜尋應用程式名稱，以回到您剛建立的邏輯應用程式。

1. 選取您的應用程式，然後選取 [邏輯應用程式設計工具]。 設計工具窗格隨即開啟。

1. 選取 [+ 新增步驟] 。

1. 由於要將事件推送至 Azure Log Analytics 服務，因此請搜尋 "Azure Log Analytics"，然後選取 [Azure Log Analytics 資料收集器]。
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics 資料收集器](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. 若要連線到 Log Analytics 工作區，您需要工作區識別碼和代理程式金鑰。 在新的索引標籤或視窗中開啟 Azure 入口網站，瀏覽至您在本教學課程開始之前所建立的 Log Analytics 工作區。
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics 工作區識別碼](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. 在左側功能表上，找出 [代理程式管理] 並選取。 如此會顯示已產生的代理程式金鑰。
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics 代理程式管理](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. 複製*工作區識別碼*。
    > [!div class="mx-imgBorder"]
    > ![複製工作區識別碼](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. 在其他瀏覽器索引標籤或視窗的 Azure Log Analytics 資料收集器下，選取 [傳送資料]，為您的連線命名，然後在 [工作區識別碼] 欄位中貼上*工作區識別碼*。

1. 返回工作區瀏覽器索引標籤或視窗，並複製「工作區金鑰」。
    > [!div class="mx-imgBorder"]
    > ![代理程式管理主要金鑰](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. 在其他瀏覽器索引標籤或視窗中，將「工作區金鑰」貼入 **工作區金鑰**欄位。

1. 選取 [建立]  。 現在您將建立 JSON 要求本文和自訂記錄名稱。

1. 選取 [JSON 要求本文] 欄位。  隨即會出現**新增動態內容**的連結。

1. 選取 [新增動態內容]，然後選取 [主題]。

1. 針對**自訂記錄名稱**執行相同的動作。
    > [!div class="mx-imgBorder"]
    > ![已選取主題](media/tutorial-events-log-analytics/topic-selected.png)

1. 選取邏輯應用程式的**程式碼檢視**。 尋找輸入和記錄類型行。
    > [!div class="mx-imgBorder"]
    > ![兩行的程式碼檢視](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. 將 `body` 值從 `"@triggerBody()?['topic']"` 變更為 `"@{triggerBody()}"`。 這可將整個訊息剖析至 Log Analytics。

1. 將 `Log-Type` 從 `"@triggerBody()?['topic']"` 變更為 `"@replace(triggerBody()?['eventType'],'.','')"`。 (這會取代 "."，因為 Log Analytics 記錄名稱中不允許這種情況。)
    > [!div class="mx-imgBorder"]
    > ![變更後的邏輯應用程式 JSON](media/tutorial-events-log-analytics/changed-lines.png)

1. 選取 [儲存]。

1. 若要確認，請選取 [邏輯應用程式設計工具]。
    > [!div class="mx-imgBorder"]
    > ![確認主體和函式步驟](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. 當您檢查資源群組中的所有資源時，會列出一個邏輯應用程式和兩個邏輯應用程式 API 連接器，一個用於事件，另一個則用於 Log Analytics。 如需事件方格系統主題的詳細資訊，請參閱[事件方格系統主題](https://docs.microsoft.com/azure/event-grid/system-topics)。
    > [!div class="mx-imgBorder"]
    > ![參閱資源群組中的所有新資源](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>測試

若要測試其實際運作方式，請在 Azure 媒體服務中建立即時活動。 建立 RTMP 即時事件，並使用 ffmpeg 根據 .mp4 範例檔案來推送「即時」串流。 建立事件之後，取得 RTMP 內嵌 URL。

1. 從您的媒體服務帳戶中，選取 [即時串流]。
    > [!div class="mx-imgBorder"]
    > ![建立 Azure 媒體服務即時活動](media/tutorial-events-log-analytics/live-event.png)

1. 選取 [新增即時活動]。

1. 在 [即時活動名稱] 欄位中輸入名稱。 (**描述**欄位為可選。)

1. 選取 [標準] 雲端編碼。

1. 針對編碼預設，選取 [預設 720p] 。

1. 選取 [RTMP] 輸入通訊協定。

1. 針對持續性輸入 URL 選取 [是]。

1. 選取 [是]，以在事件建立時啟動事件。

    > [!WARNING]
    > 如果您選取是，就會開始計費。  如果要在開始使用 FFmpeg 進行串流處理「之前」才開始串流，請選取 [否]，然後記得啟動您的即時活動。

    > [!div class="mx-imgBorder"]
    > ![即時活動設定](media/tutorial-events-log-analytics/live-event-settings.png)

1. 選取 [我擁有使用內容/檔案的權限...] 核取方塊。

1. 選取 [檢閱 + 建立]。

1. 檢閱您的設定，然後選取 [建立]。  即時活動清單隨即出現，並會顯示即時活動內嵌 URL。

1. 將**內嵌 URL**複製到剪貼簿。

1. 選取清單中的 [即時活動] 以查看製作者檢視。

### <a name="stream-with-ffmpeg-cli"></a>使用 FFmpeg CLI 進行串流

1. 使用下列命令列。

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. 變更 `<ingestURL>` 以內嵌複製到剪貼簿的 URL。
1. 將 `<localpathtovideo>` 變更為想要從 FFmpeg 串流的檔案本機路徑。
1. 在結尾處新增唯一的名稱，例如 `mystream`。
1. 調整命令列，以反映您的測試來源檔案和任何其他系統變數。
1. 執行命令。 幾秒鐘後，「製作者檢視」播放機應該就會開始串流。 (如果影片未自動顯示，請重新整理播放機。)

    > [!div class="mx-imgBorder"]
    > ![確認已在製作者預覽播放機中內嵌適當影片](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>驗證事件

使用即時串流時，Azure 媒體服務會發出各種觸發邏輯應用程式流程的事件。 若要確認，請瀏覽至邏輯應用程式，並判斷媒體服務的事件是否會引發任何觸發程序。

1. 瀏覽至邏輯應用程式的概觀頁面，您應該會看到「執行歷程記錄」列出已成功完成的作業。
    > [!div class="mx-imgBorder"]
    > ![確認邏輯應用程式中的作業執行成功](media/tutorial-events-log-analytics/run-history.png)

1. 選取成功的作業。 在執行階段期間，會顯示作業的詳細資料。
1. 選取 [傳送資料] 加以展開。 在此情況下，`MicrosoftMediaLiveEventEncoderConnected` 事件會顯示系統已加以擷取，並顯示已剖析的主體。 這是推送至 Azure Log Analytics 工作區的內容。
    > [!div class="mx-imgBorder"]
    > ![查看傳送資料](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>驗證記錄

1. 瀏覽至您稍早建立的 Log Analytics 工作區。

1. 選取 [記錄]  。
1. 關閉範例查詢快顯視窗。
1. 隨即出現自訂記錄清單。 選取向下箭號加以展開。 您會看到事件名稱 `MicrosoftMediaLiveEventEncoderConnected`。
1. 選取事件名稱加以展開。
1. 選取「眼睛」圖示時，會顯示查詢結果的預覽。
1. 選取 [在查詢編輯器中查看]，然後選取 **TimeGenerated UTC** 清單下的項目，加以展開並檢視原始資料。

![查看 Log Analytics 中的詳細事件輸出](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>刪除資源

如果您不想繼續使用在本教學課程中建立的資源，請務必刪除資源群組中的所有資源，否則我們會繼續向您收取費用。

## <a name="next-steps"></a>後續步驟

您可以建立不同的查詢並加以儲存。 這些可以新增至 [Azure 儀表板](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)。
