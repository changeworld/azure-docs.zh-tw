---
title: 工作負載隔離
description: 在 Azure 同步分析中設置與工作負荷組一起設置工作負載隔離的指南。
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
ms.openlocfilehash: 5d81dc1f4da6e952061496fa348d0f8e87b00b81
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742966"
---
# <a name="azure-synapse-analytics-workload-group-isolation-preview"></a>Azure 同步分析工作負荷組隔離(預覽)

本文介紹如何使用工作負載組配置工作負載隔離、包含資源以及應用運行時規則進行查詢執行。

## <a name="workload-groups"></a>工作負載群組

工作負載組是一組請求的容器,是如何在系統配置工作負載管理(包括工作負載隔離)的基礎。  使用[創建工作負荷組](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)語法創建工作負載組。  簡單的工作負載管理配置可以管理數據載入和用戶查詢。  例如,名為`wgDataLoads`的工作負載組將定義載入到系統中的數據的工作負載方面。 此外,名為的`wgUserQueries`工作負載組將定義運行查詢以從系統中讀取數據的使用者的工作負載方面。

以下各節將重點介紹工作負載組如何提供定義隔離、包含、請求資源定義和遵守執行規則的能力。

## <a name="workload-isolation"></a>工作負載隔離

工作負載隔離意味著資源僅保留給工作負荷組。  通過在[創建工作組](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)語法中將MIN_PERCENTAGE_RESOURCE參數配置為大於零來實現工作負載隔離。  對於需要遵循緊密 SRNA 的持續執行工作負載,隔離可確保始終可用於工作負載組的資源。

配置工作負載隔離隱式定義保證的併發級別。 例如,將設置為`MIN_PERCENTAGE_RESOURCE`30%`REQUEST_MIN_RESOURCE_GRANT_PERCENT`且設置為 2% 的工作負載組保證 15 個併發性。  併發性級別得到保證,因為工作負載組中隨時保留 15-2% 的資源插槽(無論配置`REQUEST_*MAX*_RESOURCE_GRANT_PERCENT`方式如何 )。  如果`REQUEST_MAX_RESOURCE_GRANT_PERCENT`大`REQUEST_MIN_RESOURCE_GRANT_PERCENT`於`CAP_PERCENTAGE_RESOURCE`和大於每個`MIN_PERCENTAGE_RESOURCE`請求 添加的其他資源。  如果`REQUEST_MAX_RESOURCE_GRANT_PERCENT``REQUEST_MIN_RESOURCE_GRANT_PERCENT`和`CAP_PERCENTAGE_RESOURCE`等於`MIN_PERCENTAGE_RESOURCE`且大於 ,則可能增加併發。  請考慮以下方法來確定保證併發性:

[保證併發]`MIN_PERCENTAGE_RESOURCE`[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] [ ] [ ] [ ] [ ] [ ]

> [!NOTE]
> 有特定的服務級別最低可行值min_percentage_resource。  有關詳細資訊,請參閱[有效值](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values)瞭解更多詳細資訊。

在沒有工作負載隔離的情況下,請求在[共享資源池](#shared-pool-resources)中運行。  不保證訪問共用池中的資源,並且以[重要性](sql-data-warehouse-workload-importance.md)為基礎分配。

配置工作負載隔離時應謹慎進行,因為資源已分配給工作負荷組,即使工作負荷組中沒有活動請求也是如此。 過度配置隔離會導致整體系統利用率降低。

使用者應避免使用配置 100% 工作負載隔離的工作負載管理解決方案:當在所有工作負載組中配置的min_percentage_resource總和等於 100% 時,可實現 100% 隔離。  這種類型的配置過於嚴格和殭化,為意外錯誤分類的資源請求留下的空間很小。 有一個規定允許從未配置為隔離的工作負載組執行一個請求。 分配給此請求的資源將在系統中顯示為零 DMV,並從系統預留資源借用少量資源授予。

> [!NOTE]
> 為確保最佳資源利用率,請考慮使用一些隔離的工作負載管理解決方案,以確保滿足 SL 並與基於[工作負載重要性](sql-data-warehouse-workload-importance.md)訪問的共享資源混合。

## <a name="workload-containment"></a>工作負載控制

工作負載包含是指限制工作負荷組可以使用的資源量。  通過在[創建工作組](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)語法中將CAP_PERCENTAGE_RESOURCE參數配置為小於 100 來實現工作負載控制。  請考慮使用者需要讀取系統訪問許可權,以便可以通過臨時查詢運行假設分析的情況。  這些類型的請求可能會對系統上運行的其他工作負載產生負面影響。  配置包含可確保資源量受到限制。

配置工作負載包含隱式定義最大併發級別。  CAP_PERCENTAGE_RESOURCE設置為 60%,REQUEST_MIN_RESOURCE_GRANT_PERCENT設置為 1%,則允許工作負載組達到 60 個併發級別。  請考慮下面包括的方法,以確定最大併發性:

[最大併發]`CAP_PERCENTAGE_RESOURCE`[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] [ ] [ ] [ ] [ ] [ ]

> [!NOTE]
> 當創建MIN_PERCENTAGE_RESOURCE級別大於零的工作負載組時,工作負載組的有效CAP_PERCENTAGE_RESOURCE不會達到 100%。  有關有效的運行時值,請參閱[sys.dm_workload_management_workload_groups_stats。](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="resources-per-request-definition"></a>每個要求定義的資源

工作負載組提供了一種機制,用於定義每個請求使用[創建工作組](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)語法中REQUEST_MIN_RESOURCE_GRANT_PERCENT和REQUEST_MAX_RESOURCE_GRANT_PERCENT參數分配的最小和最大資源量。  在這種情況下,資源是 CPU 和記憶體。  配置這些值決定了系統上可以實現多少資源和實現什麼級別的併發性。

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT是一個可選參數,它預設為REQUEST_MIN_RESOURCE_GRANT_PERCENT指定的相同值。

與選擇資源類一樣,配置REQUEST_MIN_RESOURCE_GRANT_PERCENT設置請求使用的資源的值。  set 值指示的資源量保證在請求開始執行之前分配給它。  對於從資源類遷移到工作負荷組的客戶,請考慮按照[「如何操作」](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)一文將從資源類映射到工作負荷組作為起點。

將REQUEST_MAX_RESOURCE_GRANT_PERCENT配置為大於REQUEST_MIN_RESOURCE_GRANT_PERCENT允許系統根據請求分配更多資源。  在安排請求時,系統根據共用池中的資源可用性和系統上的當前負載確定對請求的實際資源分配,該分配位於REQUEST_MIN_RESOURCE_GRANT_PERCENT和REQUEST_MAX_RESOURCE_GRANT_PERCENT之間。  計劃查詢時,資源必須存在於[共享資源池](#shared-pool-resources)中。  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT和REQUEST_MAX_RESOURCE_GRANT_PERCENT具有依賴於有效MIN_PERCENTAGE_RESOURCE和CAP_PERCENTAGE_RESOURCE值的有效值。  有關有效的運行時值,請參閱[sys.dm_workload_management_workload_groups_stats。](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="execution-rules"></a>執行規則

在臨時報告系統上,客戶可能會意外執行失控的查詢,從而嚴重影響其他人的工作效率。  系統管理員被迫花時間殺死失控的查詢以釋放系統資源。  工作負載組提供配置查詢執行超時規則以取消超出指定值的查詢的能力。  規則是通過在`QUERY_EXECUTION_TIMEOUT_SEC`[「創建工作群體](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)」語法中設置參數來配置的。

## <a name="shared-pool-resources"></a>共用池資源

共用池資源是未配置為隔離的資源。  MIN_PERCENTAGE_RESOURCE設置為零的工作負載組利用共用池中的資源執行請求。  CAP_PERCENTAGE_RESOURCE大於MIN_PERCENTAGE_RESOURCE的工作負載組也使用共享資源。  共用池中的可用資源量計算如下。

[共用池] = 100 - `MIN_PERCENTAGE_RESOURCE` [所有工作負載組的總和]

對共用池中資源的訪問許可權是按[重要性](sql-data-warehouse-workload-importance.md)分配的。  具有相同重要性級別的請求將首先訪問共用池資源。

## <a name="next-steps"></a>後續步驟

- [快速入門:配置工作負載隔離](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [將資源類別轉換為工作負荷群組](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)。
- [工作負載管理門戶監控](sql-data-warehouse-workload-management-portal-monitor.md)。  
