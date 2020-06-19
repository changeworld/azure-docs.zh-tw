---
title: 將資源類別轉換成工作負載群組
description: 了解如何建立與 Azure SQL 資料倉儲中資源類別類似的工作負載群組。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f0cc0cd7233d0c16cae8389fcddd50a16cf96bd2
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683633"
---
# <a name="convert-resource-classes-to-workload-groups"></a>將資源類別轉換成工作負載群組

工作負載群組提供隔離和包含系統資源的機制。  此外，工作負載群組可讓您設定在其中所執行要求的執行規則。  查詢逾時執行規則允許取消失控查詢而不需要使用者介入。  本文說明如何採用現有的資源類別，並使用類似的設定來建立工作負載群組。  此外，也會新增選擇性的查詢逾時規則。

> [!NOTE]
> 如需同時使用工作負載群組和資源類別的指引，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)概念文件中的[混合資源類別指派與分類器](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers)一節。

## <a name="understanding-the-existing-resource-class-configuration"></a>了解現有的資源類別設定

工作負載群組需要名為 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 的參數，指定每個要求配置的整體系統資源百分比。  系統會透過配置並行位置，為[資源類別](resource-classes-for-workload-management.md#what-are-resource-classes)完成資源配置。  若要判斷要為 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 指定的值，請使用 sys.dm_workload_management_workload_groups_stats <link tbd> DMV。  例如，下列查詢會傳回可用於 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 參數的值，以建立類似 staticrc40 的工作負載群組。

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> 工作負載群組會根據整體系統資源的百分比來運作。  

因為工作負載群組是根據整體系統資源的百分比來運作，所以當您擴大或縮小時，配置給靜態資源類別的資源百分比會與整體系統資源變更相關。  例如，DW1000c 的 staticrc40 會配置 19.2% 的整體系統資源。  在 DW2000c 中，會配置 9.6%。  無論您是想要針對並行處理擴大，或針對每個要求配置更多資源，此模型都很類似。

## <a name="create-workload-group"></a>建立工作負載群組

使用已知的 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`，您可以使用 CREATE WORKLOAD GROUP <link> 語法來建立工作負載群組。  您可以選擇性地指定大於零的 `MIN_PERCENTAGE_RESOURCE`，以隔離工作負載群組的資源。  此外，您可以選擇性地指定小於 100 的 `CAP_PERCENTAGE_RESOURCE`，以限制工作負載群組可以使用的資源量。  

下列範例會將 `MIN_PERCENTAGE_RESOURCE` 設定為專用 `wgDataLoads` 的 9.6% 系統資源，並保證一個查詢總是能夠執行。  此外，`CAP_PERCENTAGE_RESOURCE` 設定為 38.4%，並將此工作負載群組限制為四個並行要求。  藉由將 `QUERY_EXECUTION_TIMEOUT_SEC` 參數設定為 3600，任何執行超過 1 小時的查詢都會自動取消。

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>建立分類器

先前，對資源類別的查詢對應是使用 [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class) 來完成。  若要達到相同的功能，並將要求對應至工作負載群組，請使用 [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 語法。  使用 sp_addrolemember 只允許您根據登入將資源對應至要求。  分類器除了登入以外，還提供其他選項，例如：
    - 標籤
    - 工作階段 (session)
    - 以下範例會從 `AdfLogin` 登入指派查詢，而該登入的 [OPTION LABEL](sql-data-warehouse-develop-label.md) 也設定為 `factloads` 到上方建立的工作負載群組 `wgDataLoads`。

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>使用範例查詢進行測試

以下是範例查詢和 DMV 查詢，以確保工作負載群組和分類器已正確設定。

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>後續步驟

- [工作負載隔離](sql-data-warehouse-workload-isolation.md)
- [如何建立工作負載群組](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?&view=azure-sqldw-latest)
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
