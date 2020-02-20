---
title: 教學課程： C#使用 .net 建立技能集
titleSuffix: Azure Cognitive Search
description: 逐步執行範例程式碼，以顯示 Azure 認知搜尋擴充索引管線中的資料提取、自然語言和影像 AI 處理。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efd4a9333b5fb02c18b2f6a6d0f8ce58bfb8f220
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472378"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-c-and-the-net-sdk"></a>教學課程：使用C#和 .Net SDK 建立 AI 擴充管線

在本教學課程中，您將了解在 Azure 認知搜尋中使用*認知技能*進行資料擴充程式設計的機制。 技能會受到自然語言處理 (NLP) 與認知服務中的映像分析功能所支援。 透過技能組合和設定，您可以擷取文字，以及映像或所掃描文件檔案的文字表示法。 您也可以偵測語言、實體、關鍵片語等。 其最終結果是，AI 支援的索引管線會在搜尋索引中建立豐富的額外內容。

在此教學課程中，您將使用 .NET SDK 來執行下列工作：

> [!div class="checklist"]
> * 建立對索引路由中的範例資料進行擴充的索引管線
> * 套用內建技能：光學字元辨識、文字合併、語言偵測、文字分割、實體辨識、關鍵片語擷取
> * 了解如何藉由將技能集的輸入對應至輸出，將多項技術串聯在一起
> * 執行要求並檢閱結果
> * 重設索引和索引子以進行進一步開發

輸出是 Azure 認知搜尋可全文檢索搜尋的索引。 您可以使用其他標準功能來強化索引，例如[同義字](search-synonyms.md)、[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[分析器](search-analyzers.md)和[篩選](search-filters.md)。

本教學課程雖然在免費服務上執行，但可用的交易數目限制為每日 20 份文件。 如果您想要在同一天內多次執行本教學課程，請刪除索引子以重設計數器。

> [!NOTE]
> 當您藉由增加處理次數、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須連結可計費的認知服務資源。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務隨用隨附價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

本教學課程會使用下列服務、工具和資料。 

+ [建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)，以儲存範例資料。 確定儲存體帳戶位於與 Azure 認知搜尋相同的區域中。

+ 下載由不同類型小型檔案集組成的[範例資料](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)。 

+ [安裝 Visual Studio](https://visualstudio.microsoft.com/) 以當作 IDE 使用。

+ [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本教學課程的免費服務。

## <a name="get-a-key-and-url"></a>取得金鑰和 URL

若要與 Azure 認知搜尋服務互動，您需要服務 URL 和存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀] 頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定] >  [金鑰] 中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

   ![取得 HTTP 端點和存取金鑰](media/search-get-started-postman/get-url-key.png "取得 HTTP 端點和存取金鑰")

擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="prepare-sample-data"></a>準備範例資料

擴充管線會從 Azure 資料來源中提取資料。 來源資料必須來自 [Azure 認知搜尋索引子](search-indexer-overview.md)支援的資料來源類型。 針對此練習，我們會使用 Blob 儲存體來展現多個內容類型。

1. [登入 Azure 入口網站](https://portal.azure.com)瀏覽至您的 Azure 儲存體帳戶、按一下 [Blob]，然後按一下 [+ 容器]。

1. [建立 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)以容納範例資料。 您可以將公用存取層級設定為任何有效值。 此教學課程假設容器名稱為 "basic-demo-data-pr"。

1. 建立容器之後，請開啟它，然後選取命令列上的 [上傳] 來上傳[樣本資料](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)。

   ![Azure Blob 儲存體中的來源檔案](./media/cognitive-search-quickstart-blob/sample-data.png)

1. 範例檔案載入之後，請取得 Blob 儲存體的容器名稱和連接字串。 您可以透過在 Azure 入口網站中瀏覽至儲存體帳戶、選取 [存取金鑰]，然後複製 [連接字串] 欄位來執行此動作。

   連接字串應為類似於下列範例的 URL：

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

此外也有其他方式可指定連接字串，例如提供共用存取簽章。 若要深入了解資料來源認證，請參閱[編製 Azure Blob 儲存體的索引](search-howto-indexing-azure-blob-storage.md#Credentials)。

## <a name="set-up-your-environment"></a>設定您的環境

一開始請開啟 Visual Studio，並建立可在 .NET Core 上執行的新主控台應用程式專案。

### <a name="install-nuget-packages"></a>安裝 NuGet 套件

[Azure 認知搜尋 .NET SDK](https://aka.ms/search-sdk) 包含數個用戶端程式庫，可讓您管理索引、資料來源、索引子及技能集，以及上傳和管理文件，還可以執行查詢，而且一律不需要處理 HTTP 和 JSON 的細節。 這些用戶端程式庫都以 NuGet 套件來散發。

針對此專案，您必須安裝 `Microsoft.Azure.Search` NuGet 套件的版本 9 與最新的 `Microsoft.Extensions.Configuration.Json` NuGet 套件。

在 Visual Studio 中，使用套件管理員主控台來安裝 `Microsoft.Azure.Search` NuGet 套件。 若要開啟套件管理員主控台，請選取 [工具] > [NuGet 套件管理員] > [套件管理員主控台]。 若要取得要執行的命令，請瀏覽至 [Microsoft.Azure.Search NuGet 套件頁面](https://www.nuget.org/packages/Microsoft.Azure.Search) \(英文\)、選取版本 9，然後複製套件管理員命令。 在套件管理員主控台中，執行此命令。

若要在 Visual Studio 中安裝 `Microsoft.Extensions.Configuration.Json` NuGet 套件，請選取 **工具** >  **NuGet 套件管理員**， > **管理方案的 nuget 套件**...。選取 流覽 並搜尋 `Microsoft.Extensions.Configuration.Json` NuGet 套件。 一旦找到它之後，請選取該套件、選取您的專案、確認該版本為最新的穩定版本，然後選取 [安裝]。

## <a name="add-azure-cognitive-search-service-information"></a>新增 Azure 認知搜尋服務資訊

若要連線到您的 Azure 認知搜尋服務，您必須將搜尋服務資訊新增至專案。 在 [方案總管] 中以滑鼠右鍵按一下您的專案，然後選取 [新增] > [新增項目]。 將檔案命名為 `appsettings.json`，然後選取 [新增]。 

此檔案必須包含於您的輸出目錄中。 若要執行此動作，請以滑鼠右鍵按一下 `appsettings.json`，然後選取 [屬性]。 將 [**複製到輸出目錄**] 的值變更為 [**有更新時才複製**]。

將下列 JSON 複製到新的 JSON 檔案。

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

新增您的搜尋服務與 Blob 儲存體帳戶資訊。

您可以在 Azure 入口網站，從搜尋帳戶頁面中取得搜尋服務資訊。 帳戶名稱將位於主頁面上，而您可以透過選取 [金鑰] 來尋找金鑰。

您可以透過在 Azure 入口網站中瀏覽至儲存體帳戶、選取 [存取金鑰]，然後複製 [連接字串] 欄位來取得 Blob 連接字串。

## <a name="add-namespaces"></a>新增命名空間

此教學課程會使用許多來自各種命名空間的不同類型。 若要使用那些類型，請將下列內容新增至 `Program.cs`。

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>建立用戶端

建立 `SearchServiceClient` 類別的執行個體。

```csharp
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
> 

## <a name="create-a-data-source"></a>建立資料來源

呼叫 `DataSource` 來建立新的 `DataSource.AzureBlobStorage` 執行個體。 `DataSource.AzureBlobStorage` 要求您指定資料來源名稱、連接字串和 Blob 容器名稱。

雖然不會在此教學課程中使用它，但也會定義虛刪除原則，用以根據虛刪除資料行的值來識別已刪除的 Blob。 如果 Blob 具有值為 `IsDeleted` 的中繼資料屬性 `true`，下列原則就會認為要刪除該 Blob。

```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

既然您已將 `DataSource` 物件初始化，請建立資料來源。 `SearchServiceClient` 具有 `DataSources` 屬性。 此屬性會提供您建立、列出、更新或刪除 Azure 認知搜尋資料來源所需的所有方法。

如果要求成功，方法將傳回已建立的資料來源。 如果要求發生問題 (例如無效的參數)，則方法將擲回例外狀況。

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

由於這是您第一個要求，請查看 Azure 入口網站，以確認已在 Azure 認知搜尋中建立資料來源。 在搜尋服務儀表板頁面上，確認 [資料來源] 圖格有新的項目。 您可能需要等候幾分鐘，讓入口網站重新整理頁面。

  ![入口網站中的資料來源圖格](./media/cognitive-search-tutorial-blob/data-source-tile.png "入口網站中的資料來源圖格")

## <a name="create-a-skillset"></a>建立技能集

在此節中，您會定義一組要套用至資料的擴充步驟。 每個擴充步驟均稱為一個「技能」，而一組擴充步驟會稱為一個「技能集」。 本教學課程會使用為技能集[內建的認知技能](cognitive-search-predefined-skills.md)：

+ [光學字元辨識](cognitive-search-skill-ocr.md)可辨識影像檔案中的列印和手寫文字。

+ [文字合併](cognitive-search-skill-textmerger.md)可將一組欄位中的文字合併成單一欄位。

+ [語言偵測](cognitive-search-skill-language-detection.md)，用以識別內容的語言。

+ [文字分割](cognitive-search-skill-textsplit.md)，用以在呼叫關鍵片語擷取技能和實體辨識技能之前，將大型內容分成較小的區塊。 關鍵片語擷取和實體辨識接受 50,000 個字元 (含) 以下的輸入。 有些範例檔案需要進行分割，以符合這項限制。

+ [實體辨識](cognitive-search-skill-entity-recognition.md)，用以從 Blob 容器中的內容擷取組織名稱。

+ [關鍵片語擷取](cognitive-search-skill-keyphrases.md)，用以提取最高排名的關鍵片語。

在最初處理期間，Azure 認知搜尋會萃取每份文件，以讀取不同檔案格式的內容。 找到來自來源檔案的文字時，會將文字放入產生的 ```content``` 欄位中，每份文件一個欄位。 因此，將輸入設定為 ```"/document/content"``` 以使用此文字。 

輸出可以對應至索引、作為下游技能的輸入，或在使用語言代碼時同時作為對應和輸入。 在索引中，語言代碼可用於篩選。 作為輸入時，文字分析技能會使用語言代碼指出斷字方面的語言規則。

如需技能集基本概念的詳細資訊，請參閱[如何定義技能集](cognitive-search-defining-skillset.md)。

### <a name="ocr-skill"></a>OCR 技術

**OCR** 技能可從影像中擷取文字。 此技能假設 normalized_images 欄位存在。 為產生此欄位，我們會在此教學課程稍後，於索引子定義中將 ```"imageAction"``` 設定設為 ```"generateNormalizedImages"```。

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "image",
    source: "/document/normalized_images/*"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "text",
    targetName: "text"));

OcrSkill ocrSkill = new OcrSkill(
    description: "Extract text (plain and structured) from image",
    context: "/document/normalized_images/*",
    inputs: inputMappings,
    outputs: outputMappings,
    defaultLanguageCode: OcrSkillLanguage.En,
    shouldDetectOrientation: true);
```

### <a name="merge-skill"></a>合併技能

在此節中，您將建立**合併**技能，來合併文件內容欄位與 OCR 技能所產生的文字。

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/content"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "itemsToInsert",
    source: "/document/normalized_images/*/text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "offsets",
    source: "/document/normalized_images/*/contentOffset"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "mergedText",
    targetName: "merged_text"));

MergeSkill mergeSkill = new MergeSkill(
    description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    insertPreTag: " ",
    insertPostTag: " ");
```

### <a name="language-detection-skill"></a>語言偵測技能

**語言偵測**技能會偵測輸入文字的語言，並針對要求所提交的每份文件回報單一語言代碼。 我們將使用**語言偵測**技能的輸出作為**文字分割**技能輸入的一部分。

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "languageCode",
    targetName: "languageCode"));

LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
    description: "Detect the language used in the document",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="text-split-skill"></a>文字分割技能

下列**分割**技能將依頁面分割文字，並按照 `String.Length` 所測量的來將其限制為 4,000 個字元。 此演算法會嘗試將文字分割成區塊，且區塊大小最多為 `maximumPageLength`。 在此案例中，演算法將盡量在例句邊界斷句，好讓區塊大小稍微小於 `maximumPageLength`。

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "textItems",
    targetName: "pages"));

SplitSkill splitSkill = new SplitSkill(
    description: "Split content into pages",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    textSplitMode: TextSplitMode.Pages,
    maximumPageLength: 4000);
```

### <a name="entity-recognition-skill"></a>實體辨識技能

此 `EntityRecognitionSkill` 執行個體會設定來辨識類別類型 `organization`。 **實體辨識**技能也可以辨識類別類型 `person` 和 `location`。

請注意，[內容] 欄位會設定為附有星號的 ```"/document/pages/*"```，這表示會在 ```"/document/pages"``` 下方針對每個頁面呼叫擴充步驟。

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
    
List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "organizations",
    targetName: "organizations"));

List<EntityCategory> entityCategory = new List<EntityCategory>();
entityCategory.Add(EntityCategory.Organization);
    
EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
    description: "Recognize organizations",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings,
    categories: entityCategory,
    defaultLanguageCode: EntityRecognitionSkillLanguage.En);
```

### <a name="key-phrase-extraction-skill"></a>關鍵片語擷取技能

如同剛建立的 `EntityRecognitionSkill` 執行個體，會針對文件的每個頁面呼叫**關鍵片語擷取**技能。

```csharp
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
```

### <a name="build-and-create-the-skillset"></a>建置和建立技能集

使用您建立的技能來建置 `Skillset`。

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

在您的搜尋服務中建立技能集。

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>建立索引

在本節中，您會藉由指定要在可搜尋索引中包含哪些欄位以及每個欄位的搜尋屬性，來定義索引結構描述。 欄位具有類型，並且可取用決定如何使用欄位 (可搜尋、可排序等等) 的屬性。 索引中的欄位名稱不需要完全符合來源中的欄位名稱。 在後續步驟中，您可以在索引子中新增欄位對應以連接來源-目的地欄位。 針對此步驟，請使用與搜尋應用程式相關的欄位命名慣例來定義索引。

此練習會使用下列欄位和欄位類型：

| 欄位名稱： | `id`       | content   | languageCode | keyPhrases         | 組織     |
|--------------|----------|-------|----------|--------------------|-------------------|
| 欄位類型： | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>建立 DemoIndex 類別

此索引的欄位會使用模型類別來定義。 每個模型類別的屬性皆具有屬性，會判斷對應索引欄位的搜尋相關行為。 

我們會將模型類別新增至新的 C# 檔案。 以滑鼠右鍵按一下您的專案，然後選取 [新增] > [新增項目]、選取 [類別] 並將檔案命名為 `DemoIndex.cs`，然後選取 [新增]。

請務必指出您想要使用來自 `Microsoft.Azure.Search` 和 `Microsoft.Azure.Search.Models` 命名空間的類型。

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

將下列模型類別定義新增至 `DemoIndex.cs`，然後將它包含於您將建立索引的相同命名空間。

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Cognitive Search .NET SDK.
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
```

既然您已定義模型類別，請返回 `Program.cs`，您現在可以非常輕鬆地建立索引定義。 此索引的名稱將會是 "demoindex"。

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

在測試期間，您可能發現您正嘗試多次建立索引。 基於此緣故，請檢查以查看您即將建立的索引是否已經存在，然後再嘗試建立它。

```csharp
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
    // Handle exception
}
```

若要深入了解如何定義索引，請參閱[建立索引 (Azure 認知搜尋 REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)。

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>建立索引子、對應欄位，並執行轉換

至此，您已建立資料來源、技能集和索引。 這三項元件構成了[索引子](search-indexer-overview.md)的一部分，可將各項資料一併提取到多階段的單一作業中。 若要在索引子中結合這些項目，您必須定義欄位對應。

+ fieldMappings 會在技能集之前進行處理，用來將資料來源中的來源欄位對應到索引中的目標欄位。 如果兩端上的欄位名稱和類型都相同，則不需要任何對應。

+ outputFieldMappings 會在技能集之後進行處理，用來參考文件萃取或擴充後才建立的 sourceFieldNames。 targetFieldName 是索引中的欄位。

除了將輸入連結至輸出，您也可以使用欄位對應來將資料結構壓平合併。 如需詳細資訊，請參閱[如何將擴充的欄位對應至可搜尋的索引](cognitive-search-output-field-mapping.md)。

```csharp
IDictionary<string, object> config = new Dictionary<string, object>();
config.Add(
    key: "dataToExtract",
    value: "contentAndMetadata");
config.Add(
    key: "imageAction",
    value: "generateNormalizedImages");

List<FieldMapping> fieldMappings = new List<FieldMapping>();
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "metadata_storage_path",
    targetFieldName: "id",
    mappingFunction: new FieldMappingFunction(
        name: "base64Encode")));
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "content",
    targetFieldName: "content"));

List<FieldMapping> outputMappings = new List<FieldMapping>();
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/organizations/*",
    targetFieldName: "organizations"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/keyPhrases/*",
    targetFieldName: "keyPhrases"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/languageCode",
    targetFieldName: "languageCode"));

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
    // Handle exception
}
```

預期建立索引子將需花費一些時間才能完成。 即使資料集很小，分析技能仍需要大量計算。 某些技能 (例如影像分析) 需要長時間執行。

> [!TIP]
> 建立索引子時會叫用管線。 資料的存取、輸入和輸出的對應或作業順序若有問題，都會在這個階段中出現。

### <a name="explore-creating-the-indexer"></a>探索建立索引子

程式碼會將 ```"maxFailedItems"``` 設定為 -1，這會指示索引引擎在資料匯入期間忽略錯誤。 此設定有其用處，因為示範資料來源中只有少量文件。 若要有較大的資料來源，您應將值設定為大於 0。

另請注意，```"dataToExtract"``` 會設定為 ```"contentAndMetadata"```。 此陳述式會指示索引子自動擷取不同檔案格式的內容，以及每個檔案的相關中繼資料。

在擷取內容時，您可以設定 `imageAction`，以從在資料來源中找到的影像擷取文字。 設定為 ```"imageAction"``` 組態的 ```"generateNormalizedImages"``` 可與 OCR 技能和文字合併技能相結合，以指示索引子從影像中擷取文字 (例如，從「停」交通號誌中擷取「停」這個字)，並將其內嵌為內容欄位的一部分。 此行為適用於內嵌在文件中的影像 (例如 PDF 內的影像)，以及在資料來源中找到的影像 (例如 JPG 檔案)。

## <a name="check-indexer-status"></a>檢查索引子狀態

索引子經定義後，將會在您提交要求時自動執行。 根據您所定義的認知技能，索引編製所需的時間可能會超出您的預期。 若要了解索引子是否仍在執行，請使用 `GetStatus` 方法。

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
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
    // Handle exception
}
```

`IndexerExecutionInfo` 表示索引子的目前狀態和執行記錄。

某些來源檔案和技能的組合常會出現警告，這並不一定表示有問題。 在本教學課程中，警告是良性的 (例如，沒有來自 JPEG 檔案的文字輸入)。
 
## <a name="query-your-index"></a>查詢您的索引

索引編製完成後，您可以執行會傳回個別欄位內容的查詢。 根據預設，Azure 認知搜尋會傳回前 50 項結果。 範例資料很小，因此預設值即足堪使用。 不過，在使用較大的資料集時，您可能需要在查詢字串中加上參數，以傳回較多結果。 如需相關指示，請參閱[如何在 Azure 認知搜尋中對結果分頁](search-pagination-page-layout.md)。

在驗證步驟中，您會查詢所有欄位的索引。

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
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

輸出將是索引結構描述，附有每個欄位的名稱、類型和屬性。

提交第二個 `"*"` 查詢，以傳回單一欄位的所有內容，例如 `organizations`。

```csharp
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

對其他欄位重複前述步驟：此練習中的內容、語言程式碼、關鍵片語和組織。 您可以透過使用逗號分隔清單的 `$select` 傳回多個欄位。

<a name="reset"></a>

## <a name="reset-and-rerun"></a>重設並重新執行

在開發的早期實驗階段中若要設計反覆項目，最實用的方法是從 Azure 認知搜尋中刪除物件，並讓您的程式碼加以重建。 資源名稱是唯一的。 刪除物件可讓您使用相同的名稱加以重新建立。

此教學課程會負責檢查現有的索引子和索引，如果它們已經存在，即會將它們刪除，讓您能夠重新執行您的程式碼。

您也可以使用入口網站來刪除索引、索引子和技能集。

當您的程式碼成熟時，您可以精簡重建策略。 如需詳細資訊，請參閱[如何重建索引](search-howto-reindex.md)。

## <a name="takeaways"></a>重要心得

本教學課程示範了藉由建立下列元件組件來建置擴充索引管線的基本步驟：資料來源、技能集、索引和索引子。

[內建技能](cognitive-search-predefined-skills.md)已透過輸入和輸出連同技能集定義和鏈結技能的機制一起導入。 您也已了解在將管線中的擴充值路由至 Azure 認知搜尋服務上的可搜尋索引時，索引子定義中必須要有 `outputFieldMappings`。

最後，您了解到如何測試結果並重設系統，以進行進一步的反覆運算。 您已了解對索引發出查詢，會傳回由擴充的索引管線建立的輸出。 您也已了解如何檢查索引子狀態，以及在重新執行管線之前應刪除哪些物件。

## <a name="clean-up-resources"></a>清除資源

在完成教學課程後，最快速的清除方式是刪除包含 Azure 認知搜尋服務和 Azure Blob 服務的資源群組。 如果您將這兩項服務放在相同群組中，此時刪除資源群組，將會永久刪除其中的所有內容，包括服務與您為此教學課程建立的任何已儲存內容。 在入口網站中，資源群組名稱位在每個服務的 [概觀] 頁面上。

## <a name="next-steps"></a>後續步驟

以自訂技能自訂或擴充管線。 建立自訂技能並將其新增至技能集，以便讓您自行撰寫的文字或影像分析上線。

> [!div class="nextstepaction"]
> [範例：建立 AI 擴充的自訂技能](cognitive-search-create-custom-skill-example.md)
