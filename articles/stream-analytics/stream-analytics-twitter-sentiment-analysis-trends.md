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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240290"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure 串流分析中的即時 Twitter 情感分析

本文會教您如何藉由將即時 Twitter 事件帶入 Azure 事件中樞，來建立社交媒體情感分析解決方案。 您可以撰寫 Azure 串流分析查詢來分析資料，並儲存結果以供稍後使用，或建立[Power BI](https://powerbi.com/)的儀表板來即時提供深入解析。

社交媒體分析工具可協助組織了解熱門話題。 趨勢主題是在社交媒體上有大量文章的主題和態度。 情感分析（亦稱為*意見挖掘*）會使用社交媒體分析工具來判斷對產品或想法的態度。 

即時 Twitter 趨勢分析是分析工具的絕佳範例，因為主題標籤訂閱模型可讓您接聽特定關鍵字（主題標籤），並開發摘要的情感分析。

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>案例：即時的社交媒體情感分析

擁有新聞媒體網站的公司提供與讀者有直接關聯的網站內容，試圖打敗競爭對手。 公司透過對 Twitter 資料執行即時情感分析，來針對與讀者相關的話題使用社交媒體分析。

為了在 Twitter 上即時找出熱門話題，公司需要即時分析重要話題的推文數量和情感。

## <a name="prerequisites"></a>先決條件

在本操作指南中，您會使用連接到 Twitter 的用戶端應用程式，並尋找具有特定主題標籤（您可以設定）的推文。 若要執行應用程式並使用 Azure 串流分析來分析推文，您必須具備下列各項：

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)。

* [Twitter](https://twitter.com)帳戶。

* TwitterClientCore 應用程式，它會讀取 Twitter 摘要。 若要取得此應用程式，請下載[TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)。

* 安裝[.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)版本2.1.0。

## <a name="create-an-event-hub-for-streaming-input"></a>建立用於串流輸入的事件中樞

此範例應用程式會產生事件，並將事件發送至 Azure 事件中樞。 Azure 事件中樞是串流分析的事件內嵌慣用方法。 如需詳細資訊，請參閱 [Azure 事件中樞文件](../event-hubs/event-hubs-what-is-event-hubs.md)。

### <a name="create-an-event-hub-namespace-and-event-hub"></a>建立事件中樞命名空間和事件中樞
在本節中，您會建立事件中樞命名空間，並將事件中樞新增至該命名空間。 事件中樞命名空間可在邏輯上將相關的事件匯流排執行個體分組。 

1. 登入 Azure 入口網站，然後選取 [**建立資源**]。 請. 搜尋**事件中樞**，然後選取 [**建立**]。

2. 在 [**建立命名空間**] 頁面上，輸入命名空間名稱。 您可以使用命名空間的任何名稱，但名稱必須是有效的 URL，而且在整個 Azure 中必須是唯一的。 
    
3. 選取 [定價層] 和 [訂用帳戶]，然後建立或選擇資源群組。 然後，選擇一個位置，然後選取 [**建立**]。 
 
4. 當命名空間完成部署時，流覽至您的資源群組，並在您的 Azure 資源清單中尋找事件中樞命名空間。 

5. 從新的命名空間中，選取** + &nbsp;[事件中樞**]。 

6. 將新的事件中樞命名為*socialtwitter-eh*。 您可以使用不同的名稱。 如果這樣做，請記下來，因為稍後需要用到此名稱。 您不需要為事件中樞設定其他任何選項。
 
7. 選取 [建立]  。

### <a name="grant-access-to-the-event-hub"></a>授權存取事件中樞

在處理常式可以將資料傳送至事件中樞之前，事件中樞需要允許存取的原則。 存取原則會產生包含授權資訊的連接字串。

1.  在事件中樞命名空間左側的導覽列中，選取位於 [**實體**] 區段中的 [**事件中樞**]。 然後，選取您剛才建立的事件中樞。

2.  在左側的導覽列中，選取位於 [**設定**] 底下的 [**共用存取原則**]。

    >[!NOTE]
    >事件中樞命名空間和事件中樞的下有 [共用存取原則] 選項。 請確定您是在事件中樞的內容中運作，而不是在整體的事件中樞命名空間中工作。

3.  在 [存取原則] 頁面上，選取 [ **+ 新增**]。 然後在 [**原則名稱**] 中輸入*socialtwitter-access* ，然後勾選 [**管理**] 核取方塊。
 
4.  選取 [建立]  。

5.  部署原則之後，請從共用存取原則的清單中選取原則。

6.  尋找標示為**連接字串主要金鑰**的方塊，然後選取連接字串旁的 [複製] 按鈕。
 
7.  將連接字串貼到文字編輯器。 在您進行一些小型編輯之後，下一節需要此連接字串。

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
如果您還沒有可用於此操作指南的 Twitter 應用程式，您可以建立一個。 您必須已經有 Twitter 帳戶。

> [!NOTE]
> 在 Twitter 中建立應用程式及取得金鑰、秘密和權杖的確切流程可能不同。 如果這些指示不符合您在 Twitter 網站上看到的內容，請參閱 Twitter 開發人員文件。

1. 在網頁瀏覽器中，移至 [[適用于開發人員的 Twitter](https://developer.twitter.com/en/apps)]，建立開發人員帳戶，然後選取 [**建立應用程式**]。 您可能會看到一則訊息，指出您必須申請 Twitter 開發人員帳戶。 您可以隨意執行此動作，在您的應用程式經過核准之後，您應該會看到一封確認電子郵件。 核准開發人員帳戶可能需要幾天的時間。

   ![Twitter 應用程式詳細資料](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter 應用程式詳細資料")

2. 在 [建立應用程式]**** 頁面上，提供新應用程式的詳細資料，然後選取 [建立 Twitter 應用程式]****。

   ![Twitter 應用程式詳細資料](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter 應用程式詳細資料")

3. 在應用程式頁面中，選取 [金鑰和權杖]**** 索引標籤，並複製 [取用者 API 金鑰]**** 和 [取用者 API 秘密金鑰]**** 的值。 此外，選取 [存取權杖和存取權杖密碼]**** 底下的 [建立]**** 來產生存取權杖。 複製 [存取權杖]**** 和 [存取權杖祕密]**** 的值。

   儲存您所擷取的 Twitter 應用程式值。 您稍後需要這些值。

> [!NOTE]
> Twitter 應用程式的金鑰和秘密可用來存取您的 Twitter 帳戶。 這項資訊要視為機密，就像看待您的 Twitter 密碼一樣。 例如，請勿將這項資訊內嵌在您提供給其他人的應用程式中。 

### <a name="configure-the-client-application"></a>設定用戶端應用程式

我們已建立用戶端應用程式，可利用 [Twitter 的串流 API](https://dev.twitter.com/streaming/overview) 來連線至 Twitter 資料，以收集一組特定話題相關的推文事件。

在應用程式執行之前，您需要提供一些資訊，例如 Twitter 金鑰和事件中樞連接字串。

1. 請確定您已下載[TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)應用程式（如必要條件中所列）。

2. 使用文字編輯器來開啟*app.config*檔案。 對`<appSettings>`元素進行下列變更：

   * 設定`oauth_consumer_key`為 Twitter 取用者金鑰（API 金鑰）。 
   * 設定`oauth_consumer_secret`為 Twitter 取用者秘密（API 秘密金鑰）。
   * 將`oauth_token`設定為 Twitter 存取權杖。
   * 設定`oauth_token_secret`為 Twitter 存取權杖秘密。
   * 將 `EventHubNameConnectionString` 設為連接字串。
   * 將`EventHubName`設定為事件中樞名稱（也就是實體路徑的值）。

3. 開啟命令列，並流覽至您的 TwitterClientCore 應用程式所在的目錄。 使用命令`dotnet build`來建立專案。 然後使用命令`dotnet run`來執行應用程式。 應用程式會將推文傳送至事件中樞。

## <a name="create-a-stream-analytics-job"></a>建立串流分析作業

既然正在從 Twitter 即時串流推文事件，您可以設定串流分析作業來即時分析這些事件。

1. 在 [Azure 入口網站中，流覽至您的資源群組，然後選取 [ **+ 新增**]。 然後搜尋**串流分析作業**，然後選取 [**建立**]。

2. 將作業命名為 `socialtwitter-sa-job`，並指定訂用帳戶、資源群組和位置。

    最好將作業和事件中樞放在相同的區域，以達到最佳效能，在區域之間傳送資料也不需要付費。

3. 選取 [建立]  。 然後在部署完成時，流覽至您的作業。

## <a name="specify-the-job-input"></a>指定工作輸入

1. 在您的串流分析作業中，從 [**作業拓撲**] 底下的左側功能表選取 [**輸入**]。

2.  > 選取** + &nbsp;[新增資料流程輸入****事件中樞**]。 在新的**輸入**表單中填寫下列資訊：

   |**設定**  |**建議的值**  |**說明**  |
   |---------|---------|---------|
   |輸入別名| *TwitterStream* | 輸入輸入的別名。 |
   |訂用帳戶  | \<您的訂用帳戶\> |  選取您要使用的 Azure 訂用帳戶。 |
   |事件中樞命名空間 | *asa-twitter-eventhub* |
   |事件中樞名稱 | *socialtwitter-eh* | 選擇 [*使用現有*的]。 然後選取您所建立的事件中樞。|
   |事件壓縮類型| GZip | 資料壓縮類型。|

   保留其餘的預設值，然後選取 [**儲存**]。

## <a name="specify-the-job-query"></a>指定作業查詢

串流分析支援說明轉換的簡單、宣告式查詢模型。 若要深入了解語言，請參閱 [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)。 本操作指南可協助您撰寫和測試 Twitter 資料的數個查詢。

為了比較提及不同話題的次數，您可以使用[輪轉視窗](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics)，依話題每隔五秒取得一次提及次數。

1. 在您的作業**總覽**中，選取靠近 [查詢] 方塊右上方的 [**編輯查詢**]。 Azure 會列出針對作業設定的輸入和輸出，並可讓您建立查詢，以便在輸入資料流程傳送到輸出時進行轉換。

2. 在 [查詢編輯器] 中，將查詢變更為下列內容：

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. 訊息中的事件資料應該會出現在您查詢下方的 [**輸入預覽**] 視窗中。 請確定此**視圖**已設定為**JSON**。 如果您看不到任何資料，請確定您的資料產生器正在將事件傳送至事件中樞，而且您已選取**GZip**做為輸入的壓縮類型。

4. 選取 [**測試查詢**]，並注意查詢下方的 [**測試結果**] 視窗中的結果。

5. 在程式碼編輯器中，將查詢變更為下列，然後選取 [**測試查詢**]：

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. 此查詢會傳回包含關鍵字*Azure*的所有推文。

## <a name="create-an-output-sink"></a>建立輸出接收器

您現在已定義事件資料流、用來內嵌事件的事件中樞，以及用來轉換資料流的查詢。 最後一個步驟是定義作業的輸出接收。  

在此操作指南中，您會將作業查詢中的匯總推文事件寫入 Azure Blob 儲存體。  根據您的應用程式需求，您也可以將結果發送至 Azure SQL Database、Azure 資料表儲存體、事件中樞或 Power BI。

## <a name="specify-the-job-output"></a>指定作業輸出

1. 在左側導覽功能表的 [**作業拓撲**] 區段下，選取 [**輸出**]。 

2. 在 [**輸出**] 頁面上** + &nbsp; ** ，按一下 [新增] 和 [ **Blob 儲存體/Data Lake Storage Gen2**：

   * **輸出別名**：使用名稱 `TwitterStream-Output`。 
   * 匯**入選項**：選取 [**從您的訂用帳戶選取儲存體**]。
   * **儲存體帳戶**。 選取您的儲存體帳戶。
   * **容器**。 選取 **[新建]** 並`socialtwitter`輸入。
   
4. 選取 [儲存]  。   

## <a name="start-the-job"></a>啟動工作

已指定作業輸入、查詢和輸出。 您已準備好啟動串流分析作業。

1. 請確定 TwitterClientCore 應用程式正在執行。 

2. 在 [作業總覽] 中，選取 [**啟動**]。

3. 在 [**啟動作業**] 頁面上，針對 [**作業輸出開始時間**] 選取 [**現在**]，然後選取 [**啟動**]。

## <a name="get-support"></a>取得支援
如需進一步的協助，請嘗試我們的[Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure 串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 串流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
