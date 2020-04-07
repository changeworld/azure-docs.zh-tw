---
title: 工作負載分類
description: 使用分類來管理 Azure 突觸分析中查詢的併發性、重要性和計算資源的指導。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e7aa0c402878c994aabe4e12d811a99e300d7e67
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743647"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Azure 突觸分析工作負荷分類

本文介紹了分配工作負載組的工作負載分類過程,以及使用 Azure Synapse 中的 Synapse SQL 池對傳入請求的重要性。

## <a name="classification"></a>分類

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

工作負載管理分類允許通過分配[資源類](resource-classes-for-workload-management.md#what-are-resource-classes)和[重要性](sql-data-warehouse-workload-importance.md)將工作負載策略應用於請求。

雖然對數據倉庫工作負載進行分類的方法有很多,但最簡單和最常見的分類是載入和查詢。 使用插入、更新和刪除語句載入資料。  使用 Select 查詢數據。 數據倉庫解決方案通常具有用於載入活動的工作負載策略,例如為具有更多資源的更高資源類分配更高資源。 不同的工作負載策略可以應用於查詢,例如與載入活動相比,其重要性較低。

您還可以對負載和查詢工作負載進行分類分類。 子分類使您能夠對工作負載進行更多控制。 例如,查詢工作負荷可以由多維數據集刷新、儀錶板查詢或臨時查詢組成。 您可以使用不同的資源類或重要性設置對每個查詢工作負荷進行分類。 負載也可以受益於子分類。 可以將大型轉換分配給較大的資源類。 更高的重要性可用於確保關鍵銷售數據在天氣數據或社交數據饋送之前載入。

並非所有語句都進行分類,因為它們不需要資源或需要重要來影響執行。  DBCC 命令、BEGIN、COMMIT 和 ROLLBACK 交易語句不分類。

## <a name="classification-process"></a>分類程序

Azure Synapse 中的 Synapse SQL 池的分類今天通過將使用者分配給具有相應資源類的角色(使用[sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest))來實現。 使用此功能,在登錄到資源類之外對請求進行特徵描述的能力受到限制。 現在,使用[CREATE 工作分類語法](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)提供了更豐富的分類方法。  使用此語法,Synapse SQL 池`workload_group`使用者可以通過 參數分配重要性以及將多少系統資源分配給請求。

> [!NOTE]
> 根據請求評估分類。 單個會話中的多個請求可以分類不同。

## <a name="classification-weighting"></a>分類加權

作為分類過程的一部分,已進行加權以確定分配了哪個工作負載組。  加權如下所示：

|分類器參數 |Weight   |
|---------------------|---------|
|成員名稱:使用者      |64       |
|成員名稱:角色      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

參數`membername`是必填項。  但是,如果指定的成員名稱是資料庫使用者而不是資料庫角色,則使用者的權重更高,因此選擇了該分類器。

如果使用者是多個角色的成員，且被指派了不同的資源類別或在多個分類器中相符，該使用者將獲得最高的資源類別指派。  此行為與現有資源類分配行為一致。

## <a name="system-classifiers"></a>系統分類器

工作負載分類具有系統工作負載分類器。 系統分類器將現有資源類角色成員身份映射到具有正常重要性的資源類資源分配。 無法刪除系統分類器。 要檢視系統分類器,可以執行以下查詢:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>將資源類別配置與分類器混合

代表您創建的系統分類器提供了遷移到工作負載分類的簡便路徑。 使用具有分類優先順序的資源類角色映射,可能會導致在開始創建具有重要性的新分類器時錯誤分類。

請考慮下列案例：

- 現有數據倉庫具有資料庫使用者 DBAUser 分配給較大的 c 資源類角色。 資源類分配使用sp_addrolemember完成。
- 數據倉庫現在使用工作負載管理進行更新。
- 為了測試新的分類語法,資料庫角色 DBARole(DBAUser 是其成員)為其創建了一個分類器,將它們映射到中度和重要性。
- 當 DBAUser 登錄並運行查詢時,查詢將分配給更大的 c。 因為使用者優先於角色成員身份。

為了簡化故障排除錯誤分類,我們建議您在創建工作負載分類器時刪除資源類角色映射。  下面的代碼返回現有資源類角色成員身份。  對於從相應資源類返回的每個成員名稱運行[sp_droprolemember。](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

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

- 有關創建分類器的詳細資訊,請參閱[創建任務程式(轉算 SQL)。](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  
- 有關如何創建工作負載分類器[創建工作負載分類器](quickstart-create-a-workload-classifier-tsql.md),請參閱快速入門。
- 請參考[設定工作負載重要性](sql-data-warehouse-how-to-configure-workload-importance.md)的「如何」 的文章以及如何[管理和監控工作負載管理](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)。
- 請參閱 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 以檢視查詢和所指派的重要性。
