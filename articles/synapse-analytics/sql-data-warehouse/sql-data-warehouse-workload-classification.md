---
title: 工作負載分類
description: 使用分類來管理 Azure Synapse Analytics 中查詢的並行、重要性和計算資源的指引。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 266eebc8322b5fc648180c0524abc973a4b60373
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212372"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Azure Synapse 分析工作負載分類

本文說明在 Azure Synapse 中使用 Synapse SQL 集區指派工作負載群組和對傳入要求具有重要性的工作負載分類程式。

## <a name="classification"></a>分類

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

工作負載管理分類可讓您透過指派[資源類別](resource-classes-for-workload-management.md#what-are-resource-classes)和[重要性](sql-data-warehouse-workload-importance.md)，將工作負載原則套用至要求。

雖然有許多方法可以分類資料倉儲工作負載，但最簡單且最常見的分類是載入和查詢。 您可以使用 insert、update 和 delete 語句來載入資料。  您可以使用 [選取] 來查詢資料。 資料倉儲解決方案通常會有負載活動的工作負載原則，例如指派較高的資源類別與更多資源。 不同的工作負載原則可以套用至查詢，例如較低的重要性與載入活動。

您也可以 subclassify 負載和查詢工作負載。 Subclassification 可讓您更充分掌控您的工作負載。 例如，查詢工作負載可以包含 cube 重新整理、儀表板查詢或特定查詢。 您可以使用不同的資源類別或重要性設定來分類每個查詢工作負載。 載入也可以受益于 subclassification。 大型的轉換可以指派給較大的資源類別。 較高的重要性可以用來確保重要的銷售資料是在氣象資料或社交資料摘要之前的載入器。

並非所有的語句都會分類，因為它們不需要資源，也不需要重要性來影響執行。  DBCC 命令、BEGIN、COMMIT 和 ROLLBACK TRANSACTION 語句不會分類。

## <a name="classification-process"></a>分類程序

現今在 Azure Synapse 中 Synapse SQL 集區的分類是藉由將使用者指派給具有對應資源類別的角色（使用[sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)）來達成。 對資源類別登入以外的要求加上特性的能力，會受到這項功能的限制。 「[建立工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)」語法現在提供更豐富的分類方法。  使用此語法，Synapse SQL 集區使用者可以透過參數指派重要性，以及指派給要求的系統資源數量 `workload_group` 。

> [!NOTE]
> 分類是根據每個要求來評估。 單一會話中的多個要求可以不同方式分類。

## <a name="classification-weighting"></a>分類加權

做為分類程式的一部分，加權是用來判斷指派的工作負載群組。  加權如下所示：

|分類器參數 |Weight   |
|---------------------|---------|
|成員名稱： USER      |64       |
|成員名稱： ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

`membername`參數是必要的。  不過，如果指定的成員名稱是資料庫使用者，而不是資料庫角色，則使用者的加權會較高，因此會選擇分類器。

如果使用者是多個角色的成員，且被指派了不同的資源類別或在多個分類器中相符，該使用者將獲得最高的資源類別指派。  這種行為與現有的資源類別指派行為一致。

## <a name="system-classifiers"></a>系統分類器

工作負載分類具有系統工作負載分類器。 系統分類器會將現有的資源類別角色成員資格對應至資源類別資源配置，並具有一般重要性。 無法卸載系統分類器。 若要查看系統分類器，您可以執行下列查詢：

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>混合使用分類器的資源類別指派

代表您建立的系統分類器提供遷移至工作負載分類的簡單路徑。 使用具有分類優先順序的資源類別角色對應，在您開始建立具有重要性的新分類器時，可能會導致分類誤判。

請考慮下列案例：

- 現有的資料倉儲具有指派給 largerc 資源類別角色的資料庫使用者 DBAUser。 資源類別指派是使用 sp_addrolemember 完成。
- 現在已使用工作負載管理來更新資料倉儲。
- 若要測試新的分類語法，資料庫角色 DBARole （其 DBAUser 是的成員）已建立分類器，可將其對應至 mediumrc 和高重要性。
- 當 DBAUser 登入並執行查詢時，會將查詢指派給 largerc。 因為使用者優先于角色成員資格。

若要簡化分類誤判的疑難排解，建議您在建立工作負載分類器時，移除資源類別角色對應。  下列程式碼會傳回現有的資源類別角色成員資格。  針對每個從對應的資源類別傳回的成員名稱執行[sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 。

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>後續步驟

- 如需建立分類器的詳細資訊，請參閱[建立工作負載分類器（transact-sql）](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。  
- 請參閱如何建立工作負載分類器建立工作[負載分類器](quickstart-create-a-workload-classifier-tsql.md)的快速入門。
- 請參閱操作說明文章以[設定工作負載重要性](sql-data-warehouse-how-to-configure-workload-importance.md)，以及如何[管理和監視工作負載管理](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)。
- 請參閱 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 以檢視查詢和所指派的重要性。
