---
title: Azure Blob 儲存體監視資料參考資料 |Microsoft Docs
description: 從 Azure Blob 儲存體監視資料的記錄和計量參考。
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 16ae2f9e74202aff47e58a22dbe21a28d8280a7e
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780717"
---
# <a name="azure-blob-storage-monitoring-data-reference"></a>Azure Blob 儲存體監視資料參考

如需收集和分析 Azure 儲存體監視資料的詳細資訊，請參閱[監視 Azure 儲存體](monitor-blob-storage.md)。

## <a name="metrics"></a>計量

下表列出針對 Azure 儲存體收集的平台計量。 

### <a name="capacity-metrics"></a>容量度量

容量計量值會每日重新整理 (最多24小時的) 。 時間粒紋會定義用來呈現計量值的時間間隔。 所有容量計量支援的時間粒紋為一小時 (PT1H)。

Azure 儲存體會提供下列 Azure 監視器容量計量。

#### <a name="account-level"></a>帳戶層級

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="blob-storage"></a>Blob 儲存體

下表顯示 [Blob 儲存體計量](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices)。

| 計量 | 描述 |
| ------------------- | ----------------- |
| BlobCapacity | 儲存體帳戶中所使用的 Blob 儲存體總計。 <br/><br/> 單位：位元組 <br/> 彙總類型：Average <br/> 值範例：1024 <br/> 維度：**BlobType** 和 **BlobTier** ([定義](#metrics-dimensions)) |
| BlobCount    | 儲存體帳戶中所儲存的 Blob 物件數目。 <br/><br/> 單位：Count <br/> 彙總類型：Average <br/> 值範例：1024 <br/> 維度：**BlobType** 和 **BlobTier** ([定義](#metrics-dimensions)) |
| BlobProvisionedSize | 在儲存體帳戶中布建的儲存體數量。 此計量僅適用于 premium 儲存體帳戶。 <br/><br/> 單位：位元組 <br/> 彙總類型：Average |
| ContainerCount    | 儲存體帳戶中的容器數目。 <br/><br/> 單位：Count <br/> 彙總類型：Average <br/> 值範例：1024 |
| IndexCapacity     | ADLS Gen2 階層式索引使用的儲存體容量 <br/><br/> 單位：位元組 <br/> 彙總類型：Average <br/> 值範例：1024 |

### <a name="transaction-metrics"></a>交易度量

對儲存體帳戶的每個要求，都會發出從 Azure 儲存體到 Azure 監視器的交易計量。 在您的儲存體帳戶上沒有活動的情況下，該期間內交易計量上不會有資料。 所有交易計量都可在帳戶和 Blob 儲存體服務層級使用。 時間粒紋會定義用來呈現計量值的時間間隔。 所有交易計量支援的時間粒紋為 PT1H 和 PT1M。

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>計量維度

Azure 儲存體支援下列 Azure 監視器計量維度。

### <a name="dimensions-available-to-all-storage-services"></a>適用于所有儲存體服務的維度

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

### <a name="dimensions-specific-to-blob-storage"></a>Blob 儲存體專用的維度

| 維度名稱 | 描述 |
| ------------------- | ----------------- |
| **BlobType** | 只適用於 Blob 計量的 Blob 類型。 支援的值為 **BlockBlob**、**PageBlob** 和 **Azure Data Lake Storage**。 附加 blob 包含在 **BlockBlob** 中。 |
| **BlobTier** | Azure 儲存體提供不同的存取層，可讓您以最符合成本效益的方式儲存 Blob 物件資料。 如需詳細資訊，請參閱 [Azure 儲存體 Blob 層](../blobs/storage-blob-storage-tiers.md)。 支援的值包括： <br/> <li>**經常性**：經常性存取層</li> <li>**非經常性**：非經常性存取層</li> <li>**封存**：封存層</li> <li>**進階**：區塊 Blob 的進階層</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**：進階分頁 Blob 的層級類型</li> <li>**標準**：標準分頁 Blob 的層級類型</li> <li>**無分層**：一般用途 v1 儲存體帳戶的層級類型</li> |

對於計量支援維度，您必須指定維度值才能查看對應的計量值。 例如，如果您要查看成功回應的 **Transactions** 值，則需要篩選具有 **Success** 值的 **ResponseType** 維度。 如果您查看區塊 Blob 的 **BlobCount** 值，就必須使用 **BlockBlob** 來篩選 **BlobType** 維度。

## <a name="resource-logs-preview"></a>資源記錄 (預覽)

> [!NOTE]
> Azure 監視器中的 Azure 儲存體記錄處於公開預覽狀態，可在所有公用雲端區域中進行預覽測試。 此預覽可啟用 Blob (包括 Azure Data Lake Storage Gen2)、檔案、佇列、資料表、一般用途 v1 進階儲存體帳戶及一般用途 v2 儲存體帳戶的記錄。 不支援傳統儲存體帳戶。

下表列出在 Azure 監視器記錄或 Azure 儲存體中收集時，Azure 儲存體資源記錄的屬性。 屬性會描述作業、服務，以及用來執行作業的授權類型。

### <a name="fields-that-describe-the-operation"></a>描述作業的欄位

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>描述如何驗證作業的欄位

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>描述服務的欄位

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>另請參閱

- 如需監視 Azure 儲存體的說明，請參閱[監視 Azure 儲存體](monitor-blob-storage.md)。
- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器來監視 Azure 資源](../../azure-monitor/insights/monitor-azure-resource.md)。