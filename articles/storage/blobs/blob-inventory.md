---
title: '使用 Azure 儲存體清查來管理 blob 資料 (預覽) '
description: Azure 儲存體清查是協助您瞭解儲存體帳戶內所有 blob 資料的工具。
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 12/03/2020
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 86ded3dea819702631b1fa04dbc56f727566fc98
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602677"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>使用 Azure 儲存體 blob 清查來管理 blob 資料 (預覽) 

Azure 儲存體 blob 清查功能可概述儲存體帳戶內的 blob 資料。 使用清查報表來瞭解資料大小總計、年齡、加密狀態等等。 此報告供應商務和合規性需求的資料總覽。 啟用之後，每天都會自動建立清查報告。

## <a name="availability"></a>可用性

一般用途第2版 (GPv2) 和 premium 區塊 blob 儲存體帳戶支援 blob 清查。 當或未啟用 [階層命名空間](data-lake-storage-namespace.md) 功能時，支援此功能。

### <a name="preview-regions"></a>預覽區域

Blob 清查預覽適用于下欄區域中的儲存體帳戶：

- 法國中部
- 加拿大中部
- 加拿大東部

### <a name="pricing-and-billing"></a>價格和計費

預覽期間不會收取清查報表的費用。 當這項功能正式推出時，將會決定定價。

## <a name="enable-inventory-reports"></a>啟用清查報表

將原則新增至您的儲存體帳戶，以啟用 blob 清查報告。 使用 [Azure 入口網站](https://portal.azure.com/)來新增、編輯或移除原則。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com/)
1. 選取其中一個儲存體帳戶
1. 在 [ **blob 服務**] 底下，選取 [ **blob 清查**]
1. 確認已選取 [ **啟用 Blob 清查** ]
1. 選取 [**新增規則**]
1. 命名您的新規則
1. 選取清查報表的 **Blob 類型**
1. 新增前置詞相符以篩選清查報表
1. 選取是否要在清查報告中 **包含 blob 版本** 並 **包含快照** 集。 您必須在帳戶上啟用版本和快照集，才能在啟用對應選項的情況下儲存新規則。
1. 選取 [儲存]

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="顯示如何使用 Azure 入口網站新增 blob 清查規則的螢幕擷取畫面":::

清查原則會完整讀取或寫入。 不支援部分更新。

> [!IMPORTANT]
> 如果您啟用儲存體帳戶的防火牆規則，可能會封鎖清查要求。 您可以提供受信任 Microsoft 服務的例外狀況，以解除封鎖這些要求。 如需詳細資訊，請參閱[設定防火牆和虛擬網路](../common/storage-network-security.md#exceptions)中的＜例外狀況＞一節。

每日會自動排程 blob 清查執行。 清查執行最多可能需要24小時才能完成。 清查報表的設定方式是新增具有一或多個規則的清查原則。

## <a name="inventory-policy"></a>清查原則

清查原則是 JSON 檔中的一組規則。

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

在 Azure 入口網站的 [ **Blob 清查**] 區段中選取 [程式 **代碼**] 索引標籤，以查看清查原則的 JSON。

| 參數名稱 | 參數類型        | 備註 | 必要？ |
|----------------|-----------------------|-------|-----------|
| 目的地    | String                | 將產生所有清查檔案的目的地容器。 目的地容器必須已經存在。 | 是 |
| 已啟用        | Boolean               | 用來停用整個原則。 當設為 **true** 時，[已啟用規則層級] 欄位會覆寫此參數。 停用時，將會停用所有規則的清查。 | 是 |
| 規則          | Rule 物件的陣列 | 原則中至少需要一個規則。 最多支援10個規則。 | 是 |

## <a name="inventory-rules"></a>清查規則

規則會捕捉用來產生清查報表的篩選準則和輸出參數。 每個規則都會建立清查報告。 規則可以有重迭的首碼。 Blob 可以根據規則定義出現在多個清查中。

原則中的每個規則都有數個參數：

| 參數名稱 | 參數類型                 | 備註 | 必要？ |
|----------------|--------------------------------|-------|-----------|
| NAME           | String                         | 規則名稱最多可包含256個區分大小寫的英數位元。 名稱在原則中必須是唯一的。 | 是 |
| 已啟用        | Boolean                        | 允許啟用或停用規則的旗標。 預設值為 **true**。 | 是 |
| 定義     | JSON 清查規則定義 | 每個定義都是由一組規則篩選器所組成。 | 是 |

全域 **Blob 清查啟用** 旗標優先于規則中的 *enabled* 參數。

### <a name="rule-filters"></a>規則篩選

有幾個篩選準則可用來自訂 blob 清查報表：

| 篩選名稱         | 篩選類型                     | 備註 | 必要？ |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | 預先定義的列舉值陣列 | 有效值為 `blockBlob` 和 `appendBlob` 適用于已啟用階層命名空間的帳戶，以及 `blockBlob` `appendBlob` 其他帳戶的、和 `pageBlob` 。 | 是 |
| prefixMatch         | 要比對的前置詞最多10個字串的陣列。 前置詞必須以容器名稱開頭，例如 "container1/foo" | 如果您未定義 *prefixMatch* 或提供空的前置詞，則規則會套用至儲存體帳戶內的所有 blob。 | 否 |
| includeSnapshots    | Boolean                         | 指定清查是否應包含快照集。 預設值為 **false**。 | 否 |
| includeBlobVersions | Boolean                         | 指定清查是否應包含 blob 版本。 預設值為 **false**。 | 否 |

在 Azure 入口網站的 [ **Blob 清查**] 區段中選取 [程式 **代碼**] 索引標籤，以查看清查規則的 JSON。 篩選準則會在規則定義中指定。

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>清查輸出

每個清查執行都會在指定的清查目的地容器中產生一組 CSV 格式的檔案。 清查輸出會在下列路徑下 `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` 產生：：

- *accountName* 是您的 Azure Blob 儲存體帳戶名稱
- *清查-目的地-容器* 是您在清查原則中指定的目的地容器
- *YYYY/MM/DD/HH-MM-SS* 是清查開始執行的時間

### <a name="inventory-files"></a>清查檔案

每個清查執行都會產生下列檔案：

- **清查 CSV 檔案**：每個清查規則 (CSV) 檔案的逗點分隔值。 每個檔案都包含相符的物件和其中繼資料。 每個 CSV 格式化檔案中的第一個資料列一律是架構資料列。 下圖顯示在 Microsoft Excel 中開啟的清查 CSV 檔案。

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="在 Microsoft Excel 中開啟之清查 CSV 檔案的螢幕擷取畫面":::

- **資訊清單** 檔：檔案上的 manifest.js，其中包含針對該執行中的每個規則所產生之清查檔案的詳細資料。 資訊清單檔也會捕捉使用者提供的規則定義，以及該規則的清查路徑。

- 總和 **檢查** 碼檔案： manifest. checksum 檔案，其中包含檔案上 manifest.js內容的 MD5 總和檢查碼。 產生資訊清單。總和檢查碼檔案會標示清查執行的完成。

## <a name="inventory-completed-event"></a>清查已完成事件

訂閱「清查已完成」事件，以在清查執行完成時收到通知。 建立資訊清單總和檢查碼檔案時，就會產生此事件。 清查已完成事件也會發生在清查執行失敗時，才會開始執行。 例如，不正確原則或目的地容器不存在錯誤將會觸發事件。 此事件會發佈至 Blob 清查主題。

範例事件：

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="next-steps"></a>後續步驟

[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)
