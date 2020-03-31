---
title: 管理及監視工作負載的重要性
description: 瞭解如何在 Azure 突觸分析中管理和監視請求級別重要性。
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
ms.openlocfilehash: fdac8ebc56291292559f6718de5f0092c7d8e063
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350477"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>管理和監視 Azure 突觸分析中的工作負載重要性

使用 DMV 和目錄檢視在 Azure Synaps 中管理和監視 SQL 分析請求級別重要性。

## <a name="monitor-importance"></a>監控重要性

使用[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)動態管理檢視中的新重要性列監視重要性。
以下監視查詢顯示查詢的提交時間和開始時間。 查看提交時間和開始時間以及重要性，瞭解重要性如何影響排程。

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

要進一步查看查詢的進度，請使用目錄檢視。

## <a name="manage-importance-with-catalog-views"></a>使用目錄檢視管理重要性

sys.workload_management_workload_classifiers目錄檢視包含有關分類器的資訊。 要排除映射到資源類的系統定義的分類器，請執行以下代碼：

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

目錄檢視[sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest)包含有關創建分類器中使用的參數的資訊。  下面的查詢顯示 ExecReportsClassifier 是在具有執行報告```membername```的值的參數上創建的：

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![查詢結果](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

為了簡化故障排除錯誤分類，我們建議您在創建工作負載分類器時刪除資源類角色映射。 下面的代碼返回現有資源類角色成員身份。 運行從相應資源```membername```類返回的每個sp_droprolemember。
下面是在刪除工作負載分類器之前檢查是否存在的示例：

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>後續步驟
- 有關分類的詳細資訊，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)。
- 有關重要性的詳細資訊，請參閱[工作負載重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [轉到配置工作負載重要性](sql-data-warehouse-how-to-configure-workload-importance.md)
