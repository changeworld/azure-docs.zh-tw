---
title: 設定工作負載的重要性
description: 瞭解如何在 Azure 突觸分析中設置請求級別重要性。
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
ms.openlocfilehash: 0ab7b8be8780f7edb2734d99587bc7709ced9436
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633356"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>在 Azure 突觸分析中設定工作負載重要性

在 Azure Synapse Synapse 的 Synapse SQL 中設置重要性允許您影響查詢的調度。 具有較高重要性的查詢將安排在重要性較低的查詢之前運行。 要為查詢分配重要性,您需要創建工作負載分類器。

## <a name="create-a-workload-classifier-with-importance"></a>建立具有重要性的工作負載分類器

通常,在數據倉庫方案中,使用者需要其查詢才能快速運行。  使用者可能是需要運行報表的公司高管,也可以是運行臨時查詢的分析師。 創建工作負載分類器以為查詢分配重要性。  以下範例使用新的[創建工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)語法創建兩個分類器。 `Membername`可以是單個使用者或組。 單個用戶分類優先於角色分類。 要查找現有資料倉儲用戶,執行:

```sql
Select name from sys.sysusers
```

要建立工作負載分類器,請為具有更高重要性的使用者執行:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

要為執行具有較低重要性的特設查詢的使用者建立工作負載分類器,請執行:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>後續步驟

- 有關工作負載管理的詳細資訊,請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)
- 有關重要性的詳細資訊,請參閱[工作負載重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [移至管理及監控工作負載重要性](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
