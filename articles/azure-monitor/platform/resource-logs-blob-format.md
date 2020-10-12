---
title: 針對 Azure 監視器資源記錄檔的格式變更進行準備
description: 在2018年11月1日，Azure 資源記錄已移至使用附加 blob。
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 45b18352d88877a5d611f203d87da83fd0d58c6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87077123"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>針對封存至儲存體帳戶 Azure 監視器平臺記錄的格式變更進行準備

> [!WARNING]
> 如果您要使用[記錄設定檔](./resource-logs.md#send-to-azure-storage)，將[Azure 資源記錄或計量傳送至](./resource-logs.md#send-to-azure-storage)儲存體帳戶，儲存體帳戶中的資料格式會在2018年11月1日變更為 JSON 行。 下方的指示會說明新格式的影響，以及如何更新工具來加以因應。
>

## <a name="what-changed"></a>變更內容

Azure 監視器提供一項功能，可讓您將資源記錄和活動記錄傳送到 Azure 儲存體帳戶、事件中樞命名空間，或 Azure 監視器中的 Log Analytics 工作區。 為了解決系統效能問題，在 **2018 年11月1日的12:00 午夜 UTC** ，記錄資料傳送至 blob 儲存體的格式已變更。 如果您有工具會從 Blob 儲存體讀取資料，便需要更新工具才能了解新的資料格式。

* 在2018年11月1日星期四的12:00 午夜 UTC，blob 格式已變更為 [JSON 行](http://jsonlines.org/)。 這表示每筆記錄都會以新行字元隔開、沒有外部記錄陣列，而且 JSON 記錄之間沒有逗號。
* 所有訂用帳戶上所有診斷設定的 blob 格式一次變更。 第一個在11月1日發出的檔案 PT1H.js使用這個新的格式。 Blob 和容器名稱維持不變。
* 在11月1日之前設定診斷設定，會在11月1日之前，繼續以目前的格式發出資料。
* 這種變更會在所有公用雲端區域發生一次。 這項變更將不會發生在由世紀、Azure 德國或 Azure Government 雲端 Microsoft Azure 操作的中。
* 這項變更會影響下列資料類型：
  * [Azure 資源記錄](./resource-logs.md#send-to-azure-storage) ([在此查看資源清單](./resource-logs-schema.md)) 
  * [診斷設定所匯出的 Azure 資源計量](diagnostic-settings.md)
  * [記錄設定檔所匯出的 Azure 活動記錄資料](./activity-log.md)
* 這項變更不會影響：
  * 網路流量記錄
  * Azure 服務記錄未透過 (Azure 監視器提供，例如 Azure App Service 資源記錄檔、儲存體分析記錄) 
  * 將 Azure 資源記錄和活動記錄路由傳送至 (事件中樞、Log Analytics) 的其他目的地

### <a name="how-to-see-if-you-are-impacted"></a>如何查看您是否受影響

您只有在下列情況下才會受到這項變更的影響：
1. 使用診斷設定將記錄資料傳送至 Azure 儲存體帳戶，以及
2. 有工具依存著儲存體中這些記錄的 JSON 結構。
 
若要識別您是否有正在將資料傳送至 Azure 儲存體帳戶的診斷設定，您可以流覽至入口網站的 [ **監視** ] 區段，按一下 [ **診斷設定**]，並找出 **診斷狀態** 設定為 [ **已啟用**] 的任何資源：

![Azure 監視器診斷設定刀鋒視窗](media/diagnostic-logs-append-blobs/portal-diag-settings.png)

如果 [診斷狀態] 設定為 [已啟用]，則您會有該資源的作用中診斷設定。 按一下資源，以查看是否有任何診斷設定將資料傳送至儲存體帳戶：

![已啟用儲存體帳戶](media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

如果您的資源使用這些資源診斷設定將資料傳送至儲存體帳戶，則這項變更會影響該儲存體帳戶中的資料格式。 除非您的自訂工具可以在這些儲存體帳戶外部操作，否則格式變更不會影響您。

### <a name="details-of-the-format-change"></a>格式變更的詳細資料

Azure Blob 儲存體中 PT1H.json 檔案的目前格式會使用記錄的 JSON 陣列。 下列範例是目前的 KeyVault 記錄檔：

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

新的格式會使用 [JSON 資料行](http://jsonlines.org/)，其中每個事件都是一行，而新行字元表示新事件。 以下是在變更之後，上述範例在 PT1H.json 檔案中的外觀：

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

這種新格式可讓 Azure 監視器使用[附加 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) 發送記錄檔，以更有效率地持續附加新的事件資料。

## <a name="how-to-update"></a>更新方式

如果您的自訂工具內嵌這些記錄檔以供進一步處理，則只需要更新。 如果您使用外部記錄分析或 SIEM 工具，則建議[改為使用事件中樞內嵌此資料](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)。 事件中樞整合在處理來自許多服務的記錄以及將特定記錄中的位置加上書籤方面更為簡單。

應該更新自訂工具，以處理目前格式和上述 JSON 資料行格式。 這確保開始以新格式顯示資料時，您的工具不會中斷。

## <a name="next-steps"></a>接下來的步驟

* 瞭解 [如何將資源資源記錄檔封存至儲存體帳戶](./resource-logs.md#send-to-azure-storage)
* 了解[將活動記錄資料封存至儲存體帳戶](./activity-log.md#legacy-collection-methods)
