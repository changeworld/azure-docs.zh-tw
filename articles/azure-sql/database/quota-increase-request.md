---
title: 要求增加配額
description: 此頁面說明如何建立支援要求，以增加 Azure SQL Database 和 Azure SQL 受控執行個體的配額。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 06/04/2020
ms.openlocfilehash: cfcdd143a26d36ed3c4646122fce7c19c41976d0
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448835"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database 和 SQL 受控執行個體的要求配額增加
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文說明如何要求 Azure SQL Database 和 Azure SQL 受控執行個體的配額增加。 此外，也會說明如何啟用區域的訂用帳戶存取。

## <a name="create-a-new-support-request"></a><a id="newquota"></a> 建立新的支援要求

使用下列步驟，從 SQL Database 的 Azure 入口網站建立新的支援要求。

1. 在 [Azure 入口網站](https://portal.azure.com)功能表上，選取 [説明 + 支援]。

   ![[説明 + 支援] 連結](./media/quota-increase-request/help-plus-support.png)

1. 在 [說明 + 支援] 中，選取 [新增支援要求]。

    ![建立新的支援要求](./media/quota-increase-request/new-support-request.png)

1. 針對 [ **問題類型**]，選取 [ **服務與訂用帳戶限制] (配額) **。

   ![選取問題類型](./media/quota-increase-request/select-quota-issue-type.png)

1. 針對 [ **訂**用帳戶]，選取您要增加其配額的訂用帳戶。

   ![選取提高配額的訂用帳戶](./media/quota-increase-request/select-subscription-support-request.png)

1. 針對 [ **配額類型**]，選取下列其中一個配額類型：

   - 適用于單一資料庫和彈性集區配額的**SQL Database** 。
   - 受控實例的**SQL Database 受控執行個體**。

   然後，選取 **[下一步：解決方案 >>]** 。

   ![選取配額類型](./media/quota-increase-request/select-quota-type.png)

1. 在 [ **詳細資料** ] 視窗中，選取 [ **輸入詳細資料** ] 以輸入其他資訊。

   ![輸入詳細資料連結](./media/quota-increase-request/provide-details-link.png)

按一下 [ **輸入詳細資料** ] 會顯示 [ **配額詳細資料** ] 視窗，可讓您新增其他資訊。 下列各節說明 **SQL Database** 和 **SQL Database 受控執行個體** 配額類型的不同選項。

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> SQL Database 配額類型

下列各節說明 **SQL Database** 配額類型的配額增加選項：

- 每一伺服器的資料庫交易單位 (Dtu) 
- 每個訂用帳戶的伺服器
- M 系欄區域存取
- 區域存取

### <a name="database-transaction-units-dtus-per-server"></a>每一伺服器的資料庫交易單位 (Dtu) 

使用下列步驟來要求增加每部伺服器的 Dtu 數。

1. 選取 **每個伺服器配額類型 (dtu) 的資料庫交易單位** 。

1. 在 [資源] 清單中，選取要設為目標的資源。

1. 在 [ **新配額** ] 欄位中，輸入您要要求的新 DTU 限制。

   ![DTU 配額詳細資料](./media/quota-increase-request/quota-details-dtus.png)

如需詳細資訊，請參閱使用 dtu 購買模型的 [單一資料庫的資源限制](resource-limits-dtu-single-databases.md) （使用 dtu 購買模型的彈性集區的 [資源](resource-limits-dtu-elastic-pools.md)限制）。

### <a name="servers-per-subscription"></a>每個訂用帳戶的伺服器

使用下列步驟來要求每個訂用帳戶增加伺服器數目。

1. 選取**每個訂用帳戶的伺服器**配額類型。

1. 在 [位置] 清單中，選取要使用的 Azure 區域。 配額是各個區域中的每個訂用帳戶。

1. 在 [新配額] 欄位中，輸入您對於該區域中伺服器數目上限的要求。

   ![伺服器配額詳細資料](./media/quota-increase-request/quota-details-servers.png)

如需詳細資訊，請參閱 [SQL Database 資源限制和資源管理](resource-limits-logical-server.md)。

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a> 啟用區域的訂用帳戶存取權

某些供應項目類型無法在每個區域中使用。 您可能會看到如下的錯誤：

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

如果您的訂用帳戶需要特定區域的存取權，請選取 [ **區域存取** ] 選項。 在您的要求中，指定您想要為區域啟用的供應項目和 SKU 詳細資料。 若要探索供應專案和 SKU 選項，請參閱 [Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/single/)。

1. 選取 [ **區域存取** 配額] 類型。

1. 在 [ **選取位置** ] 清單中，選取要使用的 Azure 區域。 配額是各個區域中的每個訂用帳戶。

1. 輸入 **購買模型**和預期的 **耗用量** 詳細資料。

   ![要求區域存取](./media/quota-increase-request/quota-request.png)

### <a name="enable-m-series-access-to-a-region"></a><a id="mseries"></a> 啟用區域的 M 系列存取

若要啟用訂用帳戶和區域的 M 系列硬體，必須開啟支援要求。

1. 選取 **M 系欄區域存取** 配額類型。

1. 在 [ **選取位置** ] 清單中，選取要使用的 Azure 區域。 配額是各個區域中的每個訂用帳戶。


   ![要求 M 系欄區域存取](./media/quota-increase-request/quota-m-series.png)

## <a name="sql-managed-instance-quota-type"></a><a id="sqlmiquota"></a> SQL 受控執行個體配額類型

針對 **SQL 受控執行個體** 配額類型，請使用下列步驟：

1. 在 [ **區域** ] 清單中，選取要設為目標的 Azure 區域。

1. 輸入您針對 **子網** 和 **vCore**要求的新限制。

   ![SQL 受控執行個體配額詳細資料](./media/quota-increase-request/quota-details-managed-instance.png)

如需詳細資訊，請參閱 [總覽 AZURE SQL 受控執行個體資源限制](../managed-instance/resource-limits.md)。

## <a name="submit-your-request"></a>提交您的要求

最後一個步驟是填入 SQL Database 配額要求的剩餘詳細資料。 然後，選取 **[下一步：** 檢閱+建立>>]，然後查看要求詳細資料後，按一下 [建立]  以提交要求。

## <a name="next-steps"></a>後續步驟

提交要求之後，我們會加以審核。 系統會根據您在表單中提供的資訊與您連絡。

如需其他 Azure 限制的詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
