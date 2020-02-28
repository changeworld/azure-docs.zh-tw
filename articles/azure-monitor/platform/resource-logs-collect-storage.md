---
title: 將 Azure 資源記錄封存到儲存體帳戶 |Microsoft Docs
description: 瞭解如何在儲存體帳戶中封存您的 Azure 資源記錄，以進行長期保留。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 843c179826b2064a1be24d3cee84b398987b4aed
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658966"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>將 Azure 資源記錄封存到儲存體帳戶
Azure 中的[平臺記錄](platform-logs-overview.md)，包括 azure 活動記錄和資源記錄，可提供 azure 資源的詳細診斷和審核資訊，以及它們所依賴的 azure 平臺。  本文說明如何將平臺記錄檔收集至 Azure 儲存體帳戶，以保留資料以供封存。

## <a name="prerequisites"></a>必要條件
如果您還沒有[Azure 儲存體帳戶](../../storage/common/storage-account-create.md)，您必須建立它。 儲存體帳戶不一定要與資源傳送記錄位於相同的訂用帳戶中，前提是設定此設定的使用者具有這兩個訂用帳戶的適當 RBAC 存取權。


> [!IMPORTANT]
> Azure Data Lake Storage Gen2 帳戶目前不支援做為診斷設定的目的地，即使它們在 Azure 入口網站中可能列為有效的選項。


您不應該使用已儲存其他非監視資料的現有儲存體帳戶，讓您可以更有效地控制資料的存取權。 不過，如果您要同時封存活動記錄檔和資源記錄，您可以選擇使用相同的儲存體帳戶，將所有監視資料保留在中央位置。

## <a name="create-a-diagnostic-setting"></a>建立診斷設定
藉由建立 Azure 資源的診斷設定，將平臺記錄傳送到儲存體和其他目的地。 如需詳細資訊，請參閱[建立診斷設定以收集 Azure 中的記錄和計量](diagnostic-settings.md)。


## <a name="collect-data-from-compute-resources"></a>從計算資源收集資料
診斷設定會收集 Azure 計算資源的資源記錄，就像任何其他資源一樣，而不是其客體作業系統或工作負載。 若要收集此資料，請安裝[Windows Azure 診斷代理程式](diagnostics-extension-overview.md)。 


## <a name="schema-of-platform-logs-in-storage-account"></a>儲存體帳戶中平臺記錄的架構

一旦您建立了診斷設定，一旦其中一個已啟用的記錄類別中發生事件，就會在儲存體帳戶中建立儲存體容器。 容器內的 blob 會使用下列命名慣例：

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，網路安全性群組的 blob 可能會有類似以下的名稱：

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

每個 PT1H.json blob 有一個 JSON blob，包含在 blob URL 指定時數內 (例如 h = 12) 發生的事件。 在目前這一小時，事件一發生就會附加到 PT1H.json 檔案。 分鐘值（m = 00）一定是00，因為資源記錄事件會分成每小時的個別 blob。

在 PT1H json 檔案中，每個事件都是以下列格式儲存。 這會使用通用的最上層架構，但每個 Azure 服務都是唯一的，如[資源記錄架構](diagnostic-logs-schema.md)和[活動記錄架構](activity-log-schema.md)中所述。

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> 平臺記錄會使用[JSON 行](http://jsonlines.org/)寫入至 blob 儲存體，其中每個事件都是一行，而分行符號表示新的事件。 此格式已于2018年11月執行。 在此日期之前，記錄會以 json 記錄陣列的形式寫入至 blob 儲存體，如[準備將格式變更為封存至儲存體帳戶 Azure 監視器平臺記錄](resource-logs-blob-format.md)中所述。

## <a name="next-steps"></a>後續步驟

* [深入瞭解資源記錄](platform-logs-overview.md)。
* [建立診斷設定以收集 Azure 中的記錄和計量](diagnostic-settings.md)。
* [下載 blob 以進行分析](../../storage/blobs/storage-quickstart-blobs-dotnet.md)。
* [使用 Azure 監視器封存 Azure Active Directory 記錄](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)。
