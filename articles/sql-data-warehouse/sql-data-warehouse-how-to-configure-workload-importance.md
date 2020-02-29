---
title: 設定工作負載的重要性
description: 瞭解如何在 Azure Synapse Analytics 中設定要求層級的重要性。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 8b2a4333717938edf9f3039e29e8df88cece7cc1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196792"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>在 Azure Synapse 分析中設定工作負載重要性

在適用于 Azure Synapse 的 SQL 分析中設定重要性，可讓您影響查詢的排程。 重要性較高的查詢會排程在較低重要性的查詢之前執行。 若要指派查詢的重要性，您必須建立工作負載分類器。

## <a name="create-a-workload-classifier-with-importance"></a>建立具有重要性的工作負載分類器

通常在資料倉儲案例中，您的使用者需要其查詢才能快速執行。  使用者可能是需要執行報表之公司的主管，或使用者可能是執行臨機操作查詢的分析師。 您可以建立工作負載分類器，將重要性指派給查詢。  下列範例使用新的[建立工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)語法來建立兩個分類器。  成員名稱可以是單一使用者或群組。 個別使用者分類的優先順序高於角色分類。 若要尋找現有的資料倉儲使用者，請執行：

```sql
Select name from sys.sysusers
```

若要針對重要性較高的使用者建立工作負載分類器，請執行：

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

若要以較低的重要性執行執行臨機操作查詢的使用者，建立工作負載分類器：  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>後續步驟
- 如需工作負載管理的詳細資訊，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)
- 如需重要性的詳細資訊，請參閱[工作負載重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [前往管理和監視工作負載重要性](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
