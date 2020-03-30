---
title: Azure Hybrid Benefit
description: 使用現有的 SQL Server 許可證進行 SQL 資料庫折扣。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945613"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

在基於 vCore 的採購模型的預配計算層中，可以使用[SQL Server 的 Azure 混合權益](https://azure.microsoft.com/pricing/hybrid-benefit/)將現有許可證兌換為 SQL 資料庫的折扣費率。 此 Azure 權益允許您通過使用具有軟體保證的本地 SQL Server 許可證在 Azure SQL 資料庫上節省高達 30% 甚至更高。 請使用前面提到的連結使用 Azure 混合福利計算機，瞭解正確的值。 

> [!NOTE]
> 更改為 Azure 混合權益不需要任何停機時間。

![定價](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>選擇許可證模型

使用 Azure 混合權益，您可以選擇僅使用 SQL 資料庫引擎本身的現有 SQL Server 許可證（基本計算定價）為基礎 Azure 基礎結構付費，也可以同時為基礎基礎結構和 SQL Server 付費許可證（許可證包含定價）。

您可以使用 Azure 門戶或使用以下 API 之一來選擇或更改許可模型：

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

要使用 PowerShell 設置或更新許可證類型：：

- [新-AzSql 資料庫](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 設置或更新許可證類型，請執行以下操作：

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

要使用 REST API 設置或更新許可證類型，請：

- [資料庫 - 建立或更新](/rest/api/sql/databases/createorupdate)
- [資料庫 - 更新](/rest/api/sql/databases/update)
- [Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>後續步驟

- 有關在 SQL 資料庫部署選項之間進行選擇，請參閱[在 Azure SQL 中選擇正確的部署選項](sql-database-paas-vs-sql-server-iaas.md)。
- 有關 SQL 資料庫功能的比較，請參閱[Azure SQL 資料庫功能](sql-database-features.md)。
