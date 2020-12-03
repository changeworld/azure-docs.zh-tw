---
title: Azure Cosmos DB 全域散發
description: 了解如何在 Azure 入口網站中使用 Azure Cosmos DB 進行全域資料複寫
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: 58788d6194454c8bd40730c9c350aa901924ba3d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027910"
---
## <a name="add-global-database-regions-using-the-azure-portal"></a><a id="addregion"></a>使用 Azure 入口網站新增全球資料庫區域
全球所有 [Azure 區域][azureregions]都有提供 Azure Cosmos DB。 選取資料庫帳戶的預設一致性層級之後，您可以關聯一或多個區域 (取決於您對於預設一致性層級和全球發佈需求的選擇)。

1. 在 [Azure 入口網站](https://portal.azure.com/)的左列中，按一下 [Azure Cosmos DB]  。
2. 在 [Azure Cosmos DB]  頁面中，選取要修改的資料庫帳戶。
3. 在帳戶頁面中，從功能表中按一下 [全域複寫資料]  。
4. 在 [全域複寫資料]  頁面中，按一下地圖中的區域以選取要新增或移除的區域，然後按一下 [儲存]  。 新增區域需要費用，如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)或[使用 Azure Cosmos DB 全球散發資料](../articles/cosmos-db/distribute-data-globally.md)一文。
   
    ![按一下地圖中的區域以新增或移除它們][1]
    
在您新增第二個區域後，就會在入口網站中的 [全域複寫資料]  頁面上啟用 [手動容錯移轉]  選項。 您可以使用此選項來測試容錯移轉程序，或變更主要寫入區域。 在您新增第三個區域後，將會在相同的頁面上啟用 [容錯移轉優先順序]  選項，讓您能夠變更讀取的容錯移轉順序。  

### <a name="selecting-global-database-regions"></a>選取全球資料庫區域
設定兩個或更多區域有兩個常見案例︰

1. 為使用者提供低延遲的資料存取 (無論使用者位於世界何處)
2. 新增業務持續性和災害復原 (BCDR) 的區域性復原功能

若要為使用者提供低延遲，建議您在應用程式使用者所在位置的對應區域中部署應用程式和 Azure Cosmos DB。

對於 BCDR，建議根據[業務持續性和災害復原 (BCDR)：Azure 配對區域][bcdr]一文中所述的區域配對來新增區域。

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: ../articles/best-practices-availability-paired-regions.md
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/