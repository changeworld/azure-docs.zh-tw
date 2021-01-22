---
title: 管理及監視專用 SQL 集區中的工作負載重要性
description: 瞭解如何管理和監視 Azure Synapse Analytics 的要求層級重要性專用 SQL 集區。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 12b75ad3746cd0f54e27e474e0fd13bb0bba0e05
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685237"
---
# <a name="manage-and-monitor-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>管理及監視專用 SQL 集區中 Azure Synapse Analytics 的工作負載重要性

使用 Dmv 和目錄檢視，在 Azure Synapse 中管理和監視專用的 SQL 集區要求層級重要性。

## <a name="monitor-importance"></a>監視重要性

使用 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 動態管理檢視中的新 [重要性] 資料行來監視重要性。
下列監視查詢會顯示查詢的提交時間和開始時間。 查看提交時間和開始時間以及重要性，以查看重要性如何影響排程。

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

若要進一步瞭解如何排程查詢，請使用類別目錄檢視。

## <a name="manage-importance-with-catalog-views"></a>使用目錄檢視管理重要性

Sys.workload_management_workload_classifiers 目錄] 視圖包含分類器的資訊。 若要排除對應至資源類別的系統定義分類器，請執行下列程式碼：

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

目錄檢視（ [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)）包含建立分類器時所用參數的資訊。  下列查詢顯示 ExecReportsClassifier 是在 ```membername``` 具有 ExecutiveReports 值的參數上建立的：

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![查詢結果](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

若要簡化分類誤判的疑難排解，建議您在建立工作負載分類器時，移除資源類別角色對應。 下列程式碼會傳回現有的資源類別角色成員資格。 針對每個 ```membername``` 從對應資源類別傳回的，執行 sp_droprolemember。
以下是在卸載工作負載分類器之前檢查是否存在的範例：

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>下一步

- 如需分類的詳細資訊，請參閱 [工作負載分類](sql-data-warehouse-workload-classification.md)。
- 如需重要性的詳細資訊，請參閱[工作負載重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [移至設定工作負載重要性](sql-data-warehouse-how-to-configure-workload-importance.md)
