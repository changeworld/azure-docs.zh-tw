---
title: 將 Azure 資源日誌存檔到存儲帳戶 |微軟文件
description: 瞭解如何將 Azure 資源日誌存檔,以便長期保留在存儲帳戶中。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 787640ef08ee91220f42065af155c372247f0136
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804600"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>將 Azure 資源記錄封存到儲存體帳戶
Azure 中[的平台日誌](platform-logs-overview.md)(包括 Azure 活動日誌和資源日誌)為 Azure 資源和它們所依賴的 Azure 平臺提供了詳細的診斷和審核資訊。  本文介紹將平臺日誌收集到 Azure 存儲帳戶以保留用於存檔的數據。

## <a name="prerequisites"></a>Prerequisites
如果還沒有[Azure 儲存帳戶](../../storage/common/storage-account-create.md),則需要創建一個 Azure 存儲帳戶。 只要配置該設置的使用者對兩個訂閱具有適當的 RBAC 訪問許可權,存儲帳戶不必與資源發送日誌位於同一訂閱中。

> [!IMPORTANT]
> 要將資料傳送到不可變儲存,請設定儲存帳戶的不可變原則,如[「設定」中所述,並管理 Blob 儲存的不變性原則](../../storage/blobs/storage-blob-immutability-policies-manage.md)。 您必須遵循本文中的所有步驟,包括啟用受保護的追加 Blob 寫入。

> [!IMPORTANT]
> Azure 資料儲存庫 Gen2 帳戶當前不支援作為診斷設置的目標,即使這些帳戶可能在 Azure 門戶中列為有效選項。


不應使用現有存儲帳戶,該帳戶中存儲了其他非監視數據,以便更好地控制對數據的訪問。 但是,如果要將活動日誌和資源日誌存檔在一起,則可以選擇使用相同的存儲帳戶將所有監視數據保存在中心位置。

## <a name="create-a-diagnostic-setting"></a>建立診斷設定
通過為 Azure 資源創建診斷設置,將平臺日誌發送到存儲和其他目標。 有關詳細資訊[,請參閱建立診斷設定以在 Azure 中收集紀錄和指標](diagnostic-settings.md)。


## <a name="collect-data-from-compute-resources"></a>從計算資源收集資料
診斷設置將像收集任何其他資源一樣為 Azure 計算資源收集資源日誌,但不會收集其來賓操作系統或工作負荷的資源日誌。 要收集此資料,請安裝[Windows Azure 診斷代理](diagnostics-extension-overview.md)程式 。 


## <a name="schema-of-platform-logs-in-storage-account"></a>儲存帳戶中平台紀錄的架構

創建診斷設置後,一旦在已啟用的日誌類別中發生事件,就會在存儲帳戶中創建存儲容器。 容器中的 blob 使用以下命名慣例:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如,網路安全組的 blob 的名稱可能與以下內容類似:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

每個 PT1H.json blob 有一個 JSON blob，包含在 blob URL 指定時數內 (例如 h = 12) 發生的事件。 在目前這一小時，事件一發生就會附加到 PT1H.json 檔案。 分鐘值 (m=00) 始終為 00,因為資源日誌事件每小時被分解為單個 Blob。

在 PT1H.json 檔中,每個事件都儲存以以下格式。 這將使用公共頂級架構,但對於每個 Azure 服務是唯一的,如[資源日誌架構](diagnostic-logs-schema.md)[和活動日誌架構](activity-log-schema.md)中所述。

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> 平臺日誌使用[JSON 行](http://jsonlines.org/)寫入 Blob 儲存,其中每個事件都是一行,newline 字元指示新事件。 此格式於 2018 年 11 月實施。 在此日期之前,日誌被寫入 Blob 儲存作為 json 記錄陣列,如[準備更改 Azure 監視器平臺日誌以存檔到儲存帳戶](resource-logs-blob-format.md)時所述。

## <a name="next-steps"></a>後續步驟

* [閱讀資源紀錄的更多內容](platform-logs-overview.md)。
* [建立診斷設定以在 Azure 中收集紀錄和指標](diagnostic-settings.md)。
* [下載 blob 進行分析](../../storage/blobs/storage-quickstart-blobs-dotnet.md)。
* [使用 Azure 監視器 存檔 Azure 活動目錄紀錄](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)。
