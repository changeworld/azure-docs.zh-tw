---
title: 非同步重新整理 Azure Analysis Services 模型 | Microsoft Docs
description: 說明如何使用 Azure Analysis Services REST API 來程式碼非同步重新整理模型資料。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 0a5a7ac7d830cb03b1370c31d7e854f3b2a5a2fc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507177"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>使用 REST API 進行非同步重新整理

藉由使用任何支援 REST 呼叫的程式設計語言，您可以在 Azure Analysis Services 表格式模型上執行非同步資料重新整理作業。 這包括相應放大查詢的唯讀複本同步處理。 

資料重新整理作業可能需要一些時間，視許多因素而定，包括資料量、使用分割區的優化層級等等。這些作業傳統上是使用[TOM](https://docs.microsoft.com/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) （表格式物件模型）、 [PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) Cmdlet 或[TMSL](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) （表格式模型指令碼語言）來叫用現有的方法。 不過，這些方法可能需要通常不太可靠的長時間執行 HTTP 連線。

Azure Analysis Services 的 REST API 可讓資料重新整理作業以非同步方式進行。 使用 REST API，便不需要來自用戶端應用程式的長時間執行 HTTP 連線。 針對可靠性還有其他內建的功能，例如自動重試次數、批次認可。

## <a name="base-url"></a>基底 URL

基底 URL 遵循以下格式：

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

例如，在名為 `myserver` 的伺服器（位於「美國西部」 Azure 區域）上，考慮名為 AdventureWorks 的模型。 伺服器名稱是：

```
asazure://westus.asazure.windows.net/myserver 
```

此伺服器名稱的基底 URL 是：

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

藉由使用基底 URL，可以根據下列參數附加資源和作業： 

![非同步重新整理](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- 任何以 **s** 結尾的項目是集合。
- 任何以 **()** 結尾的項目是函式。
- 任何其他項目是資源/物件。

例如，您可以對重新整理集合使用 POST 動詞以執行重新整理作業：

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>驗證

所有呼叫必須使用授權標頭中的有效 Azure Active Directory (OAuth 2) 權杖進行驗證，而且必須符合下列需求：

- 權杖必須是使用者權杖或應用程式服務主體。
- 權杖的對象必須正確設定為 `https://*.asazure.windows.net`。
- 使用者或應用程式必須具有對伺服器或模型足夠的權限，才能執行要求的呼叫。 權限層級由模型中的角色或伺服器上的系統管理員群組決定。

    > [!IMPORTANT]
    > 現階段需要**伺服器管理員**角色權限。

## <a name="post-refreshes"></a>POST /refreshes

若要執行重新整理作業，對 /refreshes 集合使用 POST 動詞以在集合中新增重新整理項目。 回應中的位置標頭 (Location) 包含重新整理識別碼。 用戶端應用程式可以中斷連線，如果必要可在稍後再檢查狀態，因為這是非同步的。

模型一次只會接受一個重新整理作業。 如果目前正在執行重新整理作業時又送出另一個，會傳回 409 衝突 HTTP 狀態碼。

主體大概像這樣：

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>參數

不一定要指定參數。 會套用預設值。

| 名稱             | 類型  | 描述  |預設  |
|------------------|-------|--------------|---------|
| `Type`           | 列舉  | 要執行的處理類型。 Type 對應於 TMSL 的 [refresh 命令](https://docs.microsoft.com/analysis-services/tmsl/refresh-command-tmsl)類型：full、clearValues、calculate、dataOnly、automatic 和 defragment。 不支援 Add 類型。      |   automatic      |
| `CommitMode`     | 列舉  | 決定物件要批次認可或只在完成時認可。 CommitMode 包括：default、transactional、partialBatch。  |  transactional       |
| `MaxParallelism` | Int   | 這個值決定了可以平行執行處理命令的執行緒數目上限。 此值與 MaxParallelism 屬性對應，後者可以在 TMSL 的 [sequence 命令](https://docs.microsoft.com/analysis-services/tmsl/sequence-command-tmsl)中設定，或使用其他方法設定。       | 10        |
| `RetryCount`     | Int   | 表示作業失敗之前重試的次數。      |     0    |
| `Objects`        | Array | 要處理的物件陣列。 每個物件包含：「資料表」(處理整份資料表時)，或「資料表」和「分割區」(處理資料分割時)。 如未指定物件，會重新整理整個模型。 |   處理整個模型      |

CommitMode 等於 partialBatch。 當進行大型資料集的初始載入需要數小時時，會使用它。 如果在成功認可一或多個批次之後，重新整理作業失敗，已成功認可的批次會保留認可 (不會回復已成功認可的批次)。

> [!NOTE]
> 在本文撰寫之際，批次大小是 MaxParallelism 值，但此值無法變更。

### <a name="status-values"></a>狀態值

|狀態值  |描述  |
|---------|---------|
|`notStarted`    |   操作尚未啟動。      |
|`inProgress`     |   作業進行中。      |
|`timedOut`     |    作業因使用者指定的超時時間而超時。     |
|`cancelled`     |   使用者或系統已取消操作。      |
|`failed`     |   作業失敗。      |
|`succeeded`      |   作業成功。      |

## <a name="get-refreshesrefreshid"></a>取得/refreshes/\<refreshId>

若要檢查重新整理作業的狀態，請對重新整理識別碼使用 GET 動詞命令。 以下是回應主體的範例。 如果作業正在進行中， `inProgress` 則會傳回狀態。

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>POST /refreshes

若要取得模型過去的重新整理作業清單，對 /refreshes 集合使用 GET 動詞。 以下是回應主體的範例。 

> [!NOTE]
> 在本文撰寫之際，系統會儲存並傳回過去 30 天的重新整理作業，但此數字無法變更。

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-07T02:06:57.1838734Z",
        "endTime": "2017-12-07T02:07:00.4929675Z",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T01:05:54.157324Z",
        "endTime": "2017-12-07T01:05:57.353371Z",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>刪除/refreshes/\<refreshId>

若要取消進行中的重新整理作業，請對重新整理識別碼使用 DELETE 動詞。

## <a name="post-sync"></a>POST /sync

執行重新整理作業之後，您可能需要將新資料與查詢向外延展的複本同步處理。若要執行模型的同步處理作業，請在/sync 函數上使用 POST 動詞。 回應中的位置標頭 (Location) 包含重新整理作業的識別碼。

## <a name="get-sync-status"></a>GET /sync status

若要查看同步處理作業的狀態，請使用 GET 動詞，並傳遞作業識別碼作為參數。 以下是回應主體的範例：

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

`syncstate` 的值：

- 0：複寫。 資料庫檔案會被複寫到目標資料夾。
- 1：重新序列化。 只有唯讀伺服器執行個體上的資料庫會被序列化。
- 2：完成。 同步處理作業順利完成。
- 3：失敗。 同步處理作業失敗。
- 4：正在結束。 同步處理作業已完成，但正在執行清除步驟。

## <a name="code-sample"></a>程式碼範例

[GitHub 上的 RestApiSample](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample) 是 C# 程式碼範例，可協助您開始。

### <a name="to-use-the-code-sample"></a>使用程式碼範例

1.    複製或下載存放庫。 開啟 RestApiSample 解決方案。
2.    找到 **client.BaseAddress = …** 這一行 並提供您的[基底 URL](#base-url)。

此程式碼範例會使用[服務主體](#service-principal)驗證。

### <a name="service-principal"></a>服務主體

如需關於如何在 Azure 中設定服務主體及指派必要權限的詳細資訊，請參閱[建立服務主體 - Azure 入口網站](../active-directory/develop/howto-create-service-principal-portal.md)和[將服務主體新增至伺服器管理員角色](analysis-services-addservprinc-admins.md)。 完成這些步驟後，請完成下列額外步驟：

1.    在程式碼範例中，尋找 [**字串授權單位 = ...**]，將**common**取代為貴組織的租使用者識別碼。
2.    註解/取消註解，以便使用 ClientCredential 類別來具現化認證物件。 確保 \<App ID> 和 \<App Key> 值會以安全的方式存取，或針對服務主體使用以憑證為基礎的驗證。
3.    執行範例。


## <a name="see-also"></a>另請參閱

[範例](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


