---
title: 影片索引器連接器與邏輯應用程式和 Power Automate 教學課程。
description: 本教學課程示範如何使用邏輯應用程式和 Power Automate，帶來新體驗和創造影片索引器連接器的營收商機。
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f557794265f3bbf48fae97fc04e5e9b068b54f63
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540399"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>教學課程：使用影片索引器與邏輯應用程式和 Power Automate

Azure 媒體服務[影片索引器 v2 REST API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) 支援伺服器對伺服器和用戶端對伺服器的通訊，讓影片索引器使用者可以輕鬆地將影片和音訊見解整合到其應用程式邏輯中，進而帶來新的體驗並創造營收商機。

為了讓整合變得更容易，我們支援與 API 相容的  [Logic Apps](https://azure.microsoft.com/services/logic-apps/)  和  [Power Autmate](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)  連接器。 您可以使用連接器來設定自訂工作流程，以有效地從大量的影片和音訊檔案編制索引並擷取深入解析，而不需要撰寫任何一行程式碼。 此外，使用連接器進行整合，可更清楚地了解工作流程的健康情況，並輕鬆地進行偵錯。  

為了協助您快速開始使用影片索引器連接器，我們將逐步解說範例邏輯應用程式和 Power Automate 解決方案，您可以跟著設定。 本教學課程說明如何使用 Logic Apps 設定流程。 不過，這兩個解決方案中的編輯器和功能幾乎都相同，因此圖表和說明同時適用於 Logic Apps 和 Power Automate。

本教學課程涵蓋的「自動上傳和編製影片索引」案例，是由共同運作的兩個不同流程所組成。 
* 在 Azure 儲存體帳戶中新增或修改 Blob 時，會觸發第一個流程。 其會將新檔案上傳至具有回撥 URL 的影片索引器，以在索引作業完成後傳送通知。 
* 第二個流程會根據回撥 URL 來觸發，並將已擷取的深入解析儲存回 Azure 儲存體中的 JSON 檔案。 這兩種流程方法是用來支援非同步上傳和有效率地為較大的檔案編製索引。 

本教學課程使用邏輯應用程式來示範如何：

> [!div class="checklist"]
> * 設定檔案上傳流程
> * 設定 JSON 擷取流程

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

* 一開始，您需要影片索引器帳戶，並[透過 API 金鑰存取 API](video-indexer-use-apis.md)。 
* 您還需要一個 Azure 儲存體帳戶。 請記下儲存體帳戶使用的存取金鑰。 建立兩個容器 - 一個用來儲存影片，另一個用來儲存影片索引器所產生的深入解析。  
* 接下來，您必須在 Logic Apps 或 Power Automate 上開啟兩個不同的流程 (視您使用哪種方式)。 

## <a name="set-up-the-first-flow---file-upload"></a>設定第一個流程 - 檔案上傳   

每當在 Azure 儲存體容器中新增 Blob 時，就會觸發第一個流程。 一旦觸發，就會建立 SAS URI，您可以用來上傳影片索引器中的影片並為其編制索引。 在本節中，您會建立下列流程。 

![檔案上傳流程](./media/logic-apps-connector-tutorial/file-upload-flow.png)

若要設定第一個流程，您必須提供影片索引器 API 金鑰，以及 Azure 儲存體認證。 

![Azure Blob 儲存體](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![連線名稱和 API 金鑰](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> 如果您先前已將 Azure 儲存體帳戶或影片索引子帳戶連線至邏輯應用程式，則您的連線詳細資料已儲存，因此會自動連線。 <br/>您可以按一下 Azure 儲存體 ([儲存體] 視窗) 或影片索引子 ([播放程式] 視窗) 動作底部的 [變更連線]，來編輯連線。

一旦可以連線到您的 Azure 儲存體和影片索引器帳戶，請在 **Logic Apps 設計工具**中尋找並選擇「當新增或修改 Blob 時」觸發程序。

選取您將放置影片檔案的容器。 

![螢幕擷取畫面顯示「當新增或修改 Blob 時」對話方塊，您可以在其中選取容器。](./media/logic-apps-connector-tutorial/container.png)

接下來，尋找並選取 [依路徑建立 SAS URI] 動作。 在動作對話方塊中，從動態內容選項中選取檔案路徑清單。  

此外，請新增新的「共用存取通訊協定」參數。 選擇 HttpsOnly 作為參數值。

![依路徑的 SAS URI](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

填寫[您的帳戶位置](regions.md)和[帳戶識別碼](./video-indexer-use-apis.md#account-id) ，以取得影片索引器帳戶權杖。

![取得帳戶存取權杖](./media/logic-apps-connector-tutorial/account-access-token.png)

針對「上傳影片和編製索引」，填寫必要的參數和影片 URL。 選取「新增參數」，然後選取「回撥 URL」。 

![上傳並編製索引](./media/logic-apps-connector-tutorial/upload-and-index.png)

現在，將回撥 URL 保留空白。 只有在完成建立回撥 URL 的第二個流程之後，才會加以新增。 

您可以使用其他參數的預設值，或根據需求加以設定。 

按一下 [儲存]，並繼續設定第二個流程，以便在上傳和編製索引完成後擷取深入解析。 

## <a name="set-up-the-second-flow---json-extraction"></a>設定第二個流程 - JSON 擷取  

完成從第一個流程上傳和編製索引的作業，將會傳送具有正確回撥 URL 的 HTTP 要求，以觸發第二個流程。 然後，流程會取出影片索引器所產生的深入解析。 在此範例中，其會將編製索引工作的輸出儲存在您的 Azure 儲存體中。  不過，您可以使用輸出來完成此工作。  

建立與第一個流程分開的第二個流程。 

![JSON 擷取流程](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

若要設定第一個流程，您必須再次提供影片索引器 API 金鑰，以及 Azure 儲存體認證。 您需要更新在第一個流程中相同的參數。 

針對觸發程式，您會看到 HTTP POST URL 欄位。 在儲存流程後才會產生 URL。不過，您最後會需要此 URL。 我們稍後會再回到這裡。 

填寫[您的帳戶位置](regions.md)和[帳戶識別碼](./video-indexer-use-apis.md#account-id) ，以取得影片索引器帳戶權杖。  

移至「取得影片索引」動作，並填寫必要的參數。 針對影片識別碼，置入下列運算式：triggerOutputs()['queries']['id'] 

![影片索引器動作資訊](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

此運算式會告知連接器從觸發程式的輸出取得影片識別碼。 在此情況下，觸發程式的輸出會是您第一個觸發程式中「上傳影片和編製索引」的輸出。 

移至「建立 Blob」動作，並選取您要在其中儲存深入解析之資料夾的路徑。 設定您要建立的 Blob 名稱。 針對 Blob 內容，置入下列運算式：body(‘Get_Video_Index’) 

![建立 Blob 動作](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

此運算式會從這個流程取得「取得影片索引」動作的輸出。 

按一下 [儲存流程]。 

儲存流程後，就會在觸發程式中建立 HTTP POST URL。 複製觸發程式中的 URL。 

![儲存 URL 觸發程序](./media/logic-apps-connector-tutorial/save-url-trigger.png)

現在，返回第一個流程，並在回撥 URL 參數的「上傳影片和編製索引」動作中貼上 URL。 

請確定這兩個流程都已儲存，表示您已經準備好了！ 

將影片新增至您的 Azure blob 容器，並在幾分鐘後返回，以查看深入解析是否出現在目的資料夾中，用這種方法試用新建立的邏輯應用程式或 Power Automate 解決方案。 

## <a name="generate-captions"></a>產生字幕

如需說明[如何使用影片索引子和 Logic Apps 產生標題](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198)的步驟，請參閱下列部落格。 

本文也說明如何將影片複製到 OneDrive 以自動編製影片索引，以及如何將影片索引子所產生的字幕儲存在 OneDrive 中。
 
## <a name="clean-up-resources"></a>清除資源

完成本教學課程之後，您可以隨時保留此邏輯應用程式或 Power Automate 解決方案，並在需要時執行。 不過，如果您不想繼續執行，而且不希望支付費用，請在使用 Power Automate 時關閉這兩個流程。 使用 Logic Apps 時，請停用這兩個流程。 

## <a name="next-steps"></a>後續步驟

本教學課程只示範一個影片索引器連接器範例。 您可以使用影片索引器連接器來進行影片索引器所提供的任何 API 呼叫。 例如：上傳和擷取深入解析、轉譯結果、取得可內嵌的小工具，甚至自訂您的模型。 此外，您也可以選擇根據不同的來源 (例如，檔案存放庫的更新或傳送電子郵件) 來觸發這些動作。 然後，您可以選擇將結果依相關的基礎結構或應用程式更新，或產生任何數量的動作項目。  

> [!div class="nextstepaction"]
> [使用影片索引器 API](video-indexer-use-apis.md)
