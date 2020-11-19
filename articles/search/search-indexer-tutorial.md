---
title: 為 Azure SQL 資料編製索引的 C# 教學課程
titleSuffix: Azure Cognitive Search
description: 在此 C# 教學課程中，連線至 Azure SQL Database、擷取可搜尋的資料，並將其載入至 Azure 認知搜尋服務索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 960657d27be4b9dab9f242428592bbb404a49d86
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697164"
---
# <a name="tutorial-index-azure-sql-data-using-the-net-sdk"></a>教學課程：使用 .NET SDK 為 Azure SQL 資料編製索引

設定[索引子](search-indexer-overview.md)，以從 Azure SQL Database 中擷取可搜尋的資料，並將其傳送至 Azure 認知搜尋中的搜尋索引。 

本教學課程將使用 C# 和 [.NET SDK](/dotnet/api/overview/azure/search) 執行下列工作：

> [!div class="checklist"]
> * 建立會連線到 Azure SQL Database 的資料來源
> * 建立索引子
> * 執行索引子以將資料載入至索引
> * 以驗證步驟的形式查詢索引

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [建立](search-create-service-portal.md)或[尋找現有的搜尋服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 您可以使用免費服務來進行本教學課程。 免費的搜尋服務會有限制，您只能使用三個索引、三個索引子和三個資料來源。 本教學課程會各建立一個。 開始之前，請確定您的服務有空間可接受新的資源。

## <a name="download-files"></a>下載檔案

本教學課程的原始程式碼位於 [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) GitHub 存放庫中的 [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) 資料夾內。

## <a name="1---create-services"></a>1 - 建立服務

本教學課程將使用 Azure 認知搜尋進行索引編製和查詢，並以 Azure SQL Database 作為外部資料來源。 為具備鄰近性和管理方面的優勢，如果可能，請將這兩項服務建立在相同的區域和資源群組中。 在實務上，Azure SQL Database 可以位於任何區域中。

### <a name="start-with-azure-sql-database"></a>從 Azure SQL Database 開始著手

在此步驟中，請於 Azure SQL Database 上建立索引子可以搜耙的外部資料來源。 您可以使用 Azure 入口網站和範例下載中的 hotels.sql 檔案，在 Azure SQL Database 中建立資料集。 Azure 認知搜尋會取用扁平化資料列集，例如從檢視或查詢產生的資料列集。 範例方案中的 SQL 檔案會建立並填入單一資料表。

如果您有現有的 Azure SQL Database 資源，您可以在其中新增 hotels 資料表 (在步驟 4 開始)。

1. [登入 Azure 入口網站](https://portal.azure.com/)。

1. 尋找或建立 **SQL Database**。 您可以使用預設值和最低層級的定價層。 建立伺服器的優點之一是您可以指定系統管理員使用者名稱和密碼，以便在稍後步驟中建立和載入資料表。

   :::image type="content" source="media/search-indexer-tutorial/indexer-new-sqldb.png" alt-text="新增資料庫頁面" border="false":::

1. 按一下 [檢閱 + 建立] 來部署新的伺服器和資料庫。 等候部署伺服器和資料庫。

1. 在瀏覽窗格中，按一下 [查詢編輯器 (預覽)]，然後輸入伺服器管理員的使用者名稱和密碼。 

   如果存取遭拒，請複製錯誤訊息中的用戶端 IP 位址，然後按一下 [設定伺服器防火牆] 連結，以新增規則來允許使用您的用戶端 IP 作為範圍來從用戶端電腦進行存取。 規則可能需要幾分鐘的時間才會生效。

1. 在查詢編輯器中，按一下 [開啟查詢]，然後瀏覽至本機電腦上「hotels.sql」檔案所在的位置。 

1. 選取檔案，然後按一下 [開啟]。 指令碼應該會看起來如下列螢幕擷取畫面所示：

   :::image type="content" source="media/search-indexer-tutorial/sql-script.png" alt-text="SQL 指令碼" border="false":::

1. 按一下 [執行] 來執行查詢。 在 [結果] 窗格中，您應會看到查詢成功訊息 (3 個資料列)。

1. 若要從這個資料表傳回資料列集，您可以執行下列查詢作為驗證步驟：

    ```sql
    SELECT * FROM Hotels
    ```

1. 複製資料庫的 ADO.NET 連接字串。 在 [設定] > [連接字串] 底下，複製 ADO.NET 連接字串，如下列範例所示。

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

在下一個練習中，您將需要此連接字串來設定您的環境。

### <a name="azure-cognitive-search"></a>Azue 認知搜尋

下一個元件是 Azure 認知搜尋，您可以[在入口網站中建立](search-create-service-portal.md)該服務。 您可以使用免費層來完成此逐步解說。 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>取得 Azure 認知搜尋的管理員 API 金鑰和 URL

API 呼叫需要用到服務 URL 和存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀] 頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定] >  [金鑰] 中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="取得 HTTP 端點和存取金鑰" border="false":::

## <a name="2---set-up-your-environment"></a>2 - 設定您的環境

1. 啟動 Visual Studio 並開啟 **DotNetHowToIndexers.sln**。

1. 在方案總管中開啟 **appsettings.json**，以提供連線資訊。

1. 就 `searchServiceName` 而言，如果完整 URL 是 "https://my-demo-service.search.windows.net"，則應提供的服務名稱就是 "my-demo-service"。

1. `AzureSqlConnectionString` 的字串格式如下所示：`"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. 在連接字串中，確定連接字串包含有效的密碼。 雖然資料庫和使用者名稱會複製過去，但密碼必須手動輸入。

## <a name="3---create-the-pipeline"></a>3 - 建立管線

索引子需要資料來源物件和索引。 相關程式碼位於兩個檔案中：

  + **hotel.cs**，內含可定義索引的結構描述
  + **Program.cs**，內含用於建立和管理您服務中結構的函式

### <a name="in-hotelcs"></a>在 hotel.cs 中

索引結構描述會定義欄位集合，包括用於指定允許作業的屬性，例如欄位是否可全文檢索搜尋、可篩選，或可排序 (如 HotelName 的下列欄位定義中所示)。 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

結構描述也可以包含其他元素，包括提高搜尋分數、自訂分析城市和其他建構的評分設定檔。 不過，基於我們的目的，結構描述並未嚴密定義，只包含在範例資料集中找到的欄位。

### <a name="in-programcs"></a>在 Program.cs 中

主要程式包含用於建立用戶端、索引、資料來源和索引子的邏輯。 在您可能會執行此程式多次的假設之下，此程式碼會檢查並刪除現有的同名資源。

資料來源物件上會配置專屬於 Azure SQL Database 資源的設定，包括[部份或增量索引編製](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)，以充分利用 Azure SQL 的內建[變更偵測功能](/sql/relational-databases/track-changes/about-change-tracking-sql-server)。 在 Azure SQL 中，示範用的飯店資料庫具有名為 **IsDeleted** 的「虛刪除」資料行。 當此資料行在資料庫中設定為 true 時，索引子就會從 Azure 認知搜尋索引中移除對應文件。

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

索引子物件可跨平台使用，不論來源為何，其中的設定、排程和引動過程都相同。 此索引子範例包括排程及用於清除索引子記錄的重設選項，並且會呼叫方法來立即建立並執行索引子。

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```

## <a name="4---build-the-solution"></a>4 - 建置解決方案

按 F5 以建置及執行解決方案。 此程式會在偵錯模式中執行。 主控台視窗會報告每項作業的狀態。

   :::image type="content" source="media/search-indexer-tutorial/console-output.png" alt-text="主控台輸出" border="false":::

您的程式碼會在 Visual Studio 本機執行，並連線到您在 Azure 上的搜尋服務，搜尋服務再連線到 Azure SQL Database 並擷取資料集。 由於要進行的作業很多，所以會有數個潛在的失敗點。 如果您收到錯誤，請先檢查下列條件：

+ 您提供搜尋服務連線資訊受限於本教學課程中的服務名稱。 如果您輸入了完整 URL，作業會在索引建立時停止，出現連線失敗錯誤。

+ **appsettings.json** 中的資料庫連線資訊。 它應該是從入口網站取得的 ADO.NET 連接字串，已修改成包含您的資料庫適用的使用者名稱和密碼。 使用者帳戶必須具有擷取資料的權限。 您的本機用戶端 IP 位址則必須獲得允許而能進行存取。

+ 資源限制。 回想一下，免費層有 3 個索引、索引子和資料來源的限制。 最大限制的服務無法建立新的物件。

## <a name="5---search"></a>5 - 搜尋

使用 Azure 入口網站來確認物件的建立，然後使用 **搜尋總管** 來查詢索引。

1. [登入 Azure 入口網站](https://portal.azure.com/)，然後在搜尋服務的 [概觀] 頁面中，依序開啟每個清單以確認物件已完成建立。 [索引]、[索引子] 和 [資料來源] 分別會有 "hotels"、"azure-sql-indexer" 和 "azure-sql"。

   :::image type="content" source="media/search-indexer-tutorial/tiles-portal.png" alt-text="索引子和資料來源圖格" border="false":::

1. 選取 [hotels] 索引。 在 [hotels] 頁面上，第一個索引標籤是 [搜尋總管]。 

1. 按一下 [搜尋] 以發出空白查詢。 

   您的索引中的三個項目會以 JSON 文件形式傳回。 搜尋總管會以 JSON 傳回文件，以便您檢視整個結構。

   :::image type="content" source="media/search-indexer-tutorial/portal-search.png" alt-text="查詢索引" border="false":::
   
1. 接下來，輸入搜尋字串：`search=river&$count=true`。 

   此查詢會叫用 `river` 字詞的全文檢索搜尋，而結果會包含相符文件的計數。 在測試包含數千甚至數百萬份文件的大型索引案例時，傳回相符文件的計數很實用。 在此情況下，只有一份文件符合查詢。

1. 最後，輸入搜尋字串，將 JSON 輸出限制為感興趣的欄位：`search=river&$count=true&$select=hotelId, baseRate, description`。 

   查詢回應會縮減為選取的欄位，導致更簡潔的輸出。

## <a name="reset-and-rerun"></a>重設並重新執行

在開發的早期實驗階段中若要設計反覆項目，最實用的方法是從 Azure 認知搜尋中刪除物件，並讓您的程式碼加以重建。 資源名稱是唯一的。 刪除物件可讓您使用相同的名稱加以重新建立。

本教學課程的範例程式碼會檢查是否有現有的物件，並將其刪除，以便您重新執行程式碼。

您也可以使用入口網站來刪除索引、索引子和資料來源。

## <a name="clean-up-resources"></a>清除資源

如果您使用自己的訂用帳戶，當專案結束時，建議您移除不再需要的資源。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

## <a name="next-steps"></a>後續步驟

現在您已熟悉 SQL Database 編製索引的基本概念，接下來我們將進一步了解索引子設定。

> [!div class="nextstepaction"]
> [設定 SQL 資料庫索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)