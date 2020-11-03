---
title: 管理自訂模型
titleSuffix: Azure Digital Twins
description: 瞭解如何在 Azure 數位 Twins 內建立、編輯和刪除模型。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b31e3d44cc66e97506b29b81cef5b8d981d05e39
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279426"
---
# <a name="manage-azure-digital-twins-models"></a>管理 Azure 數位 Twins 模型

您可以使用 [**DigitalTwinModels api**](/rest/api/digital-twins/dataplane/models)、 [.Net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)或 [azure 數位 Twins CLI](how-to-use-cli.md)來管理 azure 數位 Twins 實例所知道的 [模型](concepts-models.md)。 

管理作業包括上傳、驗證、抓取和刪除模型。 

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-models"></a>建立模型

Azure 數位 Twins 的模型會以 DTDL 撰寫，並儲存為 *json* 檔案。 另外還有[Visual Studio Code](https://code.visualstudio.com/)的[DTDL 延伸](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)模組，可提供語法驗證和其他功能，以協助撰寫 DTDL 檔。

試想一下醫院想要以數位方式代表房間的範例。 每個房間都包含智慧型 soap 分配程式，可供監視手洗手，以及監視透過房間的流量的感應器。

解決方案的第一個步驟是建立模型來代表醫院的各個層面。 此案例中的患者房間可能會描述如下：

```json
{
  "@id": "dtmi:com:contoso:PatientRoom;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "displayName": "Patient Room",
  "contents": [
    {
      "@type": "Property",
      "name": "visitorCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashPercentage",
      "schema": "double"
    },
    {
      "@type": "Relationship",
      "name": "hasDevices"
    }
  ]
}
```

> [!NOTE]
> 這是 json 檔案的範例主體，其中定義和儲存模型，以便上傳做為用戶端專案的一部分。 另一方面，REST API 呼叫會採用類似上述的模型定義陣列， (對應至 `IEnumerable<string>` .NET SDK) 中的。 因此，若要直接在 REST API 中使用此模型，請以括弧括住。

此模型會定義病人房間的名稱和唯一識別碼，並使用屬性來代表訪客計數和手擦洗狀態 (這些計數器將會從動作感應器和智慧型 soap 機更新，並會一起用來計算 *handwash 百分比* 屬性) 。 此模型也會定義關聯性 *hasDevices* ，此關聯性會用來將任何以此 *房間* 模型為基礎的 [數位 twins](concepts-twins-graph.md)連接至實際裝置。

在此方法之後，您可以繼續為醫院的 wards、區域或醫院本身定義模型。

### <a name="validate-syntax"></a>驗證語法

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="manage-models-with-apis"></a>使用 Api 管理模型

下列各節說明如何使用 [Azure 數位 Twins api 和 sdk](how-to-use-apis-sdks.md)完成不同的模型管理作業。

> [!NOTE]
> 為了簡潔起見，下列範例不包含錯誤處理。 不過，強烈建議您在專案內將服務呼叫包裝在 try/catch 區塊中。

> [!TIP] 
> 請記住，所有的 SDK 方法都有同步和非同步版本。 針對分頁呼叫，非同步方法會在 `AsyncPageable<T>` 同步版本傳回時傳回 `Pageable<T>` 。

### <a name="upload-models"></a>上傳模型

建立模型之後，您可以將它們上傳至 Azure 數位 Twins 實例。

> [!TIP]
> 建議您先離線驗證您的模型，然後再將它們上傳至您的 Azure 數位 Twins 實例。 您可以使用 [DTDL 客戶](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) 端剖析器程式庫和 [DTDL 驗證程式範例](/samples/azure-samples/dtdl-validator/dtdl-validator) ， [*方法：剖析和驗證模型*](how-to-parse-models.md) 以檢查您的模型，然後再將它們上傳至服務。

當您準備好上傳模型時，您可以使用下列程式碼片段：

```csharp
// 'client' is an instance of DigitalTwinsClient
// Read model file into string (not part of SDK)
StreamReader r = new StreamReader("MyModelFile.json");
string dtdl = r.ReadToEnd(); r.Close();
string[] dtdls = new string[] { dtdl };
client.CreateModels(dtdls);
```

請注意，此 `CreateModels` 方法會在單一交易中接受多個檔案。 以下是說明的範例：

```csharp
var dtdlFiles = Directory.EnumerateFiles(sourceDirectory, "*.json");

List<string> dtdlStrings = new List<string>();
foreach (string fileName in dtdlFiles)
{
    // Read model file into string (not part of SDK)
    StreamReader r = new StreamReader(fileName);
    string dtdl = r.ReadToEnd(); r.Close();
    dtdlStrings.Add(dtdl);
}
client.CreateModels(dtdlStrings);
```

模型檔案可以包含一個以上的模型。 在此情況下，模型必須放在 JSON 陣列中。 例如：

```json
[
  {
    "@id": "dtmi:com:contoso:Planet",
    "@type": "Interface",
    //...
  },
  {
    "@id": "dtmi:com:contoso:Moon",
    "@type": "Interface",
    //...
  }
]
```
 
上傳時，服務會驗證模型檔案。

### <a name="retrieve-models"></a>取出模型

您可以列出和取出儲存在 Azure 數位 Twins 實例上的模型。 

以下是您可以選擇的選項：
* 取出單一模型
* 取出所有模型
* 取得模型的中繼資料和相依性

以下是一些範例呼叫：

```csharp
// 'client' is a valid DigitalTwinsClient object

// Get a single model, metadata and data
DigitalTwinsModelData md1 = client.GetModel(id);

// Get a list of the metadata of all available models
Pageable<DigitalTwinsModelData> pmd2 = client.GetModels();

// Get models and metadata for a model ID, including all dependencies (models that it inherits from, components it references)
Pageable<DigitalTwinsModelData> pmd3 = client.GetModels(new GetModelsOptions { IncludeModelDefinition = true });
```

用以取得模型的 API 呼叫會傳回 `DigitalTwinsModelData` 物件。 `DigitalTwinsModelData` 包含 Azure 數位 Twins 實例中所儲存之模型的相關中繼資料，例如模型的名稱、DTMI 和建立日期。 `DigitalTwinsModelData`物件也可以選擇性地包含模型本身。 根據參數，您可以使用抓取呼叫來取出中繼資料 (這在您想要顯示可用工具 UI 清單的情況下很有用，例如) 或整個模型。

`RetrieveModelWithDependencies`呼叫不僅會傳回要求的模型，也會傳回要求的模型所相依的所有模型。

模型不一定會以其上傳的檔表單來傳回。 Azure 數位 Twins 只會保證傳回表單在語義上是相等的。 

### <a name="update-models"></a>更新模型

將模型上傳至您的 Azure 數位 Twins 實例之後，整個模型介面都是不可變的。 這表示沒有傳統的「編輯」模型。 Azure 數位 Twins 也不允許重新上傳相同的模型。

相反地，如果您想要變更模型（例如更新或），則請 `displayName` `description` 上傳 **較新版本** 的模型。 

#### <a name="model-versioning"></a>模型版本設定

若要建立現有模型的新版本，請從原始模型的 DTDL 開始。 更新、新增或移除您想要變更的欄位。

接下來，藉由更新模型的欄位，將此標示為較新版本的模型 `id` 。 模型識別碼的最後一個區段（在之後） `;` 表示模型編號。 若要指出這現在是此模型的更新版本，請將值結尾的數位遞增 `id` 到大於目前版本號碼的任何數位。

例如，如果您先前的模型識別碼看起來像這樣：

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

此模型的第2版看起來可能像這樣：

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

然後，將新版本的模型上傳至您的實例。 

然後，您的實例中將會提供此版本的模型，以用於數位 twins。 它不 **會** 覆寫舊版的模型，因此模型的多個版本會並存于您的實例中，直到您將 [它們移除](#remove-models)為止。

#### <a name="impact-on-twins"></a>對 twins 的影響

當您建立新的對應項時，由於新的模型版本和舊模型版本並存，新的對應項可以使用新版本的模型或較舊的版本。

這也表示上傳新版本的模型不會自動影響現有的 twins。 現有的 twins 只會保留舊模型版本的實例。

您可以藉由修補，將這些現有的 twins 更新為新的模型版本，如「 *如何：管理數位 twins* 」的「 [*更新數位對應項的模型*](how-to-manage-twin.md#update-a-digital-twins-model)」一節中所述。 在相同的修補程式中，您必須將 **模型識別碼** (更新為新版本) 以及 **必須在對應項上更改的任何欄位，使其符合新的模型** 。

### <a name="remove-models"></a>移除模型

您也可以透過下列兩種方式之一，從服務中移除模型：
* **解除** 委任：當模型解除委任之後，您就無法再使用它來建立新的數位 twins。 已使用此模型的現有數位 twins 不會受到影響，因此您仍可使用屬性變更和新增或刪除關聯性之類的專案來更新它們。
* **刪除** ：這會將模型從方案中完全移除。 任何使用此模型的 twins 不再與任何有效的模型相關聯，因此會被視為完全沒有模型。 您仍然可以讀取這些 twins，但必須等到重新指派到不同的模型之後，才能在這些上進行任何更新。

這些是不同的功能，它們彼此不會有任何影響，不過它們可以一起使用，以逐漸移除模型。 

#### <a name="decommissioning"></a>退役

以下是解除委任模型的程式碼：

```csharp
// 'client' is a valid DigitalTwinsClient  
client.DecommissionModel(dtmiOfPlanetInterface);
// Write some code that deletes or transitions digital twins
//...
```

模型的解除委任狀態會包含在 `ModelData` 模型抓取 api 所傳回的記錄中。

#### <a name="deletion"></a>刪除

您可以一次刪除實例中的所有模型，也可以在個別的基礎上進行。

如需如何刪除所有模型的範例，請下載教學課程中使用的範例應用程式 [*：使用範例用戶端應用程式探索基本概念*](tutorial-command-line-app.md)。 *CommandLoop.cs* 檔案會在函式中執行此 `CommandDeleteAllModels` 工作。

本節的其餘部分會將模型刪除細分為更詳細的資料，並顯示如何針對個別模型執行此作業。

##### <a name="before-deletion-deletion-requirements"></a>刪除之前：刪除需求

一般來說，您可以隨時刪除模型。

例外狀況是其他模型相依的模型，可能是透過 `extends` 關聯性或元件所決定。 例如，如果 *conferenceroom 等等* 模型延伸了 *房間* 模型，而且有 *ACUnit* 模型做為元件，則您無法刪除 *會議室* 或 *ACUnit* ，直到 *conferenceroom 等等* 移除個別的參考。 

若要這麼做，您可以更新相依模型以移除相依性，或完全刪除相依模型。

##### <a name="during-deletion-deletion-process"></a>刪除期間：刪除進程

即使模型符合要求以立即將其刪除，您可能會想要先完成幾個步驟，以避免 twins 遺留的非預期結果。 以下是可協助您管理進程的一些步驟：
1. 首先，解除委任模型
2. 等候幾分鐘，以確定服務已處理在解除委任之前傳送的任何最後分鐘對應項建立要求
3. 依模型查詢 twins，以查看所有使用 now-解除委任模型的 twins
4. 如果不再需要，請刪除 twins，或視需要將其修補至新的模型。 您也可以選擇保留它們，在這種情況下，一旦刪除模型之後，就會變成沒有模型的 twins。 請參閱下一節，以瞭解此狀態的含意。
5. 等候另幾分鐘的時間，以確定變更已 percolated 至
6. 刪除模型 

若要刪除模型，請使用此呼叫：
```csharp
// 'client' is a valid DigitalTwinsClient
await client.DeleteModelAsync(IDToDelete);
```

##### <a name="after-deletion-twins-without-models"></a>刪除之後：不含模型的 Twins

一旦刪除模型，任何使用模型的數位 twins 現在都會被視為沒有模型。 請注意，沒有可提供此狀態中所有 twins 清單的查詢，不過您仍然 *可以* 依已刪除的模型查詢 twins，以瞭解受影響的 twins。

以下概述沒有模型的 twins 可以使用和無法進行的作業。

您 **可以** 做的事：
* 查詢對應項
* 讀取屬性
* 讀取連出關聯性
* 新增和刪除連入關聯 (如中所示，其他 twins 仍然可以形成 *與* 此對應項的關聯性) 
  - `target`關聯性定義中的會仍可反映已刪除之模型的 DTMI。 未定義目標的關聯性也可以在這裡運作。
* 刪除關聯性
* 刪除對應項

您 **無法** 做的事：
* 編輯外寄關聯 (如中所述，此對應項與其他 twins *的* 關聯性) 
* 編輯屬性

##### <a name="after-deletion-re-uploading-a-model"></a>刪除之後：重新上傳模型

刪除模型之後，您可以稍後再決定上傳與您所刪除之識別碼相同的新模型。 以下是在該情況下所發生的情況。
* 從方案存放區的觀點來看，這與上傳全新的模型相同。 這項服務並不記得先前已上傳舊的服務。   
* 如果圖形中有任何剩餘的 twins 參考已刪除的模型，則不再是孤立的，此模型識別碼會與新的定義再次有效。 但是，如果模型的新定義與已刪除的模型定義不同，則這些 twins 可能會有符合已刪除之定義的屬性和關聯性，而且不是以新的定義有效。

Azure 數位 Twins 不會防止此狀態，因此請小心適當地修補 Twins，以確保它們透過模型定義參數保持有效。

## <a name="manage-models-with-cli"></a>使用 CLI 管理模型

您也可以使用 Azure 數位 Twins CLI 來管理模型。 您可以在 how [*to：使用 Azure 數位 TWINS CLI*](how-to-use-cli.md)中找到這些命令。

## <a name="next-steps"></a>後續步驟

瞭解如何根據您的模型建立和管理數位 twins：
* [*How to：管理數位 twins*](how-to-manage-twin.md)