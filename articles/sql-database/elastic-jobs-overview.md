---
title: 彈性資料庫作業 (預覽)
description: 設定彈性資料庫作業（預覽），以在一或多個 Azure SQL 資料庫中執行 Transact-sql （T-sql）腳本
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: e5b07ac0e9421cbca034b17c573cab16641f49f7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214484"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>建立、設定及管理彈性作業

在本文中，您將了解如何建立、設定和管理彈性作業。

如果您未曾使用彈性作業，請[深入了解 Azure SQL 資料庫中的作業自動化概念](sql-database-job-automation-overview.md)。

## <a name="create-and-configure-the-agent"></a>建立和設定代理程式

1. 建立或識別空的 S0 或更高版本的 SQL 資料庫。 此資料庫將在彈性作業代理程式建立期間作為「作業資料庫」。
2. 在[入口網站](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent)中或使用[PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent)建立彈性作業代理程式。

   ![建立彈性作業代理程式](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>建立、執行和管理作業

1. 使用[PowerShell](elastic-jobs-powershell.md)或[T-sql](elastic-jobs-tsql.md#create-a-credential-for-job-execution)，在*作業資料庫*中建立作業執行的認證。
2. 使用[PowerShell](elastic-jobs-powershell.md)或[t-sql](elastic-jobs-tsql.md#create-a-target-group-servers)定義目標群組（您想要對其執行作業的資料庫）。
3. 在將要執行作業的每個資料庫中建立作業代理程式認證 [(將使用者 (或角色) 新增至群組中的每個資料庫)](sql-database-manage-logins.md)。 如需範例，請參閱 [PowerShell 教學課程](elastic-jobs-powershell.md)。
4. 使用[PowerShell](elastic-jobs-powershell.md)或[t-sql](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases)建立作業。
5. 使用 [PowerShell](elastic-jobs-powershell.md) 或 [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases) 新增作業步驟。
6. 使用[PowerShell](elastic-jobs-powershell.md#run-the-job)或[t-sql](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job)執行作業。
7. 使用入口網站、 [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions)或[t-sql](elastic-jobs-tsql.md#monitor-job-execution-status)監視作業執行狀態。

   ![入口網站](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>用於執行作業的認證

作業會在執行時使用[資料庫範圍認證](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)來連線到目標群組所指定的資料庫。 如果目標群組包含伺服器或集區，這些資料庫範圍認證可用來連線到 master 資料庫，進而列舉可用的資料庫。

設定適當的認證來執行作業，可能會有點混淆，因此請牢記下列要點：

- 必須在「作業資料庫」中建立資料庫範圍認證。
- **所有目標資料庫都必須具有登入，而且有[足夠的權限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)，才能順利完成作業** (下圖中的 `jobuser`)。
- 認證可跨作業重複使用，而認證密碼會經過加密並受到保護，以免具有作業物件唯讀權限的使用者加以使用。

下圖設計用來協助了解及設定適當的作業認證。 **請記得在每個需要執行作業的資料庫 (所有「目標使用者資料庫」) 中建立使用者**。

![彈性作業認證](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>安全性最佳做法

處理彈性作業有幾個最佳做法考量：

- 將 API 的使用限制為受信任的個人。
- 認證應該具有執行作業步驟所需的最低權限。 如需詳細資訊，請參閱 [SQL Server 中的授權和權限](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)。
- 使用伺服器和/或集區目標群組的成員時，強烈建議建立個別的認證，而該認證須具有 master 資料庫的權限，可在作業執行前檢視/列出用來擴充伺服器和/或集區的資料庫清單。

## <a name="agent-performance-capacity-and-limitations"></a>代理程式效能、容量和限制

彈性作業在等待長時間執行的作業完成時，會使用最少的運算資源。

視資料庫的目標群組大小和作業所需的執行時間 (並行背景工作數目) 而定，代理程式需要不同的計算量和「作業資料庫」效能 (目標愈多和作業數目較高，所需的計算量就愈高)。

預覽版本目前受限於 100 個並行作業。

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>避免作業降低目標資料庫效能

若要確保在對 SQL 彈性集區中的資料庫執行作業時，資源不會負擔過重，則可以設定作業來限制作業可以同時執行的資料庫數目。

藉由在 T-sql 中設定 `sp_add_jobstep` 預存程式的 `@max_parallelism` 參數，或在 PowerShell 中 `Add-AzSqlElasticJobStep -MaxParallelism`，設定作業執行所在的並行資料庫數目。

## <a name="best-practices-for-creating-jobs"></a>建立作業的最佳做法

### <a name="idempotent-scripts"></a>等冪指令碼
作業的 T-SQL 指令碼必須[具有等冪性](https://en.wikipedia.org/wiki/Idempotence)。 **等冪**表示如果指令碼成功，而且再次執行，就會出現相同的結果。 指令碼可能會因為暫時性網路問題而失敗。 在此情況下，工作會自動重新執行指令碼，達到預設的次數才會停止。 即使等冪指令碼已成功執行兩次 (或更多次)，仍會有相同的結果。

簡單的策略是在建立物件之前，測試其是否存在。


```sql
IF NOT EXISTS (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

同樣地，指令碼必須以邏輯方式測試並反駁它所找到的任何條件，才能夠順利執行。



## <a name="next-steps"></a>後續步驟

- [使用 PowerShell 建立及管理彈性作業](elastic-jobs-powershell.md)
- [使用 Transact-SQL (T-SQL) 來建立及管理彈性作業](elastic-jobs-tsql.md)
