---
title: 使用 REST API 暫停、繼續、調整
description: 透過 REST API 管理 Azure Synapse Analytics 資料倉儲中的計算能力。
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: bbb8e82710b4c8ca7736b53d427b3880faf2be05
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213273"
---
# <a name="rest-apis-for-azure-synapse-analytics-data-warehouse"></a>適用於 Azure Synapse Analytics 資料倉儲的 REST API

用於管理 Azure Synapse Analytics 資料倉儲中計算能力的 REST API。

## <a name="scale-compute"></a>調整計算

若要變更資料倉儲單位，請使用[建立或更新資料庫](/rest/api/sql/databases/createorupdate?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) REST API。 下例會將裝載於 MyServer 伺服器之資料庫 MySQLDW 的資料倉儲單位設定為 DW1000。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>暫停計算

若要暫停資料庫，請使用[暫停資料庫](/rest/api/sql/databases/pause?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) REST API。 下例會暫停裝載在 Server01 伺服器上的 Database02 資料庫。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>繼續計算

若要啟動資料庫，請使用[繼續資料庫](/rest/api/sql/databases/resume?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) REST API。 下例會啟動裝載在 Server01 伺服器上的 Database02 資料庫。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>檢查資料庫狀態

> [!NOTE]
> 目前，檢查資料庫狀態可能會在資料庫正在完成線上工作流程時恢復上線，因而導致連線錯誤。 如果使用此 API 呼叫來觸發連線嘗試，則可能需要在應用程式程式碼中增加 2 到 3 分鐘的延遲。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>取得維護排程

檢查已針對資料倉儲設定的維護排程。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>設定維護排程

設定及更新現有資料倉儲上的維護排程。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[管理計算能力](sql-data-warehouse-manage-compute-overview.md)。
