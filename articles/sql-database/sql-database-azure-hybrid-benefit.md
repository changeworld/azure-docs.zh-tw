---
title: Azure Hybrid Benefit
description: 使用現有的 SQL Server 授權來 SQL Database 折扣。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945613"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

在 vCore 為基礎的購買模型的已布建計算層中，您可以使用[SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，在 SQL Database 上交換您現有的授權以享有折扣優惠。 這項 Azure 權益可讓您使用內部部署 SQL Server 授權搭配軟體保證，最多省下30% 或更高的 Azure SQL Database。 請使用先前所述的連結，以取得正確的值，以使用 Azure Hybrid Benefit 的計算機。 

> [!NOTE]
> 變更為 Azure Hybrid Benefit 不需要停機。

![定價](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>選擇授權模型

使用 Azure Hybrid Benefit，您可以選擇只使用現有的 SQL database engine SQL Server 授權（基礎計算定價）來支付基礎 Azure 基礎結構的費用，也可以支付基礎結構和 SQL Server 授權（包含授權的定價）。

您可以使用 Azure 入口網站或使用下列其中一個 Api，來選擇或變更您的授權模型：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 來設定或更新授權類型：

- [新增-Set-azsqldatabase 搭配](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 來設定或更新授權類型：

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

若要使用 REST API 來設定或更新授權類型：

- [資料庫 - 建立或更新](/rest/api/sql/databases/createorupdate)
- [資料庫 - 更新](/rest/api/sql/databases/update)
- [Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>後續步驟

- 如需在 SQL Database 部署選項之間進行選擇，請參閱[在 AZURE SQL 中選擇正確的部署選項](sql-database-paas-vs-sql-server-iaas.md)。
- 如需 SQL Database 功能的比較，請參閱[Azure SQL Database 功能](sql-database-features.md)。
