---
title: 快速入門：建立工作負載分類器 - T-SQL
description: 使用 T-SQL 建立具有高重要性的工作負載分類器。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e757c8047bf6d634ab6d7cbc8963087c0eccc46a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677363"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>快速入門：使用 T-SQL 建立工作負載分類器

在本快速入門中，您會為貴組織執行長快速建立具有高重要性的工作負載分類器。 此工作負載分類器可讓執行長查詢的優先順序高於佇列中重要性較低的其他查詢。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

> [!NOTE]
> 在 Azure Synapse Analytics 中建立專用 SQL 集區執行個體，可能會產生新的可計費服務。  如需詳細資訊，請參閱 [Azure Synapse Analytics 定價](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。
>
>

## <a name="prerequisites"></a>Prerequisites

本快速入門假設您已在 Azure Synapse Analytics 中佈建專用 SQL 集區，且已有 CONTROL DATABASE 權限。 若您需要建立 SQL 集區，請使用 [建立與連線 - 入口網站](create-data-warehouse-portal.md)建立名為 **mySampleDataWarehouse** 的專用 SQL 集區。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-login-for-theceo"></a>建立 TheCEO 的登入

使用 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 來為 'TheCEO' 在 `master` 資料庫中建立 SQL Server 驗證登入。

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>建立使用者

在 mySampleDataWarehouse 中[建立使用者](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)「TheCEO」

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>建立工作負載分類器

為「TheCEO」建立具有高重要性的[工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>檢視現有的分類器

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>清除資源

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

您需對資料倉儲單位和專用 SQL 集區中儲存的資料付費。 這些計算和儲存體資源會分開計費。

- 如果您需要將資料保留在儲存體中，可以在您不使用專用 SQL 集區時暫停計算。 暫停計算，您只需支付資料儲存體的費用。 當您準備好使用資料時，會繼續計算。
- 如果您想要移除未來的費用，可以將專用 SQL 集區刪除。

遵循下列步驟清除資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取您的專用 SQL 集區。

    ![清除資源](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. 若要暫停計算，請選取 [暫停]  按鈕。 專用 SQL 集區暫停時，您會看到 [啟動] 按鈕。  若要繼續計算，請選取 [啟動]  。

3. 若要移除專用 SQL 集區，而不再支付計算或儲存體的費用，請選取 [刪除]。

## <a name="next-steps"></a>後續步驟

- 您現在已建立了工作負載分類器。 以 TheCEO 身分執行一些查詢來查看其運作。 請參閱 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 以檢視查詢和所指派的重要性。
- 如需專用 SQL 集區工作負載管理的詳細資訊，請參閱[工作負載重要性](sql-data-warehouse-workload-importance.md)和[工作負載分類](sql-data-warehouse-workload-classification.md)。
- 請參閱[設定工作負載的重要性](sql-data-warehouse-how-to-configure-workload-importance.md)的操作說明文章，以及如何[管理並監視工作負載管理](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)。
