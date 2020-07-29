---
title: 可供記錄擷取的客戶自有儲存體帳戶
description: 使用您自己的儲存體帳戶，將記錄資料擷取至 Azure 監視器中的 Log Analytics 工作區內。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: a14f7ca3e5a7b291e430db6ea536edc5396b5448
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318890"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Azure 監視器中可供記錄擷取的客戶自有儲存體帳戶

Azure 監視器在某些資料類型的擷取程序中會使用儲存體帳戶，例如[自訂記錄](data-sources-custom-logs.md)，以及部份的[ Azure 記錄](./diagnostics-extension-logs.md)。 在擷取程序中，系統會先將記錄傳送到儲存體帳戶，並於稍後擷取到 Log Analytics 或 Application Insights。 如果您想要在擷取期間控制資料，您可以使用自己的儲存體帳戶，而非服務管理的儲存體。 使用您自己的儲存體帳戶，可讓您在內嵌期間控制記錄的存取、內容、加密及保留。 我們將此稱為「自備儲存體」或 BYOS。 

透過 Private Link 進行網路隔離時，即是需要 BYOS 的狀況之一。 在使用 VNet 時，通常需要網路隔離，而且公用網際網路的存取會受到限制。 在這種情況下，存取 Azure 監視器服務儲存體來擷取記錄的做法會完全遭到封鎖，或視為不正確的做法。 請改為透過 VNet 內部的客戶自有儲存體帳戶來擷取記錄，或可從中輕鬆存取。

另一種狀況是使用客戶管理的金鑰 (CMK) 加密記錄。 客戶可以在儲存記錄的叢集上，使用 CMK 加密已記錄的資料。 在擷取程序期間，也可以使用相同金鑰來加密記錄。

## <a name="data-types-supported"></a>支援的資料類型

從儲存體帳戶擷取的資料類型包括下列各項。 如需有關擷取這些類型的詳細資訊，請參閱[從 Azure 診斷擴充功能收集資料至 Azure 監視器記錄](./diagnostics-extension-logs.md)。

| 類型 | 資料表資訊 |
|:-----|:------------------|
| IIS 記錄 | wad-iis-logfiles|
|Windows 事件記錄 | 表格：WADWindowsEventLogsTable |
| syslog | 表格：LinuxsyslogVer2v0 |
| Windows ETW 記錄 | 表格：WADETWEventTable|
| Service Fabric | 表格：WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| 自訂的記錄 | n/a |
| Azure 資訊安全中心 Watson 傾印檔案 | n/a|  

## <a name="storage-account-requirements"></a>儲存體帳戶的需求 
儲存體帳戶必須符合下列需求：

- 可存取您的 VNet 上的資源，其可將記錄寫入至儲存體。
- 必須位於與其連結的工作區相同區域。
- 允許 Azure 監視器存取-如果您選擇限制儲存體帳戶存取選取的網路，請務必允許此例外狀況：*允許受信任的 Microsoft 服務存取此儲存體帳戶*。

## <a name="process-to-configure-customer-owned-storage"></a>設定客戶自有儲存體的程序
使用您自己的儲存體帳戶進行擷取的基本流程如下：

1. 建立儲存體帳戶或選取現有的帳戶。
2. 將儲存體帳戶連結至 Log Analytics 工作區。
3. 透過檢閱儲存體負載和保留加以管理，以確保其如預期般運作。

唯一可用來建立和移除連結的方法是透過 REST API。 下列各節提供每個程序所需特定 API 要求的詳細資料。

## <a name="command-line-and-rest-api"></a>命令列和 REST API

### <a name="command-line"></a>命令列
若要建立及管理連結的儲存體帳戶，請使用已[連結儲存體的 az monitor log analytics 工作區](/cli/azure/monitor/log-analytics/workspace/linked-storage)。 此命令可從工作區連結和取消連結儲存體帳戶，並列出連結的儲存體帳戶。

### <a name="request-and-cli-values"></a>要求和 CLI 值

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson - 將此值用於 Azure 資訊安全中心 Azure Watson 傾印檔案。
- CustomLogs – 針對下列資料類型使用此值：
  - 自訂的記錄
  - IIS 記錄
  - 事件 (Windows)
  - Syslog (Linux)
  - ETW 記錄
  - Service Fabric 事件
  - 評量資料  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
此值會使用下列結構：

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```


### <a name="get-linked-storage-accounts-for-all-data-source-types"></a>取得所有資料來源類型的連結儲存體帳戶

#### <a name="api-request"></a>API 要求

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>回應

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```


### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>取得特定資料來源類型的連結儲存體帳戶

#### <a name="api-request"></a>API 要求

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>回應 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

## <a name="create-or-modify-a-link"></a>建立或修改連結

一旦您將儲存體帳戶連結至工作區，Log Analytics 就會開始使用連結的帳戶，而非服務所擁有的儲存體帳戶。 您可以同時註冊一份儲存體帳戶清單，而且可以針對多個工作區使用相同的儲存體帳戶。

如果您的工作區在 VNet 外部處理 VNet 資源和資源，您應該確定其不會拒絕來自網際網路的流量。 您的儲存體應與工作區具有相同設定，並可供 VNet 外部的資源使用。 

### <a name="api-request"></a>API 要求

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>Payload

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>回應

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>取消連結儲存體帳戶
如果您決定停止使用儲存體帳戶進行擷取，或是要取代所使用的工作區，請將該儲存體從工作區取消連結。

從工作區中取消連結所有儲存體帳戶，表示擷取作業會嘗試依賴受服務管理的儲存體帳戶。 如果您的代理程式是在限制網際網路存取的 VNet 上執行，則擷取作業預期會失敗。 工作區必須具有可從受監視資源連線的連結儲存體帳戶。

刪除儲存體帳戶之前，請確認其所包含的所有資料都已擷取至工作區。 作為預防措施，請在連結替代儲存體之後，讓您的儲存體帳戶保持可用。 在擷取所有內容、且可看到新資料已寫入至新連接的儲存體帳戶之後，才能刪除帳戶。


### <a name="api-request"></a>API 要求
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>取代儲存體帳戶

若要取代用於擷取的儲存體帳戶，請先建立新儲存體帳戶的連結。 記錄代理程式也會取得更新的設定，並開始將資料傳送至新的儲存體。

接下來，請將舊儲存體帳戶取消連結，以便讓代理程式停止寫入已移除的帳戶。 擷取程序會繼續讀取此帳戶的資料，直到全部擷取完畢為止。 除非您看到所有記錄都已擷取，否則請勿刪除儲存體帳戶。

代理程式設定會在幾分鐘後重新整理，並切換至新儲存體。

## <a name="manage-storage-account"></a>管理儲存體帳戶

### <a name="load"></a>載入

儲存體帳戶可以在開始要求節流之前，先處理特定的讀取和寫入要求負載。 節流會影響擷取記錄所需的時間，而且可能會導致資料遺失。 如果您的儲存體已超載，請註冊其他儲存體帳戶，並將負載分散到這些帳戶之間。 

### <a name="related-charges"></a>相關費用

儲存體帳戶是依據儲存資料量、儲存體類型和備援類型來計費。 如需詳細資訊，請參閱[區塊 Blob 定價](https://azure.microsoft.com/pricing/details/storage/blobs/)和[表格儲存體定價](https://azure.microsoft.com/pricing/details/storage/tables/)。

如果您工作區的註冊儲存體帳戶位於另一個區域，則會根據這些[頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)，向您收取輸出費用。



## <a name="next-steps"></a>後續步驟

- 如需設定私人連結的詳細資訊，請參閱[使用 Azure 私人連結將網路安全地連接到 Azure 監視器](private-link-security.md)

