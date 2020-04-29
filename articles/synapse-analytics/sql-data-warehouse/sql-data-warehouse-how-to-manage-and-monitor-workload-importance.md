---
title: 管理及監視工作負載的重要性
description: 瞭解如何在 Azure Synapse Analytics 中管理和監視要求層級的重要性。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 3efd8a776542616a9ceefba331b06406540905a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633329"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>管理和監視 Azure Synapse 分析中的工作負載重要性

使用 Dmv 和目錄檢視，管理和監視 Azure Synapse 中的 Synapse SQL 要求層級重要性。

## <a name="monitor-importance"></a>監視重要性

使用 [ [dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)動態管理] 視圖中的 [新重要性] 資料行來監視重要性。
下列監視查詢會顯示查詢的提交時間和開始時間。 查看提交時間和開始時間以及重要性，以查看重要性如何影響排程。

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

若要進一步查看查詢的排程方式，請使用目錄檢視。

## <a name="manage-importance-with-catalog-views"></a>使用目錄檢視管理重要性

[Sys.databases] workload_management_workload_classifiers 目錄檢視包含分類器的資訊。 若要排除對應至資源類別的系統定義分類器，請執行下列程式碼：

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

目錄檢視[workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)，其中包含建立分類器時所使用之參數的資訊。  下列查詢顯示已使用 ExecutiveReports 在值的```membername```參數上建立 ExecReportsClassifier：

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![查詢結果](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

若要簡化分類誤判的疑難排解，建議您在建立工作負載分類器時，移除資源類別角色對應。 下列程式碼會傳回現有的資源類別角色成員資格。 針對每個```membername```從對應的資源類別傳回的執行 sp_droprolemember。
以下是在卸載工作負載分類器之前，檢查是否存在的範例：

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>後續步驟

- 如需分類的詳細資訊，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)。
- 如需重要性的詳細資訊，請參閱[工作負載重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [前往設定工作負載重要性](sql-data-warehouse-how-to-configure-workload-importance.md)
