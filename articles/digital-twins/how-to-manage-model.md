---
title: 管理自訂模型
titleSuffix: Azure Digital Twins
description: 瞭解如何在 Azure 數位 Twins 內建立、編輯和刪除模型。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 64c7db4223fcb703272749b0bf8d5b1583fbb818
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987319"
---
# <a name="manage-azure-digital-twins-models"></a>管理 Azure 數位 Twins 模型

您可以使用[**DigitalTwinsModels api**](how-to-use-apis-sdks.md)、 [.Net (c # ) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或[azure 數位 Twins CLI](how-to-use-cli.md)，來管理您的 Azure 數位 Twins 實例所知道的[模型](concepts-models.md)。 

管理作業包括上傳、驗證、抓取和刪除模型。 

## <a name="create-models"></a>建立模型

Azure 數位 Twins 的模型是以 DTDL 撰寫，並儲存為*json*檔案。 另外還有一個[DTDL 擴充](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)功能可供[Visual Studio Code](https://code.visualstudio.com/)使用，它提供語法驗證和其他功能，有助於撰寫 DTDL 檔。

假設醫院想要以數位代表其房間的範例。 每個房間都包含智慧型 soap 分配程式，可用於監視洗手和感應器，以監視透過房間的流量。

解決方案的第一個步驟是建立模型來代表醫院的各個層面。 此案例中的患者聊天室可能會如下所示：

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
> 這是定義和儲存模型之 json 檔案的範例主體，會在用戶端專案中上傳。 另一方面，REST API 呼叫會採用類似上述的模型定義陣列， (對應至 `IEnumerable<string>` .NET SDK) 中的。 因此，若要直接在 REST API 中使用此模型，請以方括弧括住。

此模型會定義患者聊天室的名稱和唯一識別碼，以及用來代表訪客計數和手動沖蝕狀態的屬性， (這些計數器會從動作感應器和智慧型 soap 配藥更新，並會一起用來計算*handwash 百分比*屬性) 。 此模型也會定義關聯性*hasDevices*，其將用來將以此*房間*模型為基礎的任何[數位 twins](concepts-twins-graph.md)連接到實際裝置。

遵循此方法，您可以繼續為醫院的 wards、區域或醫院本身定義模型。

### <a name="validate-syntax"></a>驗證語法

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="manage-models-with-apis"></a>使用 Api 管理模型。

下列各節說明如何使用[Azure 數位 Twins api 和 sdk](how-to-use-apis-sdks.md)完成不同的模型管理作業。

> [!NOTE]
> 為了簡潔起見，下列範例不包含錯誤處理。 不過，強烈建議您在專案中將服務呼叫包裝在 try/catch 區塊中。

> [!TIP] 
> 請記住，所有 SDK 方法都是以同步和非同步版本提供。 若是分頁呼叫，非同步方法會在 `AsyncPageable<T>` 同步版本傳回時傳回 `Pageable<T>` 。

### <a name="upload-models"></a>上傳模型

建立模型之後，您可以將其上傳至 Azure 數位 Twins 實例。

> [!TIP]
> 建議您先離線驗證您的模型，再將其上傳至您的 Azure 數位 Twins 實例。 在您將模型上傳至服務之前，您可以使用[*如何：剖析和驗證模型*](how-to-parse-models.md)中所述的[DTDL 客戶](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)端剖析器程式庫和[DTDL 驗證器範例](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)來檢查您的模型。

當您準備好上傳模型時，可以使用下列程式碼片段：

```csharp
// 'client' is an instance of DigitalTwinsClient
// Read model file into string (not part of SDK)
StreamReader r = new StreamReader("MyModelFile.json");
string dtdl = r.ReadToEnd(); r.Close();
string[] dtdls = new string[] { dtdl };
client.CreateModels(dtdls);
```

請注意， `CreateModels` 方法會接受單一交易中的多個檔案。 以下是說明的範例：

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

模型檔案可以包含多個單一模型。 在此情況下，模型必須放在 JSON 陣列中。 例如：

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

您可以列出並抓取 Azure 數位 Twins 實例上儲存的模型。 

以下是您可以選擇的選項：
* 取出所有模型
* 取得單一模型
* 取得具有相依性的單一模型
* 取得模型的中繼資料

以下是一些範例呼叫：

```csharp
// 'client' is a valid DigitalTwinsClient object

// Get a single model, metadata and data
ModelData md1 = client.GetModel(id);

// Get a list of the metadata of all available models
Pageable<ModelData> pmd2 = client.GetModels();

// Get a list of metadata and full model definitions
Pageable<ModelData> pmd3 = client.GetModels(null, true);

// Get models and metadata for a model ID, including all dependencies (models that it inherits from, components it references)
Pageable<ModelData> pmd4 = client.GetModels(new string[] { modelId }, true);
```

API 會呼叫以取得模型所有傳回 `ModelData` 的物件。 `ModelData`包含 Azure 數位 Twins 實例中所儲存之模型的相關中繼資料，例如模型的名稱、DTMI 和建立日期。 `ModelData`物件也會選擇性地包含模型本身。 視參數而定，您可以使用抓取呼叫來僅抓取中繼資料 (這在您想要顯示可用工具的 UI 清單（例如) ）或整個模型的案例中很有用。

`RetrieveModelWithDependencies`呼叫不僅會傳回要求的模型，也會傳回要求的模型所相依的所有模型。

模型不一定會以其上傳的檔表單來傳回。 Azure 數位 Twins 只保證傳回表單在語義上會是相等的。 

### <a name="remove-models"></a>移除模型

您也可以透過下列兩種方式的其中一種，從服務中移除模型：
* **解除**委任：一旦模型解除委任之後，您就無法再使用它來建立新的數位 twins。 已經使用此模型的現有數位 twins 不會受到影響，因此您仍然可以使用屬性變更和新增或刪除關聯性等專案來更新它們。
* **刪除**：這會將模型完全從方案中移除。 任何使用此模型的 twins 不再與任何有效的模型相關聯，因此會被視為完全沒有模型。 您仍然可以閱讀這些 twins，但在重新指派至不同的模型之前，將無法對其進行任何更新。

這些是不同的功能，而且不會影響彼此，雖然它們可以一起使用以逐漸移除模型。 

### <a name="decommissioning"></a>解除委任

以下是解除委任模型的程式碼：

```csharp
// 'client' is a valid DigitalTwinsClient  
client.DecommissionModel(dtmiOfPlanetInterface);
// Write some code that deletes or transitions digital twins
//...
```

模型的解除委任狀態會包含在 `ModelData` 模型抓取 api 所傳回的記錄中。

#### <a name="deletion"></a>刪除

您可以一次刪除實例中的所有模型，也可以個別執行此動作。

如需如何刪除所有模型的範例，請下載[*教學課程：使用範例用戶端應用程式探索基本概念*](tutorial-command-line-app.md)中所使用的範例應用程式。 *CommandLoop.cs*檔案會在函式中執行這項 `CommandDeleteAllModels` 工作。

本節的其餘部分會將模型刪除細分為更詳細的資料，並示範如何針對個別模型執行此作業。

##### <a name="before-deletion-deletion-requirements"></a>刪除之前：刪除需求

一般而言，可以隨時刪除模型。

例外狀況是其他模型相依的模型，不論是透過 `extends` 關聯性或元件。 例如，如果*conferenceroom 等等*模型延伸了*房間*模型，而且具有*ACUnit*模型做為元件，您就無法刪除*會議室*或*ACUnit* ，直到*conferenceroom 等等*移除那些個別的參考為止。 

若要執行這項操作，您可以更新相依模型以移除相依性，或完全刪除相依模型。

##### <a name="during-deletion-deletion-process"></a>刪除期間：刪除進程

即使模型符合要立即刪除的需求，您仍可能想要先逐一執行幾個步驟，以避免 twins 留下的非預期結果。 以下是一些可協助您管理程式的步驟：
1. 首先，解除委任模型
2. 等候幾分鐘的時間，以確定服務已處理在解除委任之前傳送的任何最後分鐘對應項建立要求
3. 依模型查詢 twins，以查看所有使用現已解除委任之模型的 twins
4. 如果您不再需要 twins，請將其刪除，或視需要將其修補為新的模型。 您也可以選擇保留它們，在這種情況下，在刪除模型之後，就不需要模型就會 twins。 如需此狀態的含意，請參閱下一節。
5. 等待另幾分鐘的時間，確定變更已 percolated 到
6. 刪除模型 

若要刪除模型，請使用此呼叫：
```csharp
// 'client' is a valid DigitalTwinsClient
await client.DeleteModelAsync(IDToDelete);
```

##### <a name="after-deletion-twins-without-models"></a>刪除之後：不含模型的 Twins

刪除模型之後，任何使用該模型的數位 twins 現在都會被視為沒有模型。 請注意，沒有任何查詢可以提供您處於此狀態的所有 twins 清單，雖然您仍然*可以*查詢已刪除模型的 twins，以知道哪些 twins 受到影響。

以下概述不具有模型的 twins，您可以和無法執行的工作。

您**可以**做的事：
* 查詢對應項
* 讀取屬性
* 讀取連出關聯性
* 新增和刪除傳入關聯性 (中的，其他 twins 仍然可以形成*與*此對應項的關聯性) 
  - `target`關聯性定義中的，仍然可以反映已刪除之模型的 DTMI。 未定義目標的關聯性也可以在這裡工作。
* 刪除關聯性
* 刪除對應項

您**不能**做的事：
* 編輯傳出關聯性 (，如同在中，此對應項與其他*twins 的關聯*性) 
* 編輯屬性

##### <a name="after-deletion-re-uploading-a-model"></a>刪除之後：重新上傳模型

刪除模型之後，您可能會決定使用與您所刪除的相同的識別碼來上傳新模型。 以下是在此情況下會發生的情況。
* 從解決方案存放區的觀點來看，這與上傳全新的模型相同。 服務並不記得先前已上傳過舊的服務。   
* 如果圖表中有任何剩餘的 twins 參考已刪除的模型，它們就不再是孤立的;此模型識別碼會使用新的定義再次有效。 不過，如果模型的新定義與已刪除的模型定義不同，則這些 twins 可能會有符合已刪除之定義的屬性和關聯性，而且不是新的。

Azure 數位 Twins 不會防止此狀態，因此請小心適當修補 Twins，以確保它們可以透過模型定義參數保持有效。

## <a name="manage-models-with-cli"></a>使用 CLI 管理模型

您也可以使用 Azure 數位 Twins CLI 來管理模型。 您可以在[*如何：使用 Azure 數位 TWINS CLI*](how-to-use-cli.md)中找到這些命令。

## <a name="next-steps"></a>後續步驟

請參閱如何根據您的模型建立和管理數位 twins：
* [*如何：管理數位 twins*](how-to-manage-twin.md)