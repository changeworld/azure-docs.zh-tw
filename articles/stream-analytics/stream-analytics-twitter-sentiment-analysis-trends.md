---
title: Azure 串流分析的即時 Twitter 情感分析
description: 本文說明如何使用串流分析來執行即時 Twitter 情感分析。 從事件產生到即時儀表板資料的逐步指導。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 4b265bb574895e4728ad93ee25c9dad0da226ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240290"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure 串流分析中的即時 Twitter 情感分析

本文將即時 Twitter 事件引入 Azure 事件中心，教您如何構建社交媒體情緒分析解決方案。 編寫 Azure 流分析查詢以分析資料並存儲結果以便以後使用，或創建[Power BI](https://powerbi.com/)儀表板以即時提供見解。

社交媒體分析工具可協助組織了解熱門話題。 熱門話題是社交媒體上大量帖子的主題和態度。 情緒分析，也稱為*意見挖掘*，使用社交媒體分析工具來確定對產品或想法的態度。 

即時 Twitter 趨勢分析是分析工具的一個很好的例子，因為井號標籤訂閱模型使您能夠收聽特定的關鍵字（hashtag）並開發源的情緒分析。

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>案例：即時的社交媒體情感分析

擁有新聞媒體網站的公司提供與讀者有直接關聯的網站內容，試圖打敗競爭對手。 公司透過對 Twitter 資料執行即時情感分析，來針對與讀者相關的話題使用社交媒體分析。

為了在 Twitter 上即時找出熱門話題，公司需要即時分析重要話題的推文數量和情感。

## <a name="prerequisites"></a>Prerequisites

在此"執行指南"中，您可以使用連接到 Twitter 的用戶端應用程式，並查找具有某些標籤（您可以設置）的推文。 要運行應用程式並使用 Azure 流分析分析推文，您必須具有以下內容：

* 如果沒有 Azure 訂閱，請創建[一個免費帳戶](https://azure.microsoft.com/free/)。

* [推特](https://twitter.com)帳戶。

* TwitterClientCore應用程式，它讀取推特飼料。 要獲得此應用程式，請下載[TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)。

* 安裝[.NET 核心 CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)版本 2.1.0。

## <a name="create-an-event-hub-for-streaming-input"></a>創建流式輸入的事件中心

此範例應用程式會產生事件，並將事件發送至 Azure 事件中樞。 Azure 事件中心是流分析的首選事件引入方法。 如需詳細資訊，請參閱 [Azure 事件中樞文件](../event-hubs/event-hubs-what-is-event-hubs.md)。

### <a name="create-an-event-hub-namespace-and-event-hub"></a>建立事件中樞命名空間和事件中樞
在本節中，您將創建一個事件中心命名空間，並將事件中心添加到該命名空間。 事件中樞命名空間可在邏輯上將相關的事件匯流排執行個體分組。 

1. 登錄到 Azure 門戶並選擇 **"創建資源**"。 然後。 搜索**事件中心**並選擇 **"創建**"。

2. 在 **"創建命名空間"** 頁上，輸入命名空間名稱。 可以為命名空間使用任何名稱，但名稱必須對 URL 有效，並且它必須在整個 Azure 中是唯一的。 
    
3. 選擇定價層和訂閱，然後創建或選擇資源組。 然後，選擇一個位置，然後選擇 **"創建**"。 
 
4. 命名空間完成部署後，導航到資源組，並在 Azure 資源清單中查找事件中心命名空間。 

5. 從新的命名空間中，選擇**+&nbsp;事件中心**。 

6. 命名新的事件中心*社交推特-eh*。 您可以使用不同的名稱。 如果這樣做，請記下來，因為稍後需要用到此名稱。 您不需要為事件中樞設定其他任何選項。
 
7. 選取 [建立]****。

### <a name="grant-access-to-the-event-hub"></a>授權存取事件中樞

在進程將資料發送到事件中心之前，事件中心需要允許訪問的策略。 存取原則會產生包含授權資訊的連接字串。

1.  在事件中心命名空間左側的巡覽列中，選擇位於 **"實體"** 部分**的事件中心**。 然後，選擇您剛剛創建的事件中心。

2.  在左側的巡覽列中，選擇位於 **"設置"** 下的**共用訪問策略**。

    >[!NOTE]
    >活動中心命名空間和事件中心下有共用訪問策略選項。 請確保在事件中心上下文中工作，而不是整個事件中心命名空間。

3.  在訪問策略頁中，選擇 **"添加**"。 然後輸入**策略名稱***的社交twitter訪問*，然後選中 **"管理**"核取方塊。
 
4.  選取 [建立]****。

5.  部署策略後，從共用訪問策略清單中選擇策略。

6.  查找標記為"**連接"字串主鍵的**框，然後選擇連接字串旁邊的複製按鈕。
 
7.  將連接字串貼到文字編輯器。 進行一些小編輯後，在下一節中需要此連接字串。

   連接字串如下所示：
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   請注意，連接字串包含多個機碼值組，以分號分隔：`Endpoint`、`SharedAccessKeyName`、`SharedAccessKey` 和 `EntityPath`。  

   > [!NOTE]
   > 基於安全考量，我們已移除範例中連接字串的某些部分。

## <a name="configure-and-start-the-twitter-client-application"></a>設定並啟動 Twitter 用戶端應用程式

用戶端應用程式會直接從 Twitter 取得推文事件。 為了這樣做，它需要權限來呼叫 Twitter 串流 API。 若要設定該權限，您需要在 Twitter 中建立應用程式，以產生唯一認證 (例如 OAuth 權杖)。 然後，您可以設定用戶端應用程式在呼叫 API 時使用這些認證。 

### <a name="create-a-twitter-application"></a>建立 Twitter 應用程式
如果您還沒有一個 Twitter 應用程式，您可以使用這個操作指南，您可以創建一個。 您必須已經有 Twitter 帳戶。

> [!NOTE]
> 在 Twitter 中建立應用程式及取得金鑰、秘密和權杖的確切流程可能不同。 如果這些指示不符合您在 Twitter 網站上看到的內容，請參閱 Twitter 開發人員文件。

1. 從 Web 瀏覽器轉到[Twitter 開發人員](https://developer.twitter.com/en/apps)，創建開發人員帳戶，然後選擇"**創建應用**"。 您可能會看到一則訊息，指出您必須申請 Twitter 開發人員帳戶。 請放心，在您的申請獲得批准後，您應該會看到一封確認電子郵件。 核准開發人員帳戶可能需要幾天的時間。

   ![Twitter 應用程式詳細資料](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter 應用程式詳細資料")

2. 在 [建立應用程式]**** 頁面上，提供新應用程式的詳細資料，然後選取 [建立 Twitter 應用程式]****。

   ![Twitter 應用程式詳細資料](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter 應用程式詳細資料")

3. 在應用程式頁面中，選取 [金鑰和權杖]**** 索引標籤，並複製 [取用者 API 金鑰]**** 和 [取用者 API 秘密金鑰]**** 的值。 此外，選取 [存取權杖和存取權杖密碼]**** 底下的 [建立]**** 來產生存取權杖。 複製 [存取權杖]**** 和 [存取權杖祕密]**** 的值。

   儲存您所擷取的 Twitter 應用程式值。 以後需要這些值。

> [!NOTE]
> Twitter 應用程式的金鑰和秘密可用來存取您的 Twitter 帳戶。 這項資訊要視為機密，就像看待您的 Twitter 密碼一樣。 例如，請勿將這項資訊內嵌在您提供給其他人的應用程式中。 

### <a name="configure-the-client-application"></a>設定用戶端應用程式

我們已建立用戶端應用程式，可利用 [Twitter 的串流 API](https://dev.twitter.com/streaming/overview) 來連線至 Twitter 資料，以收集一組特定話題相關的推文事件。

在應用程式執行之前，您需要提供一些資訊，例如 Twitter 金鑰和事件中樞連接字串。

1. 請確保您已下載了在先決條件中列出的[TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)應用程式。

2. 使用文字編輯器打開*App.config*檔。 對`<appSettings>`元素進行以下更改：

   * 設置為`oauth_consumer_key`Twitter 消費者金鑰（API 金鑰）。 
   * 設置為`oauth_consumer_secret`Twitter 消費者金鑰（API 金鑰）。
   * 設置為`oauth_token`Twitter 訪問權杖。
   * 設置為`oauth_token_secret`Twitter 訪問權杖金鑰。
   * 將 `EventHubNameConnectionString` 設為連接字串。
   * 設置為`EventHubName`事件中心名稱（即實體路徑的值）。

3. 打開命令列並導航到 TwitterClientCore 應用所在的目錄。 使用 命令`dotnet build`生成專案。 然後使用 命令`dotnet run`運行應用。 應用會將推文發送到事件中心。

## <a name="create-a-stream-analytics-job"></a>建立串流分析作業

既然正在從 Twitter 即時串流推文事件，您可以設定串流分析作業來即時分析這些事件。

1. 在 Azure 門戶中，導航到資源組並選擇 **" 添加**"。 然後搜索**流分析作業**並選擇 **"創建**"。

2. 將作業命名為 `socialtwitter-sa-job`，並指定訂用帳戶、資源群組和位置。

    最好將作業和事件中樞放在相同的區域，以達到最佳效能，在區域之間傳送資料也不需要付費。

3. 選取 [建立]****。 然後在部署完成後導航到作業。

## <a name="specify-the-job-input"></a>指定工作輸入

1. 在"流分析"作業中，從**作業拓撲**下的左側功能表中選擇 **"輸入**"。

2. ** +選擇&nbsp;"添加流輸入** > **事件中心**"。 填寫 **"新建輸入**"表單，並填寫以下資訊：

   |**設定**  |**建議的值**  |**描述**  |
   |---------|---------|---------|
   |輸入別名| *推特流* | 輸入輸入的別名。 |
   |訂用帳戶  | \<您的訂用帳戶\> |  選取您要使用的 Azure 訂用帳戶。 |
   |事件中樞命名空間 | *asa-twitter-事件hub* |
   |事件中樞名稱 | *社交推特-eh* | 選擇 *"使用現有*"。 然後選擇您創建的事件中心。|
   |事件壓縮類型| GZip | 資料壓縮類型。|

   保留剩餘的預設值，然後選擇 **"保存**"。

## <a name="specify-the-job-query"></a>指定作業查詢

串流分析支援說明轉換的簡單、宣告式查詢模型。 若要深入了解語言，請參閱 [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)。 此指南可説明您通過 Twitter 資料創作和測試多個查詢。

為了比較提及不同話題的次數，您可以使用[輪轉視窗](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics)，依話題每隔五秒取得一次提及次數。

1. 在作業**概述**中，選擇"編輯"框右上角附近的**查詢**。 Azure 列出了為作業配置的輸入和輸出，並允許您創建查詢以在輸入流發送到輸出時轉換輸入流。

2. 將查詢編輯器中的查詢更改為以下內容：

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. 消息中的事件資料應顯示在查詢下方的 **"輸入預覽"** 視窗中。 確保**視圖**設置為**JSON**。 如果看不到任何資料，請確保資料產生器向事件中心發送事件，並且已選擇**GZip**作為輸入的壓縮類型。

4. 選擇 **"測試"查詢**，並在查詢下方的 **"測試結果"** 視窗中注意到結果。

5. 將代碼編輯器中的查詢更改為以下內容，然後選擇 **"測試"查詢**：

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. 此查詢返回包含關鍵字*Azure*的所有推文。

## <a name="create-an-output-sink"></a>建立輸出接收器

您現在已定義事件資料流、用來內嵌事件的事件中樞，以及用來轉換資料流的查詢。 最後一個步驟是定義作業的輸出接收。  

在此說明指南中，您將聚合的推文事件從作業查詢寫入 Azure Blob 存儲。  根據您的應用程式需求，您也可以將結果發送至 Azure SQL Database、Azure 資料表儲存體、事件中樞或 Power BI。

## <a name="specify-the-job-output"></a>指定作業輸出

1. 在左側導航功能表的 **"作業拓撲"** 部分下，選擇 **"輸出**"。 

2. 在 **"輸出"** 頁中，按一下**+&nbsp;"添加**和**Blob 存儲/資料存儲第 2 代**：

   * **輸出別名**：使用名稱 `TwitterStream-Output`。 
   * **導入選項**：**選擇從訂閱中選擇存儲**。
   * **存儲帳戶**。 選取您的儲存體帳戶。
   * **容器**。 選擇 **"創建新"** 並`socialtwitter`輸入 。
   
4. 選取 [儲存]****。   

## <a name="start-the-job"></a>啟動工作

已指定作業輸入、查詢和輸出。 您已準備好啟動串流分析作業。

1. 確保 TwitterClientCore 應用程式正在運行。 

2. 在作業概述中，選擇 **"開始**"。

3. 在 **"開始作業"** 頁上，**對於作業輸出開始時間**，選擇 **"現在"，** 然後選擇"**開始**"。

## <a name="get-support"></a>取得支援
有關進一步説明，請嘗試我們的[Azure 流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [使用 Azure 流分析開始](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
