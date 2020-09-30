---
title: 在 Azure Blob 上使用 AI 的 C# 教學課程
titleSuffix: Azure Cognitive Search
description: 逐步解說使用 C# 和 Azure 認知搜尋 .NET SDK 對 Blob 儲存體中的內容進行文字擷取和自然語言處理的範例。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 13e3f5a7d86d2e9b705fbeb104ba4f8eb690cb3a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534092"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>教學課程：使用 .NET SDK 以 AI 方式從 Azure Blob 產生可搜尋的內容

如果您在 Azure Blob 儲存體中有非結構化的文字或影像，您可以利用 [AI 擴充管線](cognitive-search-concept-intro.md)來擷取資訊，並建立適用於全文檢索搜尋或知識採礦案例的新內容。 在此 C# 教學課程中，我們將對影像套用光學字元辨識 (OCR) 功能，並執行自然語言處理，以建立可在查詢、Facet 和篩選器中運用的新欄位。

本教學課程將使用 C# 和 [.NET SDK](/dotnet/api/overview/azure/search) 執行下列工作：

> [!div class="checklist"]
> * 從 Azure Blob 儲存體中的應用程式檔案和影像開始著手。
> * 定義管線以新增 OCR、文字擷取、語言偵測、實體和關鍵片語辨識。
> * 定義用來儲存輸出的索引 (原始內容加上管線產生的名稱/值組)。
> * 執行管線以開始轉換和分析，以及建立和載入索引。
> * 使用全文檢索搜尋和豐富的查詢語法來探索結果。

如果您沒有 Azure 訂用帳戶，請在開始前開啟[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

+ [Azure 儲存體](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [建立](search-create-service-portal.md)或[尋找現有的搜尋服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 您可以使用免費服務來進行本教學課程。 免費的搜尋服務會有限制，您只能使用三個索引、三個索引子和三個資料來源。 本教學課程會各建立一個。 開始之前，請確定您的服務有空間可接受新的資源。

## <a name="download-files"></a>下載檔案

1. 開啟此 [OneDrive 資料夾](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)，然後在左上角按一下 [下載]，將檔案複製到您的電腦。 

1. 以滑鼠右鍵按一下 ZIP 檔案並選取 [全部解壓縮]。 其中有 14 個不同類型的檔案。 在此練習中，您將使用 7 個檔案。

您也可以下載本教學課程的原始程式碼。 原始程式碼位於 [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) 存放庫的 tutorial-ai-enrichment 資料夾中。

## <a name="1---create-services"></a>1 - 建立服務

本教學課程會使用 Azure 認知搜尋進行索引編製和查詢、在後端將認知服務用於 AI 擴充，以及使用 Azure Blob 儲存體來提供資料。 本教學課程在認知服務上使用的資源會保持在每個索引子每日 20 筆交易的免費配置以內，因此您需要建立的服務只有搜尋和儲存體。

為具備鄰近性和管理方面的優勢，如果可能，請將這兩項服務建立在相同的區域和資源群組中。 實際上，您的 Azure 儲存體帳戶可以位在任何區域中。

### <a name="start-with-azure-storage"></a>開始使用 Azure 儲存體

1. [登入 Azure 入口網站](https://portal.azure.com/)，然後按一下 [+ 建立資源]。

1. 搜尋「儲存體帳戶」，然後選取 Microsoft 的儲存體帳戶供應項目。

   ![建立儲存體帳戶](media/cognitive-search-tutorial-blob/storage-account.png "建立儲存體帳戶")

1. 在 [基本] 索引標籤中，需要下列項目。 接受所有其他項目的預設值。

   + **資源群組**。 選取現有群組或建立一個新的群組，但必須對所有服務使用相同的群組，以便您一起管理這些服務。

   + **儲存體帳戶名稱**。 如果您認為您可能會有多個相同類型的資源，請透過名稱在類型和區域上做出區別，例如 blobstoragewestus。 

   + **位置**。 可能的話，請選擇用於 Azure 認知搜尋和認知服務的相同位置。 單一位置可避免產生頻寬費用。

   + **帳戶種類**。 選擇預設值 [StorageV2 (一般用途 v2)]。

1. 按一下 [檢閱 + 建立] 以建立服務。

1. 建立後，按一下 [移至資源] 以開啟 [概觀] 頁面。

1. 按一下 [Blob] 服務。

1. 按一下 [+ 容器] 來建立容器，並將其命名為 cog-search-demo。

1. 選取 [cog-search-demo]，然後按一下 [上傳] 以開啟其中儲存下載檔案的資料夾。 將十四個檔案全選，然後按一下 [確定] 加以上傳。

   ![上傳範例檔案](media/cognitive-search-quickstart-blob/sample-data.png "上傳範例檔案")

1. 在您離開 Azure 儲存體之前，請取得連接字串，以便在 Azure 認知搜尋中制定連線。 

   1. 往回瀏覽到儲存體帳戶的 [概觀] 頁面 (我們使用 blobstoragewestus 作為範例)。 
   
   1. 在左側導覽窗格中，選取 [存取金鑰] 並複製其中一個連接字串。 

   連接字串應為類似於下列範例的 URL：

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. 將連接字串儲存到記事本。 您稍後設定資料來源連線時會用到該字串。

### <a name="cognitive-services"></a>認知服務

AI 擴充以認知服務為後盾，包括用於自然語言和影像處理的文字分析和電腦視覺。 如果您的目標是要完成實際的原型或專案，您應在此時佈建認知服務 (位於 Azure 認知搜尋所在的區域)，以便將其連結至索引作業。

不過，在此練習中，您可以略過資源佈建，因為 Azure 認知搜尋可以在幕後連線到認知服務，並為每個索引子執行提供 20 筆免費交易。 由於本教學課程會使用 14 筆交易，因此使用免費配置就已足夠。 針對較大型的專案，請考慮以隨用隨付 S0 層來佈建認知服務。 如需詳細資訊，請參閱[連結認知服務](cognitive-search-attach-cognitive-services.md)。

### <a name="azure-cognitive-search"></a>Azue 認知搜尋

第三個元件是 Azure 認知搜尋，您可以[在入口網站中建立](search-create-service-portal.md)該服務。 您可以使用免費層來完成此逐步解說。 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>取得 Azure 認知搜尋的管理員 API 金鑰和 URL

若要與 Azure 認知搜尋服務互動，您需要服務 URL 和存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀] 頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定] >  [金鑰] 中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

   一併取得查詢金鑰。 最佳做法是發出具有唯讀存取權的查詢要求。

   ![取得服務名稱及管理和查詢金鑰](media/search-get-started-nodejs/service-name-and-keys.png)

擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="2---set-up-your-environment"></a>2 - 設定您的環境

一開始請開啟 Visual Studio，並建立可在 .NET Core 上執行的新主控台應用程式專案。

### <a name="install-nuget-packages"></a>安裝 NuGet 套件

[Azure 認知搜尋 .NET SDK](/dotnet/api/overview/azure/search) 包含數個用戶端程式庫，可讓您管理索引、資料來源、索引子及技能集，以及上傳和管理文件，還可以執行查詢，而且一律不需要處理 HTTP 和 JSON 的細節。 這些用戶端程式庫都以 NuGet 套件來散發。

針對此專案，請安裝第 9 版或更新版本的 `Microsoft.Azure.Search` NuGet 套件。

1. 在瀏覽器中，移至 [Microsoft.Azure.Search NuGet 套件頁面](https://www.nuget.org/packages/Microsoft.Azure.Search)。

1. 選取最新版本 (9 或更新版本)。

1. 複製套件管理員命令。

1. 開啟套件管理員主控台。 選取 [工具] > [NuGet 套件管理員] > [套件管理員主控台]。 

1. 貼上並執行您在先前的步驟中複製的命令。

接著，安裝最新的 `Microsoft.Extensions.Configuration.Json` NuGet 套件。

1. 選取 [工具] > [NuGet 套件管理員] > [管理解決方案的 NuGet 套件...]。 

1. 按一下 [瀏覽]，然後搜尋 `Microsoft.Extensions.Configuration.Json` NuGet 套件。 

1. 選取該套件、選取您的專案、確認該版本為最新的穩定版本，然後按一下 [安裝]。

### <a name="add-service-connection-information"></a>新增服務連線資訊

1. 在 [方案總管] 中以滑鼠右鍵按一下您的專案，然後選取 [新增] > [新增項目...]。 

1. 將檔案命名為 `appsettings.json`，然後選取 [新增]。 

1. 將此檔案包含在您的輸出目錄中。
    1. 以滑鼠右鍵按一下 `appsettings.json`，然後選取 [屬性]。 
    1. 將 [複製到輸出目錄] 的值變更為 [有更新時才複製]。

1. 將下列 JSON 複製到新的 JSON 檔案。

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

新增您的搜尋服務與 Blob 儲存體帳戶資訊。 您應該記得，您可以從上一節所指示的服務佈建步驟中取得這項資訊。

針對 **SearchServiceName**，請輸入簡短服務名稱，而不是完整 URL。

### <a name="add-namespaces"></a>新增命名空間

在 `Program.cs` 中，新增下列命名空間。

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>建立用戶端

在 `Main` 下建立 `SearchServiceClient` 類別的執行個體。

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` 會使用儲存在應用程式組態檔 (appsettings.json) 中的值，來建立新的 `SearchServiceClient`。

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> `SearchServiceClient` 類別會管理連至搜尋服務的連線。 為了避免開啟太多連線，如果可以，您應該嘗試在應用程式中共用 `SearchServiceClient` 的單一執行個體。 它的方法是可啟用這類共用的安全執行緒。
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>新增函式以在失敗期間結束程式

本教學課程旨在協助您了解編製索引管線的每個步驟。 如果發生嚴重問題，導致程式無法建立資料來源、技能集、索引或索引子，程式將會輸出錯誤訊息並結束，讓您可以得知並解決問題。

將 `ExitProgram` 新增至 `Main`，以處理需要結束程式的案例。

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - 建立管線

在 Azure 認知搜尋中，AI 處理會在編製索引 (或資料擷取) 期間進行。 逐步解說的這個部分會建立四個物件：資料來源、索引定義、技能集、索引子。 

### <a name="step-1-create-a-data-source"></a>步驟 1:建立資料來源

`SearchServiceClient` 具有 `DataSources` 屬性。 此屬性會提供您建立、列出、更新或刪除 Azure 認知搜尋資料來源所需的所有方法。

呼叫 `serviceClient.DataSources.CreateOrUpdate(dataSource)` 來建立新的 `DataSource` 執行個體。 `DataSource.AzureBlobStorage` 要求您指定資料來源名稱、連接字串和 Blob 容器名稱。

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "cog-search-demo",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

如果要求成功，方法將傳回已建立的資料來源。 如果要求發生問題 (例如無效的參數)，則方法將擲回例外狀況。

現在，在 `Main` 中新增一行，以呼叫您剛才新增的 `CreateOrUpdateDataSource` 函式。

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```

建置並執行解決方案。 由於這是您第一個要求，請查看 Azure 入口網站，以確認已在 Azure 認知搜尋中建立資料來源。 在搜尋服務儀表板頁面上，確認 [資料來源] 圖格有新的項目。 您可能需要等候幾分鐘，讓入口網站重新整理頁面。

  ![入口網站中的資料來源圖格](./media/cognitive-search-tutorial-blob/data-source-tile.png "入口網站中的資料來源圖格")

### <a name="step-2-create-a-skillset"></a>步驟 2:建立技能集

在此節中，您會定義一組要套用至資料的擴充步驟。 每個擴充步驟均稱為一個「技能」，而一組擴充步驟會稱為一個「技能集」。 本教學課程會使用為技能集[內建的認知技能](cognitive-search-predefined-skills.md)：

+ [光學字元辨識](cognitive-search-skill-ocr.md)可辨識影像檔案中的列印和手寫文字。

+ [文字合併](cognitive-search-skill-textmerger.md)可將一組欄位中的文字合併成單一欄位。

+ [語言偵測](cognitive-search-skill-language-detection.md)，用以識別內容的語言。

+ [文字分割](cognitive-search-skill-textsplit.md)，用以在呼叫關鍵片語擷取技能和實體辨識技能之前，將大型內容分成較小的區塊。 關鍵片語擷取和實體辨識接受 50,000 個字元 (含) 以下的輸入。 有些範例檔案需要進行分割，以符合這項限制。

+ [實體辨識](cognitive-search-skill-entity-recognition.md)，用以從 Blob 容器中的內容擷取組織名稱。

+ [關鍵片語擷取](cognitive-search-skill-keyphrases.md)，用以提取最高排名的關鍵片語。

在最初處理期間，Azure 認知搜尋會萃取每份文件，以讀取不同檔案格式的內容。 找到來自來源檔案的文字時，會將文字放入產生的 ```content``` 欄位中，每份文件一個欄位。 如前所述，將輸入設定為 ```"/document/content"``` 以使用此文字。 

輸出可以對應至索引、作為下游技能的輸入，或在使用語言代碼時同時作為對應和輸入。 在索引中，語言代碼可用於篩選。 作為輸入時，文字分析技能會使用語言代碼指出斷字方面的語言規則。

如需技能集基本概念的詳細資訊，請參閱[如何定義技能集](cognitive-search-defining-skillset.md)。

### <a name="ocr-skill"></a>OCR 技術

**OCR** 技能可從影像中擷取文字。 此技能假設 normalized_images 欄位存在。 為產生此欄位，我們會在此教學課程稍後，於索引子定義中將 ```"imageAction"``` 設定設為 ```"generateNormalizedImages"```。

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "text",
        targetName: "text")
    };

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>合併技能

在此節中，您將建立**合併**技能，來合併文件內容欄位與 OCR 技能所產生的文字。

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"),
        new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"),
        new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text")
    };

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>語言偵測技能

**語言偵測**技能會偵測輸入文字的語言，並針對要求所提交的每份文件回報單一語言代碼。 我們將使用**語言偵測**技能的輸出作為**文字分割**技能輸入的一部分。

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode")
    };

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>文字分割技能

下列**分割**技能將依頁面分割文字，並按照 `String.Length` 所測量的來將其限制為 4,000 個字元。 此演算法會嘗試將文字分割成區塊，且區塊大小最多為 `maximumPageLength`。 在此案例中，演算法將盡量在例句邊界斷句，好讓區塊大小稍微小於 `maximumPageLength`。

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"),
        new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages")
    };

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>實體辨識技能

此 `EntityRecognitionSkill` 執行個體會設定來辨識類別類型 `organization`。 **實體辨識**技能也可以辨識類別類型 `person` 和 `location`。

請注意，[內容] 欄位會設定為附有星號的 ```"/document/pages/*"```，這表示會在 ```"/document/pages"``` 下方針對每個頁面呼叫擴充步驟。

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations")
    };

    List<EntityCategory> entityCategory = new List<EntityCategory>
    {
        EntityCategory.Organization
    };

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>關鍵片語擷取技能

如同剛建立的 `EntityRecognitionSkill` 執行個體，會針對文件的每個頁面呼叫**關鍵片語擷取**技能。

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>建置和建立技能集

使用您建立的技能來建置 `Skillset`。

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

將下列數行新增至 `Main`。

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>
    {
        ocrSkill,
        mergeSkill,
        languageDetectionSkill,
        splitSkill,
        entityRecognitionSkill,
        keyPhraseExtractionSkill
    };

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>步驟 3：建立索引

在本節中，您會藉由指定要在可搜尋索引中包含哪些欄位以及每個欄位的搜尋屬性，來定義索引結構描述。 欄位具有類型，並且可取用決定如何使用欄位 (可搜尋、可排序等等) 的屬性。 索引中的欄位名稱不需要完全符合來源中的欄位名稱。 在後續步驟中，您可以在索引子中新增欄位對應以連接來源-目的地欄位。 針對此步驟，請使用與搜尋應用程式相關的欄位命名慣例來定義索引。

此練習會使用下列欄位和欄位類型：

| 欄位名稱 | 欄位類型 |
| --- | --- |
| `id` | Edm.String |
| `content` | Edm.String |
| `languageCode` | Edm.String |
| `keyPhrases` | List<Edm.String> |
| `organizations` | List<Edm.String> |

#### <a name="create-demoindex-class"></a>建立 DemoIndex 類別

此索引的欄位會使用模型類別來定義。 每個模型類別的屬性皆具有屬性，會判斷對應索引欄位的搜尋相關行為。 

我們會將模型類別新增至新的 C# 檔案。 以滑鼠右鍵按一下您的專案，然後選取 [新增] > [新增項目]、選取 [類別] 並將檔案命名為 `DemoIndex.cs`，然後選取 [新增]。

請務必指出您想要使用來自 `Microsoft.Azure.Search` 和 `Microsoft.Azure.Search.Models` 命名空間的類型。

將下列模型類別定義新增至 `DemoIndex.cs`，然後將它包含於您將建立索引的相同命名空間。

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

既然您已定義模型類別，請返回 `Program.cs`，您現在可以非常輕鬆地建立索引定義。 此索引的名稱將會是 `demoindex`。 如果已有索引使用該名稱，將會加以刪除。

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

在測試期間，您可能發現您正嘗試多次建立索引。 基於此緣故，請檢查以查看您即將建立的索引是否已經存在，然後再嘗試建立它。

將下列數行新增至 `Main`。

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Microsoft.Azure.Search.Models.Index demoIndex = CreateDemoIndex(serviceClient);
```

新增下列 using 陳述式以解析「消除歧義」參考。

```csharp
using Index = Microsoft.Azure.Search.Models.Index;
```

若要深入了解如何定義索引，請參閱[建立索引 (Azure 認知搜尋 REST API)](/rest/api/searchservice/create-index)。

### <a name="step-4-create-and-run-an-indexer"></a>步驟 4：建立及執行索引子

至此，您已建立資料來源、技能集和索引。 這三項元件構成了[索引子](search-indexer-overview.md)的一部分，可將各項資料一併提取到多階段的單一作業中。 若要在索引子中結合這些項目，您必須定義欄位對應。

+ fieldMappings 會在技能集之前進行處理，用來將資料來源中的來源欄位對應到索引中的目標欄位。 如果兩端上的欄位名稱和類型都相同，則不需要任何對應。

+ outputFieldMappings 會在技能集之後進行處理，用來參考文件萃取或擴充後才建立的 sourceFieldNames。 targetFieldName 是索引中的欄位。

除了將輸入連結至輸出，您也可以使用欄位對應來將資料結構壓平合併。 如需詳細資訊，請參閱[如何將擴充的欄位對應至可搜尋的索引](cognitive-search-output-field-mapping.md)。

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")),
        new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content")
    };

    List<FieldMapping> outputMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"),
        new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"),
        new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode")
    };

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
將下列數行新增至 `Main`。

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer and executing the pipeline...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

預期建立索引子將需花費一些時間才能完成。 即使資料集很小，分析技能仍需要大量計算。 某些技能 (例如影像分析) 需要長時間執行。

> [!TIP]
> 建立索引子時會叫用管線。 資料的存取、輸入和輸出的對應或作業順序若有問題，都會在這個階段中出現。

### <a name="explore-creating-the-indexer"></a>探索建立索引子

程式碼會將 ```"maxFailedItems"``` 設定為 -1，這會指示索引引擎在資料匯入期間忽略錯誤。 此設定有其用處，因為示範資料來源中只有少量文件。 若要有較大的資料來源，您應將值設定為大於 0。

另請注意，```"dataToExtract"``` 會設定為 ```"contentAndMetadata"```。 此陳述式會指示索引子自動擷取不同檔案格式的內容，以及每個檔案的相關中繼資料。

在擷取內容時，您可以設定 `imageAction`，以從在資料來源中找到的影像擷取文字。 設定為 ```"generateNormalizedImages"``` 組態的 ```"imageAction"``` 可與 OCR 技能和文字合併技能相結合，以指示索引子從影像中擷取文字 (例如，從「停」交通號誌中擷取「停」這個字)，並將其內嵌為內容欄位的一部分。 此行為適用於內嵌在文件中的影像 (例如 PDF 內的影像)，以及在資料來源中找到的影像 (例如 JPG 檔案)。

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - 監視編製索引

索引子經定義後，將會在您提交要求時自動執行。 根據您所定義的認知技能，索引編製所需的時間可能會超出您的預期。 若要了解索引子是否仍在執行，請使用 `GetStatus` 方法。

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo` 表示索引子的目前狀態和執行記錄。

某些來源檔案和技能的組合常會出現警告，這並不一定表示有問題。 在本教學課程中，警告是良性的 (例如，沒有來自 JPEG 檔案的文字輸入)。

將下列數行新增至 `Main`。

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - 搜尋

索引編製完成後，您可以執行會傳回個別欄位內容的查詢。 根據預設，Azure 認知搜尋會傳回前 50 項結果。 範例資料很小，因此預設值即足堪使用。 不過，在使用較大的資料集時，您可能需要在查詢字串中加上參數，以傳回較多結果。 如需相關指示，請參閱[如何在 Azure 認知搜尋中對結果分頁](search-pagination-page-layout.md)。

在驗證步驟中，您會查詢所有欄位的索引。

將下列數行新增至 `Main`。

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` 會使用儲存在應用程式組態檔 (appsettings.json) 中的值，來建立新的 `SearchIndexClient`。 請注意，系統會使用搜尋服務查詢 API 金鑰，而非管理金鑰。

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

將下列程式碼新增至 `Main`。 第一個 try-catch 會傳回索引定義，以及每個欄位的名稱、類型和屬性。 第二個是參數化查詢，其中，`Select` 會指定要包含在結果中的欄位，例如 `organizations`。 `"*"` 的搜尋字串會傳回單一欄位的所有內容。

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

對其他欄位重複前述步驟：此練習中的內容、語言程式碼、關鍵片語和組織。 您可以透過使用逗號分隔清單的 [Select](/dotnet/api/microsoft.azure.search.models.searchparameters.select) 屬性傳回多個欄位。

<a name="reset"></a>

## <a name="reset-and-rerun"></a>重設並重新執行

在開發的早期實驗階段中若要設計反覆項目，最實用的方法是從 Azure 認知搜尋中刪除物件，並讓您的程式碼加以重建。 資源名稱是唯一的。 刪除物件可讓您使用相同的名稱加以重新建立。

本教學課程的範例程式碼會檢查是否有現有的物件，並將其刪除，以便您重新執行程式碼。

您也可以使用入口網站來刪除索引、索引子、資料來源和技能集。

## <a name="takeaways"></a>重要心得

本教學課程示範了藉由建立下列元件組件來建置擴充索引管線的基本步驟：資料來源、技能集、索引和索引子。

[內建技能](cognitive-search-predefined-skills.md)已透過輸入和輸出連同技能集定義和鏈結技能的機制一起導入。 您也已了解在將管線中的擴充值路由至 Azure 認知搜尋服務上的可搜尋索引時，索引子定義中必須要有 `outputFieldMappings`。

最後，您了解到如何測試結果並重設系統，以進行進一步的反覆運算。 您已了解對索引發出查詢，會傳回由擴充的索引管線建立的輸出。 您也已了解如何檢查索引子狀態，以及在重新執行管線之前應刪除哪些物件。

## <a name="clean-up-resources"></a>清除資源

如果您使用自己的訂用帳戶，當專案結束時，建議您移除不再需要的資源。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

## <a name="next-steps"></a>後續步驟

現在您已熟悉 AI 擴充管線中的所有物件，接下來我們將進一步了解技能集定義和個別技能。

> [!div class="nextstepaction"]
> [如何建立技能集](cognitive-search-defining-skillset.md)