---
title: 工作負載隔離
description: 使用 Azure Synapse Analytics 中的工作負載群組來設定工作負載隔離的指導方針。
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
ms.openlocfilehash: 506aed16f1b8a6c631a759bb1367aef8242859ac
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734775"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Azure Synapse Analytics 工作負載群組隔離

本文說明如何使用工作負載群組來設定工作負載隔離、包含資源，以及針對執行查詢套用執行階段規則。

## <a name="workload-groups"></a>工作負載群組

工作負載群組是一組要求的容器，是在系統上設定工作負載管理的基礎，包括工作負載隔離。  工作負載群組是使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 語法來建立。  簡單的工作負載管理組態可以管理資料載入和使用者查詢。  例如，名為 `wgDataLoads` 的工作負載群組會定義載入系統中之資料的工作負載層面。 此外，名為 `wgUserQueries` 的工作負載群組會定義執行查詢以從系統讀取資料之使用者的工作負載層面。

下列各節將強調工作負載群組如何提供定義隔離、內含項目、要求資源定義，以及遵守執行規則的功能。

## <a name="workload-isolation"></a>工作負載隔離

工作負載隔離意指系統會以獨佔方式為工作負載群組保留資源。  您可以在 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 語法中，將 MIN_PERCENTAGE_RESOURCE 參數設為大於零，以達成工作負載隔離。  針對需要遵守嚴格 SLA 的連續執行工作負載，隔離可確保資源永遠可供工作負載群組使用。

設定工作負載隔離會隱含地定義保證的並行層級。 例如，`MIN_PERCENTAGE_RESOURCE` 設為30% 且 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 設定為 2% 的工作負載群組會保證有 15 個並行。  由於 15-2% 的資源位置會隨時保留在工作負載群組中 (無論如何設定 `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT`)，因此可保證並行層級。  如果 `REQUEST_MAX_RESOURCE_GRANT_PERCENT` 大於 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`，且 `CAP_PERCENTAGE_RESOURCE` 大於 `MIN_PERCENTAGE_RESOURCE`，則會針對每個要求新增額外的資源。  如果 `REQUEST_MAX_RESOURCE_GRANT_PERCENT` 和 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 相等，且 `CAP_PERCENTAGE_RESOURCE` 大於 `MIN_PERCENTAGE_RESOURCE`，則可能會有額外的平行存取。  請考慮使用下列方法來決定保證並行：

[Guaranteed Concurrency] = [`MIN_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> Min_percentage_resource 有特定的服務層級最小可行值。  如需詳細資訊，請參閱[有效值](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values)以取得進一步的詳細資料。

在沒有工作負載隔離的情況下，要求會在資源的[共用集區](#shared-pool-resources)中運作。  如此一來，系統不保證能夠存取共用集區中的資源，且會根據[重要性](sql-data-warehouse-workload-importance.md)來加以指派。

您應該謹慎地設定工作負載隔離，因為即便工作負載群組中沒有任何使用中的要求時，系統仍會將資源配置給工作負載群組。 過度設定隔離可能會導致系統整體使用率降低。

使用者應避免設定 100% 工作負載隔離的工作負載管理解決方案：當在所有工作負載群組上設定的 min_percentage_resource 總和等於 100% 時，就會達到100% 的隔離。  這種類型的組態過於嚴格且固定，而不會有任何不小心被誤分類的資源要求空間。 有一項佈建可允許從未設定為隔離的工作負載群組執行一個要求。 配置給此要求的資源在系統 DMV 中會顯示為零，並從系統保留的資源借用 smallrc 層級的資源授與。

> [!NOTE]
> 為確保最佳的資源使用率，請考慮使用某種隔離的工作負載管理解決方案，以確保符合 SLA，並與根據[工作負載重要性](sql-data-warehouse-workload-importance.md)存取的共用資源混合使用。

## <a name="workload-containment"></a>工作負載控制

工作負載控制指的是限制工作負載群組可以取用的資源數量。  您可以在 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 語法中，將 CAP_PERCENTAGE_RESOURCE 參數設為小於 100，以達成工作負載控制。  假設使用者需要系統的讀取權限，以便透過臨機操作查詢執行假設分析的案例。  這些類型的要求可能會對在系統上執行的其他工作負載造成負面影響。  設定控制可確保資源數量有所限制。

設定工作負載控制會隱含地定義並行層級上限。  如果 CAP_PERCENTAGE_RESOURCE 設定為 60%，且 REQUEST_MIN_RESOURCE_GRANT_PERCENT 設定為 1%，則工作負載群組最多允許 60 個並行層級。  請考慮以下列出的方法，以決定並行的最大值：

[Max Concurrency] = [`CAP_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> 建立具有大於零層級 MIN_PERCENTAGE_RESOURCE 的工作負載群組時，工作負載群組的有效 CAP_PERCENTAGE_RESOURCE 將無法達到 100%。  如需有效的執行階段值，請參閱 [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

## <a name="resources-per-request-definition"></a>依要求定義的資源

工作負載群組提供一種機制，可依 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 語法中的 REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 參數，定義每個要求所配置的最小和最大資源量。  在此情況下，資源為 CPU 和記憶體。  設定這些值會指示在系統上可以使用多少資源以及平行存取層級。

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT 是選擇性參數，預設為 REQUEST_MIN_RESOURCE_GRANT_PERCENT 指定的相同值。

就像選擇資源類別一樣，設定 REQUEST_MIN_RESOURCE_GRANT_PERCENT 會為要求所使用的資源設定值。  設定值所指示的資源量會在開始執行之前，保證配置給要求。  對於從資源類別遷移至工作負載群組的客戶，請考慮遵循[作法](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)文章，將資源類別對應至工作負載群組做為起點。

將 REQUEST_MAX_RESOURCE_GRANT_PERCENT 設定為大於 REQUEST_MIN_RESOURCE_GRANT_PERCENT 的值，可讓系統為每個要求配置更多資源。  在排程要求時，系統會根據共用集區中的資源可用性和系統上目前的負載，判斷要求的實際資源配置 (在 REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 之間)。  排程查詢時，資源必須存在於資源的[共用集區](#shared-pool-resources)中。  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 具有相依於有效 MIN_PERCENTAGE_RESOURCE 和 CAP_PERCENTAGE_RESOURCE 值的有效值。  如需有效的執行階段值，請參閱 [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

## <a name="execution-rules"></a>執行規則

在臨機操作的報告系統上，客戶可能會不小心執行失控查詢，因而嚴重影響其他項目的生產力。  系統管理員會強制終止失控查詢所花費的時間，以釋放系統資源。  工作負載群組提供設定查詢執行逾時規則的能力，以取消已超過指定值的查詢。  系統會設定 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 語法中的 `QUERY_EXECUTION_TIMEOUT_SEC` 參數設定規則。

## <a name="shared-pool-resources"></a>共用集區資源

共用集區資源是未設定為隔離的資源。  MIN_PERCENTAGE_RESOURCE 設為零的工作負載群組，會利用共用集區中的資源來執行要求。  CAP_PERCENTAGE_RESOURCE 大於 MIN_PERCENTAGE_RESOURCE 的工作負載群組也會使用共用資源。  共用集區中可用的資源數量計算方式如下。

[Shared Pool] = 100 - [所有工作負載群組的 `MIN_PERCENTAGE_RESOURCE` 總數]

如此一來，系統會根據[重要性](sql-data-warehouse-workload-importance.md)來配置共用集區中的資源。  具有相同重要性層級的要求將會以先進先出或先出的方式存取共用集區資源。

## <a name="next-steps"></a>後續步驟

- [快速入門：設定工作負載隔離](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [將資源類別轉換成工作負載群組](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)。
- [工作負載管理入口網站監視](sql-data-warehouse-workload-management-portal-monitor.md)。  
