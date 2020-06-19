---
title: 設定工作負載的重要性
description: 了解如何在 Azure Synapse Analytics 中設定要求層級重要性。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9440d5a13973a245320bc465e3997e3cdf414b3f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660319"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中設定工作負載重要性

在適用於 Azure Synapse 的 Synapse SQL 中設定重要性，可讓您影響查詢的排程。 重要性較高的查詢會排程在重要性較低的查詢之前執行。 若要將重要性指派給查詢，您必須建立工作負載分類器。

## <a name="create-a-workload-classifier-with-importance"></a>建立具有重要性的工作負載分類器

通常在資料倉儲案例中，您的使用者在忙碌的系統上需要快速執行其查詢。  使用者可能是需要執行報表的公司主管，或使用者可能是執行臨機操作查詢的分析師。 若要指派重要性，您必須建立工作負載分類器，並將重要性指派給查詢。  下列範例使用[建立工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)語法來建立兩個分類器。 `Membername` 可以是單一使用者或群組。  若要尋找現有的資料倉儲使用者，請執行：

```sql
Select name from sys.sysusers
```

若要針對具有較高重要性的使用者建立工作負載分類器，請執行：

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

若要針對以較低重要性執行臨機操作查詢的使用者建立工作負載分類器，請執行：  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>後續步驟

- 如需工作負載管理的詳細資訊，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)
- 如需重要性的詳細資訊，請參閱[工作負載重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [移至管理及監視工作負載重要性](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
