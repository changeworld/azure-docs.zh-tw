---
title: Azure 儲存體監視資料參考 |Microsoft Docs
description: 從 Azure 儲存體監視資料的記錄和計量參考。
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.openlocfilehash: 7ba66441a87e3e02483ae27400f9900d2d052af4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118153"
---
# <a name="azure-storage-monitoring-data-reference"></a>Azure 儲存體監視資料參考

如需收集和分析 Azure 儲存體的監視資料的詳細資訊，請參閱[監視 Azure 儲存體](monitor-storage.md)。

## <a name="metrics"></a>計量

下表列出針對 Azure 儲存體收集的平臺計量。 

### <a name="capacity-metrics"></a>容量度量

容量計量值會每隔一小時就傳送給 Azure 監視器。 這些值會每天重新整理。 時間粒紋會定義用來呈現計量值的時間間隔。 所有容量計量支援的時間粒紋為一小時 (PT1H)。

Azure 儲存體會提供下列 Azure 監視器容量計量。

#### <a name="account-level"></a>帳戶層級

| 計量 | 說明 |
| ------------------- | ----------------- |
| UsedCapacity | 儲存體帳戶所使用的儲存體數量。 若為標準儲存體帳戶，則為 Blob、資料表、檔案和佇列所使用的容量總和。 若為進階儲存體帳戶和 Blob 儲存體帳戶，此數量和 BlobCapacity 相同。 <br/><br/> 單位：位元組 <br/> 彙總類型：平均 <br/> 值範例：1024 |

#### <a name="blob-storage"></a>Blob 儲存體

| 計量 | 說明 |
| ------------------- | ----------------- |
| BlobCapacity | 儲存體帳戶中所使用的 Blob 儲存體總計。 <br/><br/> 單位：位元組 <br/> 彙總類型：平均 <br/> 值範例：1024 <br/> 維度： **BlobType**和**BlobTier** （[定義](#metrics-dimensions)） |
| BlobCount    | 儲存體帳戶中所儲存的 Blob 物件數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 <br/> 維度： **BlobType**和**BlobTier** （[定義](#metrics-dimensions)） |
| ContainerCount    | 儲存體帳戶中的容器數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| IndexCapacity     | ADLS Gen2 階層式索引使用的儲存體容量 <br/><br/> 單位：位元組 <br/> 彙總類型：平均 <br/> 值範例：1024 |

#### <a name="table-storage"></a>資料表儲存體

| 計量 | 說明 |
| ------------------- | ----------------- |
| TableCapacity | 儲存體帳戶所使用的表格儲存體數量。 <br/><br/> 單位：位元組 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| TableCount   | 儲存體帳戶中的表格數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| TableEntityCount | 儲存體帳戶中的表格實體數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |

#### <a name="queue-storage"></a>佇列儲存體

| 計量 | 說明 |
| ------------------- | ----------------- |
| QueueCapacity | 儲存體帳戶所使用的佇列儲存體數量。 <br/><br/> 單位：位元組 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| QueueCount   | 儲存體帳戶中的佇列數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| QueueMessageCount | 儲存體帳戶中未到期的佇列訊息數目。 <br/><br/>單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |

#### <a name="file-storage"></a>檔案儲存體

| 計量 | 說明 |
| ------------------- | ----------------- |
| FileCapacity | 儲存體帳戶所使用的檔案儲存體數量。 <br/><br/> 單位：位元組 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| FileCount   | 儲存體帳戶中的檔案數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| FileShareCount | 儲存體帳戶中的檔案共用數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |

### <a name="transaction-metrics"></a>交易度量

對儲存體帳戶的每個要求，都會發出從 Azure 儲存體到 Azure 監視器的交易計量。 在您的儲存體帳戶上沒有活動的情況下，該期間內交易計量上不會有資料。 系統會同時提供帳戶和服務層級 (Blob 儲存體、表格儲存體、Azure 檔案和佇列儲存體) 的所有交易計量。 時間粒紋會定義用來呈現計量值的時間間隔。 所有交易計量支援的時間粒紋為 PT1H 和 PT1M。

Azure 儲存體會提供下列 Azure 監視器交易計量。

| 計量 | 說明 |
| ------------------- | ----------------- |
| 交易 | 向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 <br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 適用維度：ResponseType、GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions))<br/> 值範例：1024 |
| 輸入 | 輸入資料量。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。 <br/><br/> 單位：位元組 <br/> 彙總類型：總計 <br/> 適用維度：GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| 輸出 | 輸出資料量。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。 <br/><br/> 單位：位元組 <br/> 彙總類型：總計 <br/> 適用維度：GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| SuccessServerLatency | Azure 儲存體用來處理成功要求的平均時間。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。 <br/><br/> 單位：毫秒 <br/> 彙總類型：平均 <br/> 適用維度：GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| SuccessE2ELatency | 向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。 <br/><br/> 單位：毫秒 <br/> 彙總類型：平均 <br/> 適用維度：GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| 可用性 | 儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將可計費的要求值總計除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。 <br/><br/> 單位：百分比 <br/> 彙總類型：平均 <br/> 適用維度：GeoType、ApiName 和 Authentication ([定義](#metrics-dimensions)) <br/> 值範例：99.99 |

<a id="metrics-dimensions" />

## <a name="metrics-dimensions"></a>計量維度

Azure 儲存體支援下列 Azure 監視器計量維度。

| 維度名稱 | 說明 |
| ------------------- | ----------------- |
| **BlobType** | 只適用於 Blob 計量的 Blob 類型。 支援的值為**BlockBlob**、 **PageBlob**和**Azure Data Lake Storage**。 附加 Blob 隨附於 BlockBlob。 |
| **BlobTier** | Azure 儲存體提供不同的存取層，可讓您以最符合成本效益的方式儲存 blob 物件資料。 如需詳細資訊，請參閱[Azure 儲存體 blob 層](../blobs/storage-blob-storage-tiers.md)。 支援的值包括： <br/> <li>經常性**存取：熱**層</li> <li>**酷炫**：非經常性存取層</li> <li>封存 **：封存**層</li> <li>**Premium**：區塊 blob 的高階層級</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**： premium 分頁 blob 的層級類型</li> <li>**標準**：標準分頁 Blob 的層級類型</li> <li>**Untiered**：一般用途 v1 儲存體帳戶的層級類型</li> |
| **GeoType** | 來自主要或次要叢集的交易。 可用的值包括 [**主要**] 和 [**次要**]。 在從次要租用戶讀取物件時，此維度會套用到讀取權限異地備援儲存體 (RA-GRS)。 |
| **ResponseType** | 交易回應類型。 可用的值包括： <br/><br/> <li>**ServerOtherError**：除了描述的其他所有伺服器端錯誤 </li> <li>**ServerBusyError**：傳回 HTTP 503 狀態碼的已驗證要求。 </li> <li>**ServerTimeoutError**：超時的已驗證要求，其傳回 HTTP 500 狀態碼。 逾時是因為伺服器錯誤而發生。 </li> <li>**AuthorizationError**：因為未經授權存取資料或授權失敗而失敗的已驗證要求。 </li> <li>**NetworkError**：因網路錯誤而失敗的已驗證要求。 當用戶端在逾時到期前就過早關閉連線時，最常會發生這個情況。 </li><li>**ClientAccountBandwidthThrottlingError**：要求會在頻寬上受到節流，以超過[儲存體帳戶的擴充性限制](scalability-targets-standard-account.md)。</li><li>**ClientAccountRequestThrottlingError**：要求已根據超出[儲存體帳戶擴充性限制](scalability-targets-standard-account.md)的要求速率進行節流。<li>**ClientThrottlingError**：其他用戶端節流錯誤。 已排除 ClientAccountBandwidthThrottlingError 和 ClientAccountRequestThrottlingError。</li> <li>**ClientTimeoutError**：超時的已驗證要求，其傳回 HTTP 500 狀態碼。 如果用戶端的網路逾時或要求逾時設定為比儲存體服務預期的值還低，則此值是符合預期的逾時。 否則，它會回報為 ServerTimeoutError。 </li> <li>**ClientOtherError**：除了描述的所有其他用戶端錯誤。 </li> <li>**成功**：成功的要求</li> <li> **SuccessWithThrottling**：當 SMB 用戶端在第一次嘗試時受到節流，但在重試之後成功時的成功要求。</li> |
| **ApiName** | 作業的名稱。 例如： <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> 如需所有的作業名稱，請參閱[文件](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。 |
| **驗證** | 交易中所使用的驗證類型。 可用的值包括： <br/> <li>**AccountKey**：交易已使用儲存體帳戶金鑰進行驗證。</li> <li>**SAS**：交易是以共用存取簽章進行驗證。</li> <li>**Oauth**：交易是以 oauth 存取權杖進行驗證。</li> <li>**Anonymous**：以匿名方式要求交易。 不包括預檢要求。</li> <li>**AnonymousPreflight**：交易是預檢要求。</li> |

對於計量支援維度，您必須指定維度值才能查看對應的計量值。 例如，如果您要查看成功回應的 **Transactions** 值，則需要篩選具有 **Success** 值的 **ResponseType** 維度。 或者，如果您要查看區塊 Blob 的 **BlobCount** 值，就需要篩選具有 **BlockBlob** 值的 **BlobType** 維度。

## <a name="resource-logs-preview"></a>資源記錄（預覽）

> [!NOTE]
> Azure 監視器中的 Azure 儲存體記錄處於公開預覽狀態，並可在所有公用雲端區域中進行預覽測試。 若要註冊預覽，請參閱[此頁面](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。  此預覽會針對一般用途 v1 和一般用途 v2 儲存體帳戶中的 blob （包括 Azure Data Lake Storage Gen2）、檔案、佇列、資料表、premium 儲存體帳戶啟用記錄。 不支援傳統儲存體帳戶。

下表列出在 Azure 監視器記錄或 Azure 儲存體中收集 Azure 儲存體資源記錄檔時的屬性。 屬性會描述作業、服務，以及用來執行作業的授權類型。

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

| 屬性 | 描述 |
|:--- |:---|
|**time** | 儲存體收到要求時的國際時間協調（UTC）時間。 例如： `2018/11/08 21:09:36.6900118` 。|
|**resourceId** | 儲存體帳戶的資源識別碼。 例如： `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**類別** | 所要求之作業的分類。 例如： `StorageRead` 、 `StorageWrite` 或 `StorageDelete` 。|
|**operationName** | 已執行的 REST 操作類型。 <br> 如需作業的完整清單，請參閱[儲存體分析記錄作業和狀態訊息主題](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。 |
|**operationVersion** | 提出要求時所指定的儲存體服務版本。 這相當於**x-ms 版本**標頭的值。 例如： `2017-04-17` 。|
|**schemaVersion** | 記錄的架構版本。 例如： `1.0` 。|
|**statusCode** | 要求的 HTTP 狀態碼。 如果要求中斷，此值可能會設定為 `Unknown` 。 <br> 例如： `206` |
|**statusText** | 要求之作業的狀態。  如需狀態訊息的完整清單，請參閱[儲存體分析記錄作業和狀態訊息主題](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。 在2017-04-17 版和更新版本中，不會使用狀態訊息 `ClientOtherError` 。 相反地，此欄位包含錯誤碼。 例如： `SASSuccess`  |
|**durationMs** | 執行要求之作業的總時間（以毫秒為單位）。 這包括讀取傳入要求的時間，以及將回應傳送給要求者。 例如： `12` 。|
|**callerIpAddress** | 要求者的 IP 位址，包含連接埠號碼。 例如： `192.100.0.102:4362` 。 |
|**correlationId** | 用來讓資源間的記錄相互關聯的識別碼。 例如： `b99ba45e-a01e-0042-4ea6-772bbb000000` 。 |
|**location** | 儲存體帳戶的位置。 例如： `North Europe` 。 |
|**protocol**|作業中使用的通訊協定。 例如： `HTTP` 、 `HTTPS` 、 `SMB` 或`NFS`|
| **uri** | 要求的統一資源識別項。 例如： `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10` 。 |

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>描述如何驗證操作的欄位

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

| 屬性 | 說明 |
|:--- |:---|
|**身分識別/類型** | 用來提出要求的驗證類型。 例如： `OAuth` 、 `SAS Key` 、 `Account Key` 或`Anonymous` |
|**identity/tokenHash**|此欄位僅保留供內部使用。 |
|**授權/動作** | 指派給要求的動作。 例如： `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**authorization/roleAssignmentId** | 角色指派識別碼。 例如： `4e2521b7-13be-4363-aeda-111111111111` 。|
|**authorization/roleDefinitionId** | 角色定義識別碼。 例如： `ba92f5b4-2d11-453d-a403-111111111111"` 。|
|**主體/識別碼** | 安全性主體的識別碼。 例如： `a4711f3a-254f-4cfb-8a2d-111111111111` 。|
|**主體/類型** | 安全性主體的類型。 例如： `ServicePrincipal` 。 |
|**要求者/appID** | 做為要求者使用的 Open Authorization （OAuth）應用程式識別碼。 <br> 例如： `d3f7d5fe-e64a-4e4e-871d-333333333333` 。|
|**要求者/物件** | 要求的 OAuth 物件。 例如： `https://storage.azure.com` 。 |
|**要求者/objectId** | 要求者的 OAuth 物件識別碼。 若為 Kerberos 驗證，則代表 Kerberos 已驗證使用者的物件識別碼。 例如： `0e0bf547-55e5-465c-91b7-2873712b249c` 。 |
|**要求者/tenantId** | 身分識別的 OAuth 租使用者識別碼。 例如： `72f988bf-86f1-41af-91ab-222222222222` 。|
|**要求者/tokenIssuer** | OAuth 權杖簽發者。 例如： `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/` 。|
|**要求者/upn** | 要求者的使用者主體名稱（UPN）。 例如： `someone@contoso.com` 。 |
|**要求者/使用者名稱** | 此欄位僅保留供內部使用。|

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

| 屬性 | 說明 |
|:--- |:---|
|**帳戶** | 儲存體帳戶的名稱。 例如： `mystorageaccount` 。  |
|**requestUrl** | 要求的 URL。 例如： `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10` 。|
|**userAgentHeader** | **使用者代理程式標頭**值（以引號括住）。 例如： `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)` 。|
|**referrerHeader** | 訪**客標頭**值。 例如： `http://contoso.com/about.html` 。|
|**clientRequestId** | 要求的**x-ms-用戶端要求識別碼**標頭值。 例如： `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6` 。 |
|**etag** | 傳回之物件的 ETag 識別碼 (以引號括住)。 例如： `0x8D101F7E4B662C4` 。  |
|**Azurestoragelog.serverlatencyms** | 執行要求的作業所表示的總時間（以毫秒為單位）。 此值不包括網路延遲（讀取傳入要求並將回應傳送給要求者的時間）。 例如： `22` 。 |
|**serviceType** | 與此要求相關聯的服務。 例如：`blob`、`table`、`files` 或 `queue`。 |
|**operationCount** | 包含在要求中的每個已記錄作業的數目。 此計數以的索引為開頭 `0` 。 某些要求需要一項以上的作業，例如複製 blob 的要求。 大部分的要求只會執行一項作業。 例如： `1` 。 |
|**requestHeaderSize** | 要求標頭的大小（以位元組為單位）。 例如： `578` 。 <br>如果要求不成功，此值可能是空的。 |
|**requestBodySize** | 儲存體服務所讀取的要求封包大小（以位元組表示）。 <br> 例如： `0` 。 <br>如果要求不成功，此值可能是空的。  |
|**responseHeaderSize** | 回應標頭的大小（以位元組為單位）。 例如： `216` 。 <br>如果要求不成功，此值可能是空的。  |
|**responseBodySize** | 儲存體服務寫入的回應封包大小 (以位元組為單位)。 如果要求不成功，這個值可能是空值。 例如： `216` 。  |
|**requestMd5** | 要求中的**Content-MD5**標頭或**x-ms-content-md5**標頭的值。 在此欄位中指定的 MD5 雜湊值代表要求中的內容。 例如： `788815fd0198be0d275ad329cafd1830` 。 <br>這個欄位可以是空白的。  |
|**serverMd5** | 儲存體服務所計算的 MD5 雜湊值。 例如： `3228b3cf1069a5489b298446321f8521` 。 <br>這個欄位可以是空白的。  |
|**lastModifiedTime** | 傳回之物件的上次修改時間（LMT）。  例如： `Tuesday, 09-Aug-11 21:13:26 GMT` 。 <br>對於可傳回多個物件的作業而言，此欄位是空白的。 |
|**conditionsUsed** | 代表條件的索引鍵/值組清單（以分號分隔）。 條件可以是下列任何一項： <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> 例如： `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT` 。 |
|**contentLengthHeader** | 傳送至儲存體服務之要求的內容長度標頭值。 如果要求成功，則此值等於 requestBodySize。 如果要求不成功，此值可能不會等於 requestBodySize，或可能是空的。 |
|**tlsVersion** | 要求連接中使用的 TLS 版本。 例如： `TLS 1.2` 。 |
|**smbTreeConnectID** | 伺服器訊息區（SMB） **treeConnectId**是在樹狀結構連線時間建立的。 例如： `0x3` |
|**smbPersistentHandleID** | 不受網路重新連接的 SMB2 建立要求中的持續控制碼識別碼。  在[SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 中被參考為**SMB2_FILEID。Persistent**。 例如： `0x6003f` |
|**smbVolatileHandleID** | 已在網路上回收的 SMB2 CREATE 要求中的變動性控制碼識別碼。  在[SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 中被參考為**SMB2_FILEID。Volatile**。 例如： `0xFFFFFFFF00000065` |
|**smbMessageID** | 連接相對**MessageId**。 例如： `0x3b165` |
|**smbCreditsConsumed** | 要求所使用的輸入或輸出（單位為64k）。 例如： `0x3` |
|**smbCommandDetail** | 此特定要求的詳細資訊，而不是一般的要求類型。 例如： `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | 與檔案或目錄相關聯的**FileId** 。  大致類似于 NTFS FileId。 例如： `0x9223442405598953` |
|**smbSessionID** | 在會話設定時間建立的 SMB2 **SessionId** 。 例如： `0x8530280128000049` |
|**smbCommandMajor uint32** | [SMB2_HEADER]**命令**中的值。 目前，這是介於0到18（含）之間的數位。 例如： `0x6` |
|**smbCommandMinor** | **SmbCommandMajor**的子類別（如果適用的話）。 例如： `DirectoryCloseAndDelete` |

## <a name="see-also"></a>請參閱

- 如需監視 Azure 儲存體的說明，請參閱[監視 Azure 儲存體](monitor-storage.md)。
- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器監視 azure 資源](../../azure-monitor/insights/monitor-azure-resource.md)。