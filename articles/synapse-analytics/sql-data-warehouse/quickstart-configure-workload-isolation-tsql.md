---
title: 快速入門：設定工作負載隔離 - T-SQL
description: 使用 T-SQL 設定工作負載隔離。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 29f380c468eef27fa7bc00930b5005c8d0b0bf16
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80124456"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>快速入門：使用 T-SQL 設定工作負載隔離

在本快速入門中，您將快速建立一個工作負載群組和分類器，以保留載入資料的資源。 工作負載群組會將 20% 的系統資源配置給資料載入。  工作負載分類器會將要求指派給資料載入工作負載群組。  透過 20% 的資料載入隔離，保證資源能夠達到 SLA。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

> [!NOTE]
> 在 Azure Synapse Analytics 中建立 SQL 分析執行個體，可能會產生新的可計費服務。  如需詳細資訊，請參閱 [Azure Synapse Analytics 定價](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。
>
>

## <a name="prerequisites"></a>Prerequisites
 
本快速入門假設您已有 Azure Synapse 中的 SQL 分析執行個體，且已有 CONTROL DATABASE 權限。 若您需要建立 SQL 資料倉儲，請使用[建立與連線 - 入口網站](create-data-warehouse-portal.md)來建立稱為 **mySampleDataWarehouse** 的資料倉儲。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-login-for-dataloads"></a>建立 DataLoads 的登入

使用 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) 為 'ELTLogin' 在 `master` 資料庫中建立 SQL Server 驗證登入。

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>建立使用者

在 mySampleDataWarehouse 中[建立使用者](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest)「ELTLogin」

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>建立工作負載群組
針對具有 20% 隔離的 DataLoads 建立[工作負載群組](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)。
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>建立工作負載分類器

建立[工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)，將 ELTLogin 對應至 DataLoads 工作負載群組。

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>檢視現有的工作負載群組和分類器以及執行階段值

```sql
--Workload groups
SELECT * FROM 
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM 
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM 
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>清除資源

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

您需對資料倉儲單位和資料倉儲中儲存的資料付費。 這些計算和儲存體資源會分開計費。

- 如果您需要將資料保留在儲存體中，可以在您不使用 SQL 集區時暫停計算。 暫停計算，您只需支付資料儲存體的費用。 當您準備好使用資料時，會繼續計算。
- 如果您需要移除未來的費用，可以將資料倉儲刪除。

遵循下列步驟清除資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)，選取您的資料倉儲。

    ![清除資源](./media/quickstart-configure-workload-isolation-tsql/clean-up-resources.png)

2. 若要暫停計算，請選取 [暫停]  按鈕。 資料倉儲暫停時，您會看到 [啟動]  按鈕。  若要繼續計算，請選取 [啟動]  。

3. 若要移除資料倉儲，而不再支付計算或儲存體的費用，請選取 [刪除]  。

4. 若要移除您所建立的 SQL Server，請選取先前映像中的 [mynewserver 20180430.database.windows.net]  ，然後選取 [刪除]  。  請謹慎使用刪除，因為刪除伺服器也會刪除所有指派給伺服器的資料庫。

5. 若要移除此資源群組，請選取 [myResourceGroup]  ，然後選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

- 您現在已經建立了工作負載群組。 以 ELTLogin 身分執行一些查詢來查看其運作。 請參閱 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) 以檢視查詢和所指派的工作負載群組。
- 如需關於 SQL 分析工作負載管理的詳細資訊，請參閱[工作負載管理](sql-data-warehouse-workload-management.md)和[工作負載隔離](sql-data-warehouse-workload-isolation.md)。
