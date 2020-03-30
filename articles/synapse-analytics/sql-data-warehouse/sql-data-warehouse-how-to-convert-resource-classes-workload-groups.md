---
title: 將資源類轉換為工作負荷組
description: 瞭解如何創建類似于 Azure SQL 資料倉儲中的資源類的工作負荷組。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d20a811270b89772ab75bc298544a549caa01041
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350455"
---
# <a name="convert-resource-classes-to-workload-groups"></a>將資源類轉換為工作負荷組
工作負載組提供了隔離和包含系統資源的機制。  此外，工作負載組允許您為運行在其中的請求設置執行規則。  查詢超時執行規則允許在不進行使用者干預的情況下取消失控查詢。  本文介紹如何獲取現有資源類並創建具有類似配置的工作負載組。  此外，還添加了可選的查詢超時規則。

## <a name="understanding-the-existing-resource-class-configuration"></a>瞭解現有資源類配置
工作負載組需要一個參數`REQUEST_MIN_RESOURCE_GRANT_PERCENT`，該參數指定每個請求分配的總系統資源的百分比。  通過分配併發槽，為[資源類分配資源](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes)。  要確定要為其`REQUEST_MIN_RESOURCE_GRANT_PERCENT`指定的值，請使用 sys.dm_workload_management_workload_groups_stats <link tbd> DMV。  例如，下面的查詢返回可用於`REQUEST_MIN_RESOURCE_GRANT_PERCENT`參數的值以創建類似于 staticrc40 的工作負荷組。   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> 工作負載組根據系統總資源的百分比運行。  

由於工作負載組根據系統資源總資源的百分比運行，因此在向上和向下擴展時，分配給靜態資源類的資源相對於整個系統資源的百分比會發生變化。  例如，DW1000c 的 Staticrc40 分配了整個系統資源的 9.6%。  在 DW2000c 時，分配了 19.2%。  如果要擴展以進行併發性擴展，而不是為每個請求分配更多資源，則此模型類似。   

## <a name="create-workload-group"></a>創建工作負載組
使用 已知`REQUEST_MIN_RESOURCE_GRANT_PERCENT`，可以使用 CREATE 工作負荷<link>組語法創建工作負荷組。  您可以選擇指定大於零的`MIN_PERCENTAGE_RESOURCE`指定 以隔離工作負荷組的資源。  此外，您可以選擇指定`CAP_PERCENTAGE_RESOURCE`少於 100 個，以限制工作負荷組可以使用的資源量。  

下面的示例將`MIN_PERCENTAGE_RESOURCE`9.6% 的系統資源專用於`wgDataLoads`並保證一個查詢能夠一直運行。  此外，`CAP_PERCENTAGE_RESOURCE`設置為 38.4%，並將此工作負載組限制為四個併發請求。  通過將`QUERY_EXECUTION_TIMEOUT_SEC`參數設置為 3600，運行超過 1 小時的任何查詢將自動取消。

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>創建分類器
以前，查詢到資源類的映射是使用[sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)完成的。  要實現相同的功能並將請求映射到工作負載組，請使用["創建任務"語法](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)。  使用sp_addrolemember僅允許您根據登錄將資源映射到請求。  分類器提供登錄之外的其他選項，例如：
    - 標籤
    - 工作階段 (session)
    - 時間 以下示例將具有[選項 LABEL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label)設置為`factloads`上面創建的工作負載組的`wgDataLoads``AdfLogin`登錄名的查詢分配。

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>使用依例查詢進行測試
下面是一個依例查詢和一個 DMV 查詢，以確保工作負載組和分類器配置正確。

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 * 
  FROM nation 
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command 
  FROM sys.dm_pdw_exec_requests 
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>後續步驟

工作負載隔離連結 tbd 如何創建工作負載組連結 tbd