---
title: 要求增加配額
description: 此頁面說明如何建立支援要求，以增加 Azure SQL Database 單一資料庫、伺服器和受控實例的配額。
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77586150"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Azure SQL Database 的要求配額增加

本文說明如何針對單一資料庫、伺服器和受控實例的 Azure SQL Database，要求增加配額。 它也會說明如何啟用區域的訂用帳戶存取。

## <a name="create-a-new-support-request"></a><a id="newquota"></a>建立新的支援要求

使用下列步驟，從 SQL Database 的 Azure 入口網站建立新的支援要求。

1. 在 [ [Azure 入口網站](https://portal.azure.com)] 功能表上，選取 [說明 **+ 支援**]。

   ![[說明 + 支援] 連結](./media/quota-increase-request/help-plus-support.png)

1. 在 [說明 **+ 支援**] 中，選取 [**新增支援要求**]。

    ![建立新的支援要求](./media/quota-increase-request/new-support-request.png)

1. 針對 [問題類型]****，選取 [服務與訂用帳戶限制 (配額)]****。

   ![選取問題類型](./media/quota-increase-request/select-quota-issue-type.png)

1. 針對 [**訂**用帳戶]，選取您想要增加其配額的訂用帳戶。

   ![選取訂用帳戶以增加配額](./media/quota-increase-request/select-subscription-support-request.png)

1. 針對 [**配額類型**]，選取下列其中一種配額類型：

   - 適用于單一資料庫和彈性集區配額的**SQL Database** 。
   - 適用于受控實例的**SQL Database 受控執行個體**。

   然後選取 **[下一步：方案 >>] **。

   ![選取配額類型](./media/quota-increase-request/select-quota-type.png)

1. 在**詳細資料**視窗中，選取 [**提供詳細資料**] 以輸入其他資訊。

   ![[提供詳細資料] 連結](./media/quota-increase-request/provide-details-link.png)

按一下 [**提供詳細資料**] 會顯示 [**配額詳細資料**] 視窗，可讓您新增額外的資訊。 下列各節說明**SQL Database**和**SQL Database 受控執行個體**配額類型的不同選項。

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>SQL Database 配額類型

下列各節說明**SQL Database**配額類型的三個配額增加選項：

- 每一伺服器的資料庫交易單位（Dtu）
- 每個訂用帳戶的伺服器
- 啟用區域的訂用帳戶存取

### <a name="database-transaction-units-dtus-per-server"></a>每一伺服器的資料庫交易單位（Dtu）

使用下列步驟來要求增加每部伺服器的 Dtu 數。

1. 選取 [**每個伺服器的資料庫交易單位（dtu）** ] 配額類型。

1. 在**資源清單**中，選取要設為目標的資源。

1. 在 [**新增配額**] 欄位中，輸入您所要求的新 DTU 限制。

   ![DTU 配額詳細資料](./media/quota-increase-request/quota-details-dtus.png)

如需詳細資訊，請參閱使用 dtu 購買[模型的單一資料庫的資源限制](sql-database-dtu-resource-limits-single-databases.md)和[使用 dtu 購買模型的彈性集區資源限制](sql-database-dtu-resource-limits-elastic-pools.md)。

### <a name="servers-per-subscription"></a>每個訂用帳戶的伺服器

使用下列步驟來要求增加每個訂用帳戶的伺服器數目。

1. 選取 [**每個訂**用帳戶的伺服器] 配額類型。

1. 在 [**位置**] 清單中，選取要使用的 Azure 區域。 配額是每個區域中的每個訂用帳戶。

1. 在 [**新配額**] 欄位中，輸入您的要求，以取得該區域中的伺服器數目上限。

   ![伺服器配額詳細資料](./media/quota-increase-request/quota-details-servers.png)

如需詳細資訊，請參閱[SQL Database 資源限制和資源管理](sql-database-resource-limits-database-server.md)。

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>啟用區域的訂用帳戶存取

某些供應專案類型無法在每個區域中使用。 您可能會看到如下的錯誤：

`This location is not available for subscription`

如果您的訂用帳戶需要特定區域的存取權，請使用 [**其他配額要求**] 選項來要求存取權。 在您的要求中，指定您想要為區域啟用的供應專案和 SKU 詳細資料。 若要探索供應專案和 SKU 選項，請參閱[Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/single/)。

![其他配額詳細資料](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>受控實例配額類型

針對**SQL Server 受控執行個體**配額類型，請使用下列步驟：

1. 在 [**區域**] 清單中，選取要設為目標的 Azure 區域。

1. 輸入您為**子網**和**vCore**要求的新限制。

   ![受控實例配額詳細資料](./media/quota-increase-request/quota-details-managed-instance.png)

如需詳細資訊，請參閱[Azure SQL Database 受控實例資源限制的總覽](sql-database-managed-instance-resource-limits.md)。

## <a name="submit-your-request"></a>提交您的要求

最後一個步驟是填入 SQL Database 配額要求的剩餘詳細資料。 然後選取 **[下一步：審查 + 建立>>] **，然後在查看要求詳細資料之後，按一下 [**建立**] 以提交要求。

## <a name="next-steps"></a>後續步驟

提交要求之後，就會進行審核。 系統會根據您在表單中提供的資訊，與您的答案聯繫。

如需其他 Azure 限制的詳細資訊，請參閱[azure 訂用帳戶和服務限制、配額和條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
