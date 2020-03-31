---
title: 要求增加配額
description: 本頁介紹如何創建支援請求以增加 Azure SQL 資料庫單個資料庫、伺服器和託管實例的配額。
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586150"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Azure SQL 資料庫的請求配額增加

本文介紹如何請求為單個資料庫、伺服器和託管實例增加 Azure SQL 資料庫的配額。 它還說明了如何啟用對區域的訂閱訪問。

## <a name="create-a-new-support-request"></a><a id="newquota"></a>創建新的支援請求

使用以下步驟從 AZURE 門戶為 SQL 資料庫創建新的支援請求。

1. 在[Azure 門戶](https://portal.azure.com)功能表上，選擇 **"説明 + 支援**"。

   !["説明+支援"連結](./media/quota-increase-request/help-plus-support.png)

1. 在 **"説明+支援"** 中，選擇 **"新建支援請求**"。

    ![創建新的支援請求](./media/quota-increase-request/new-support-request.png)

1. 針對 [問題類型]****，選取 [服務與訂用帳戶限制 (配額)]****。

   ![選擇問題類型](./media/quota-increase-request/select-quota-issue-type.png)

1. 對於**訂閱**，選擇要增加其配額的訂閱。

   ![為增加的配額選擇訂閱](./media/quota-increase-request/select-subscription-support-request.png)

1. 對於**配額類型**，請選擇以下配額類型之一：

   - **用於**單個資料庫和彈性池配額的 SQL 資料庫。
   - 託管實例的**SQL 資料庫託管實例**。

   然後選擇 **"下一步：解決方案 >>。**

   ![選擇配額類型](./media/quota-increase-request/select-quota-type.png)

1. 在 **"詳細資訊"** 視窗中，選擇 **"提供詳細資訊**"以輸入其他資訊。

   !["提供詳細資訊"連結](./media/quota-increase-request/provide-details-link.png)

按一下 **"提供詳細資訊**"將顯示允許您添加其他資訊的**配額詳細資訊**視窗。 以下各節介紹**SQL 資料庫**和**SQL 資料庫託管實例**配額類型的不同選項。

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>SQL 資料庫配額類型

以下各節介紹**SQL 資料庫**配額類型的三個增加配額選項：

- 每個伺服器的資料庫事務單元 （DUS）
- 每個訂閱的伺服器
- 啟用對區域的訂閱訪問

### <a name="database-transaction-units-dtus-per-server"></a>每個伺服器的資料庫事務單元 （DUS）

使用以下步驟請求增加每個伺服器的 DTO。

1. 根據伺服器配額類型選擇**資料庫事務單位 （DTUs）。**

1. 在 **"資源"** 清單中，選擇要定位的資源。

1. 在 **"新建配額"** 欄位中，輸入要請求的新 DTU 限制。

   ![DTU 配額詳細資訊](./media/quota-increase-request/quota-details-dtus.png)

有關詳細資訊，請參閱使用[DTU 採購模型的單個資料庫的資源限制](sql-database-dtu-resource-limits-single-databases.md)以及[使用 DTU 採購模型的彈性池的資源限制](sql-database-dtu-resource-limits-elastic-pools.md)。

### <a name="servers-per-subscription"></a>每個訂閱的伺服器

使用以下步驟請求增加每個訂閱的伺服器數。

1. **選擇每個訂閱**配額類型的伺服器。

1. 在 **"位置**"清單中，選擇要使用的 Azure 區域。 配額是每個區域中每個訂閱的配額。

1. 在"**新建配額"** 欄位中，輸入對該區域中伺服器最大數量的請求。

   ![伺服器配額詳細資訊](./media/quota-increase-request/quota-details-servers.png)

有關詳細資訊，請參閱[SQL 資料庫資源限制和資源治理](sql-database-resource-limits-database-server.md)。

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>啟用對區域的訂閱訪問

某些產品/服務類型並非在每個區域都可用。 您可能會看到如下錯誤：

`This location is not available for subscription`

如果您的訂閱需要在特定區域進行訪問，請使用 **"其他配額請求**"選項請求訪問。 在請求中，指定要為該區域啟用的產品/服務以及 SKU 詳細資訊。 要流覽產品/服務選項和 SKU 選項，請參閱[Azure SQL 資料庫定價](https://azure.microsoft.com/pricing/details/sql-database/single/)。

![其他配額詳細資訊](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>託管實例配額類型

對於**SQL 伺服器託管實例**配額類型，請使用以下步驟：

1. 在 **"區域**"清單中，選擇要定位的 Azure 區域。

1. 輸入您請求的**子網**和**vCore**的新限制。

   ![託管實例配額詳細資訊](./media/quota-increase-request/quota-details-managed-instance.png)

有關詳細資訊，請參閱概述[Azure SQL 資料庫託管實例資源限制](sql-database-managed-instance-resource-limits.md)。

## <a name="submit-your-request"></a>提交您的要求

最後一步是填寫 SQL 資料庫配額請求的剩餘詳細資訊。 然後選擇 **"下一步：審閱 + 創建>>**"，在查看請求詳細資訊後，按一下"**創建"** 以提交請求。

## <a name="next-steps"></a>後續步驟

提交請求後，將對其進行審核。 我們將根據您在表單中提供的資訊與您聯繫並回答。

有關其他 Azure 限制的詳細資訊，請參閱[Azure 訂閱和服務限制、配額和約束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
