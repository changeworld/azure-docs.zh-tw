---
title: 設定工作負載的重要性
description: 瞭解如何在 Azure 突觸分析中佈建要求級別重要性。
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
ms.openlocfilehash: 85fa6a989ae1a6214392914ffd46cd12af27a8d2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350485"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>在 Azure 突觸分析中配置工作負載重要性

在 Azure Synapse 的 SQL 分析中設置重要性允許您影響查詢的調度。 具有較高重要性的查詢將安排在重要性較低的查詢之前運行。 要為查詢分配重要性，您需要創建工作負載分類器。

## <a name="create-a-workload-classifier-with-importance"></a>創建具有重要性的工作負載分類器

通常，在資料倉儲方案中，使用者需要其查詢才能快速運行。  使用者可能是需要運行報表的公司高管，也可以是運行臨時查詢的分析師。 創建工作負載分類器以為查詢分配重要性。  以下示例使用新的[創建工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)語法創建兩個分類器。  成員名稱可以是單個使用者或組。 單個使用者分類優先于角色分類。 要查找現有資料倉儲使用者，運行：

```sql
Select name from sys.sysusers
```

要創建工作負載分類器，請為具有更高重要性的使用者運行：

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

要為運行具有較低重要性的特設查詢的使用者創建工作負載分類器，請執行：  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>後續步驟
- 有關工作負載管理的詳細資訊，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)
- 有關重要性的詳細資訊，請參閱[工作負載重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [轉到管理和監視工作負載重要性](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
