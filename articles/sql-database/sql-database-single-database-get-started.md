---
title: 建立單一資料庫
description: 使用 Azure 入口網站、PowerShell 和 Azure CLI，在 Azure SQL Database 中建立和查詢單一資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 02/14/2020
ms.openlocfilehash: 2dacdfaa5443707ab82ae53922ac439319375276
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252101"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>快速入門：使用 Azure 入口網站、PowerShell 和 Azure CLI，在 Azure SQL Database 中建立單一資料庫

要在 Azure SQL Database 中建立資料庫，建立[單一資料庫](sql-database-single-database.md)是最快速且最簡單的部署選項。 本快速入門說明如何使用 Azure 入口網站建立並查詢單一資料庫。

如果您沒有 Azure 訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/)。 

若要進行本快速入門中的所有步驟，請登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-single-database"></a>建立單一資料庫

您可以在已佈建或無伺服器的計算層中建立單一資料庫。

- 已佈建計算層中的單一資料庫會使用其中一種[購買模型](sql-database-purchase-models.md)，預先配置固定數量的計算資源，包括 CPU 和記憶體。
- 無伺服器計算層中的單一資料庫具有 CPU 和記憶體等多種可自動調整的計算資源，且僅適用於[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)。

當您建立單一資料庫時，您也會定義 [SQL Database 伺服器](sql-database-servers.md)加以管理，並將其放入指定區域中的 [Azure 資源群組](../azure-resource-manager/management/overview.md)內。

> [!NOTE]
> 本快速入門使用[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)，但[以 DTU 為基礎的購買模型](sql-database-service-tiers-DTU.md)也可供使用。

若要建立包含 AdventureWorksLT 範例資料的單一資料庫：

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>查詢資料庫

您現在已建立資料庫，接著請使用 Azure 入口網站內建的查詢工具連線到資料庫，並查詢資料。

1. 在資料庫的 [SQL Database]  頁面上，選取左側功能表中的 [查詢編輯器 (預覽)]  。

   ![登入查詢編輯器](./media/sql-database-get-started-portal/query-editor-login.png)

2. 輸入您的登入資訊，並選取 [確定]  。
3. 在 [查詢編輯器]  窗格中輸入下列查詢。

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. 選取 [執行]  ，然後在 [結果]  窗格中檢閱查詢結果。

   ![查詢編輯器結果](./media/sql-database-get-started-portal/query-editor-results.png)

5. 關閉 [查詢編輯器]  頁面，並在系統提示是否要捨棄未儲存的編輯時選取 [確定]  。

## <a name="clean-up-resources"></a>清除資源

如果您想要繼續進行[後續步驟](#next-steps)，請保留此資源群組、資料庫伺服器和單一資料庫。 後續步驟會示範如何使用不同的方法連接及查詢您的資料庫。

這些資源使用完畢後，您可以依照下列方式加以刪除：

1. 從 Azure 入口網站的左側功能表中，依序選取 [資源群組]  和 [myResourceGroup]  。
2. 在資源群組頁面上，選取 [刪除資源群組]  。
3. 在欄位中輸入 myResourceGroup  ，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

- 建立伺服器層級的防火牆規則，以從內部部署或遠端工具連線到您的單一資料庫。 如需詳細資訊，請參閱[建立伺服器層級防火牆規則](sql-database-server-level-firewall-rule.md)。
- 在建立伺服器層級的防火牆規則後，使用數種不同的工具或語言來[連線及查詢](sql-database-connect-query.md)您的資料庫。
  - [使用 SQL Server Management Studio 進行連線和查詢](sql-database-connect-query-ssms.md)
  - [使用 Azure Data Studio 進行連線及查詢](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- 若要使用 Azure CLI 在已佈建的計算層中建立單一資料庫，請參閱 [Azure CLI 範例](sql-database-cli-samples.md)。
- 若要使用 Azure PowerShell 在已佈建的計算層中建立單一資料庫，請參閱 [Azure PowerShell 範例](sql-database-powershell-samples.md)。
- 若要使用 Azure PowerShell 在無伺服器計算層中建立單一資料庫，請參閱[建立無伺服器資料庫](sql-database-serverless.md#create-new-database-in-serverless-compute-tier)。
