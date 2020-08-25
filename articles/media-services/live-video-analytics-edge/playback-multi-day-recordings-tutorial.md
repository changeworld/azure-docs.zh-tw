---
title: 播放多日錄製 - Azure
description: 在本教學課程中，您將了解如何使用 Azure 媒體服務 API 來播放多日的持續影片錄製。
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 19a42c61ef250fecce4503d3aed70b36d71ee3cf
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649086"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>教學課程：播放多日錄製  

本教學課程的建置基礎為[連續影片錄製](continuous-video-recording-concept.md) (CVR) 教學課程。 在本教學課程中，您將了解如何使用 Azure 媒體服務 API 從雲端播放多日的持續影片錄製。 

這適用於公共安全之類的案例，其中需要維護攝影機多日 (或數週) 的畫面錄製，且偶爾需要監看該畫面的特定部分。

本教學課程說明如何：

> [!div class="checklist"]
> * 執行範例應用程式，以示範您可以如何瀏覽多日錄製的內容
> * 檢視該錄製的選取部分

## <a name="suggested-pre-reading"></a>建議的預先閱讀  

建議您閱讀下列文件頁面：

* [IoT Edge 上的 Live Video Analytics 概觀](overview.md)
* [IoT Edge 上的 Live Video Analytics 術語](terminology.md)
* [媒體圖表概念](media-graph-concept.md)
* [連續影片錄製](continuous-video-recording-concept.md) 
* [操作指南：播放錄製](playback-recordings-how-to.md)
* [教學課程：連續影片錄製](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>必要條件

* 完成 [CVR 教學課程](continuous-video-recording-tutorial.md)。 雖然本教學課程和[教學課程：連續影片錄製](continuous-video-recording-tutorial.md)中討論的相關 API 適用於 5 分鐘或更長的錄製，但建議您錄製 5 小時的影片 (如果不是更長)。 用來瀏覽錄製的 API 最適合透過長時間錄製來進行示範。
* 我們建議您在[教學課程：連續影片錄製](continuous-video-recording-tutorial.md)仍在執行 (也就是您仍在將影片錄製到雲端) 時，執行本教學課程。

## <a name="run-the-sample"></a>執行範例 

做為 [CVR 教學課程](continuous-video-recording-tutorial.md)的一部分，您可能已建立媒體服務帳戶。 在本教學課程中，您將需要具備該帳戶的完整 API 存取權。 您可以使用[取得存取媒體服務 API 的認證](../latest/access-api-howto.md?tabs=portal)中的步驟來建立服務主體。 您應該能夠從 Azure 入口網站取得 JSON 區塊，如下所示：

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

接下來，在 Visual Studio 程式碼中，開啟 src/ams-asset-player。 此資料夾包含本教學課程所需的檔案。 開啟 appsettings.json 檔案，並將其內容複製到新的檔案 (appsettings.Developer.json) 中。 對後者檔案進行下列編輯：

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

在 Visual Studio Code 中，您可以按一下左側的 [執行] 圖示 (或 Ctrl+Shift+D)，以顯示可用的應用程式來執行：

![執行](./media/playback-multi-day-recordings-tutorial/run.png)
 
從下拉式方塊中選取 [AMS 資產播放器] 應用程式 (如下所示)，然後按 F5 開始偵錯。

![偵錯](./media/playback-multi-day-recordings-tutorial/debug.png)

範例應用程式會建置並啟動您的預設瀏覽器應用程式，並開啟 [AMS 資產播放器] 頁面。

> [!NOTE]
> 視瀏覽器上的安全性設定而定，您可能會看到一則警告訊息。 由於網頁是在本機執行，因此您可以選擇忽略警告。

AMS 資產播放器會提示您輸入媒體服務資產的名稱。 在[教學課程：連續影片錄製](continuous-video-recording-tutorial.md)中，您應該使用用來錄製影片的資產名稱。

在輸入資產名稱並按下 [提交] 時，播放器程式碼將會載入串流 URL。 如需詳細資訊，請參閱 [操作指南：播放錄製](playback-recordings-how-to.md)。 如果您如建議仍在錄製資產，則播放器會偵測到此情況，並嘗試提示播放到錄製影片的最新部分。 您可以看到播放器左上方的時間戳記 (UTC)。 在下面的螢幕擷取畫面中，請注意如何選取 [即時] 按鈕。

![STREAM](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
在播放器右側，您可以看到可瀏覽封存的控制項。 此控制項中的年份、月份和日期會使用[操作指南：播放錄製](playback-recordings-how-to.md)中的 availableMedia API 文件來填入。
當您展開日期時，如果您已讓 CVR 教學課程執行了數小時，就會看到如下的結果：

![瀏覽封存](./media/playback-multi-day-recordings-tutorial/results.png)

教學課程中的影片摘要來源是 MKV 檔案。 當 RSTP 模擬器 (請參閱 [Live555 媒體伺服器](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) 到達檔案結尾時，其會結束串流。 媒體圖表中的 RTSP 來源節點會偵測到此情況，並重新建立連線及繼續影片。 在每個這類檔案結尾和重新連線之間，錄製的封存中會有一個間距，這會在 availableMedia 結果中顯示為新的項目。

![結果](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
當您按一下清單中的任何一個項目時，應用程式會使用適當的篩選器來建立串流 URL，例如 https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS)。 播放器會載入此 URL，且將於所需的 startTime 開始播放。

或者，您也可以透過頁面底部的控制項來使用特定的開始和結束時間。 您可以使用右側所列的 availableMedia 呼叫結果，做為開始和結束時間允許值的指南。 StartTime 和 endTime 篩選器的詳細限制記載於[操作指南：播放錄製](playback-recordings-how-to.md)。 選取時間值之後，按一下 [提交] 時，應用程式將會載入具有如下串流 URL 的播放器： https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS,endTime= YYYY-MM-DDTHH:MM:SS) 將會在所需的 startTime 開始播放。

## <a name="next-steps"></a>後續步驟

[以事件為基礎錄製到雲端並從雲端播放的影片](event-based-video-recording-tutorial.md)
