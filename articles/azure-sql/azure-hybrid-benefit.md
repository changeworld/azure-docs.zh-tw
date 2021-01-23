---
title: Azure Hybrid Benefit
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 針對 Azure SQL Database 和 SQL 受控執行個體折扣使用現有的 SQL Server 授權。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 11/13/2019
ms.openlocfilehash: 51cf4a4d3aa3cb4654b141a98064460a7152fcec
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737450"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure Hybrid Benefit-Azure SQL Database & SQL 受控執行個體
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

在以 vCore 為基礎的購買模型的已布建計算層中，您可以使用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，在 Azure SQL Database 和 Azure SQL 受控執行個體上以折扣費率交換現有授權。 此 Azure 權益可讓您使用 SQL Server 授權搭配軟體保證，在 SQL Database & SQL 受控執行個體上省下最多30% 或更高的版本。 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)頁面有一個計算機，可協助您判斷節省的成本。  請注意，Azure Hybrid Benefit 不適用於 Azure SQL Database 無伺服器。

> [!NOTE]
> 變更為 Azure Hybrid Benefit 不需要停機。

![vcore 定價結構](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>選擇授權模型

使用 Azure Hybrid Benefit 時，您可以選擇只支付基礎 Azure 基礎結構的費用，方法是使用您現有的 SQL Server 資料庫引擎 SQL Server 授權 (基礎計算定價) ，也可以支付基礎結構和 SQL Server 授權 (授權包含的定價) 。

您可以在 Azure 入口網站中選擇或變更您的授權模型： 
- 針對新的資料庫，在建立期間，選取 [**基本**] 索引標籤上的 [**設定資料庫**]，然後選取選項來節省成本。
- 針對現有的資料庫，請在 [**設定**] 功能表中選取 [**設定**]，然後選取選項來節省成本。

您也可以使用下列其中一個 Api 來設定新的或現有的資料庫：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 來設定或更新授權類型：

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
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


### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid Benefit 問題

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>適用於 SQL Server 的 Azure Hybrid Benefit 是否有雙重使用權？

對於此授權，您具有 180 天的雙重使用權，以確保移轉能順暢地執行。 在該180天期間之後，您只能在 SQL Database 的雲端中使用 SQL Server 授權。 您不再擁有內部部署和雲端的雙重使用權利。

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>適用於 SQL Server 的 Azure Hybrid Benefit 與授權行動性有何不同？

我們提供授權行動性權益，以軟體保證 SQL Server 客戶。 這可將其授權重新指派給夥伴的共用伺服器。 您可以在 Azure IaaS 和 AWS EC2 上使用這項權益。

適用於 SQL Server 的 Azure Hybrid Benefit 主要在以下兩方面與授權行動性有所差異：

- 它提供將高度虛擬化工作負載移轉至 Azure 的經濟效益。 SQL Server Enterprise Edition 客戶可以在 Azure 的一般目的 SKU 中，為其內部部署的每個核心（高度虛擬化應用程式）取得四個核心。 授權行動性不允許將虛擬化工作負載移至雲端的任何特殊成本優勢。
- 它可在 Azure (SQL 受控執行個體) 與 SQL Server 高度相容的 PaaS 目的地。

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>適用於 SQL Server 的 Azure Hybrid Benefit 有哪些明確的權利？

SQL Database 的客戶與 SQL Server 的 Azure Hybrid Benefit 有下列相關聯的許可權：

|授權使用量|SQL Server 的 Azure Hybrid Benefit 有何説明？|
|---|---|
|具有 SA 的 SQL Server Enterprise Edition 核心客戶|<li>可以根據超大規模、一般用途或業務關鍵 SKU 支付基本費率</li><br><li>1個內部部署核心 = 超大規模 SKU 中的4個核心</li><br><li>1 個內部部署核心 = 一般目的 SKU 中的 4 個核心</li><br><li>1 個內部部署核心 = 業務關鍵 SKU 中的 1 個核心</li>|
|具有 SA 的 SQL Server Standard Edition 核心客戶|<li>只能支付超大規模和一般用途 SKU 的基本費率</li><br><li>1個內部部署核心 = 超大規模 SKU 中的1個核心</li><br><li>1 個內部部署核心 = 一般目的 SKU 中的 1 個核心</li>|
|||


## <a name="next-steps"></a>後續步驟

- 如需選擇 Azure SQL 部署選項的協助，請參閱 [AZURE sql 中的選擇正確的部署選項](azure-sql-iaas-vs-paas-what-is-overview.md)。
- 如需 SQL Database 和 SQL 受控執行個體功能的比較，請參閱 [SQL Database & SQL 受控執行個體功能](database/features-comparison.md)。
