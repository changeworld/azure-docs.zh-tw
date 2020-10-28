---
title: 教學課程：入口網站中的異地複寫與容錯移轉
description: 使用 Azure 入口網站為資料庫設定異地複寫，並起始容錯移轉。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 71c73fec4f559b34b097556243617636acd77480
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673276"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>教學課程：在 Azure 入口網站中設定主動式異地複寫和容錯移轉 (Azure SQL Database)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文說明如何使用 [Azure 入口網站](https://portal.azure.com)[為 Azure SQL Database 設定主動式異地複寫](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities)，並起始容錯移轉。

如需使用自動容錯移轉群組的最佳做法，請參閱 [Azure SQL Database 的 最佳做法](auto-failover-group-overview.md#best-practices-for-sql-database)和 [Azure SQL 受控執行個體的最佳做法](auto-failover-group-overview.md#best-practices-for-sql-managed-instance)。 



## <a name="prerequisites"></a>必要條件

若要使用 Azure 入口網站來設定作用中異地複寫，您需要下列資源：

* Azure SQL Database 中的資料庫：您想要複寫到不同地理區域的主要資料庫。

> [!Note]
> 使用 Azure 入口網站時，您只能在與主要資料庫相同的訂用帳戶內建立次要資料庫。 如果次要資料庫必須位在不同的訂用帳戶中，請使用[建立資料庫 REST API](/rest/api/sql/databases/createorupdate) 或 [ALTER DATABASE Transact-SQL API](/sql/t-sql/statements/alter-database-transact-sql)。

## <a name="add-a-secondary-database"></a>新增次要資料庫

下列步驟會在異地複寫合作關係中建立新的次要資料庫。  

若要新增次要資料庫，您必須是訂用帳戶擁有者或共同擁有者。

次要資料庫的名稱會與主要資料庫相同，並且預設會具有相同的服務層級和計算大小。 次要資料庫可以是單一資料庫或集區資料庫。 如需詳細資訊，請參閱[以 DTU 為基礎的購買模型](service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](service-tiers-vcore.md)。
建立並植入次要複本之後，就會開始從主要資料庫將資料複寫到新的次要資料庫。

> [!NOTE]
> 如果夥伴資料庫已經存在 (例如，因終止先前的「異地複寫」關聯性所導致)，命令將會失敗。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您想要為「異地複寫」設定的資料庫。
2. 在 SQL Database 頁面上，選取 [異地複寫]，然後選取要建立次要資料庫的區域。 您可以選取裝載主要資料庫之區域以外的任何區域，但我們建議您選取[配對區域](../../best-practices-availability-paired-regions.md)。

    ![設定異地複寫](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. 選取或設定伺服器及次要資料庫的定價層。

    ![建立次要形式](./media/active-geo-replication-configure-portal/create-secondary.png)
4. (選擇性) 您可以將次要資料庫新增至彈性集區。 若要在集區中建立次要資料庫，請按一下 [彈性集區]  ，然後選取目標伺服器上的集區。 集區必須已存在目標伺服器上。 此工作流程不會建立集區。
5. 按一下 [建立]  以加入次要資料庫。
6. 將會建立次要資料庫並開始植入程序。

    ![次要地圖](./media/active-geo-replication-configure-portal/seeding0.png)
7. 當植入程序完成時，次要資料庫會顯示其狀態。

    ![植入完成](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>起始容錯移轉

次要資料庫可被切換成為主要資料庫。  

1. 在 [Azure 入口網站](https://portal.azure.com) 中，瀏覽至「異地複寫」合作關係中的主要資料庫。
2. 在 [SQL Database] 刀鋒視窗上，選取 [所有設定] > [異地複寫]。
3. 在 [次要] 清單中，選取要作為新主要資料庫的資料庫，然後按一下 [強制容錯移轉]。

    ![failover](./media/active-geo-replication-configure-portal/secondaries.png)
4. 按一下 [是]  即可開始容錯移轉。

命令會立即將次要資料庫切換為主要角色。 此程序通常會在 30 秒 (或更少的時間) 內完成。

切換角色時，會有一小段時間無法使用這兩個資料庫 (大約為 0 到 25 秒)。 如果主要資料庫有多個次要資料庫，此命令會自動重新設定其他次要複本以連接至新的主要複本。 在正常情況下，完成整個作業所需的時間應該少於一分鐘。

> [!NOTE]
> 此命令的設計目的是要快速復原發生中斷的資料庫。 它會觸發容錯移轉但不進行資料同步 (強制性容錯移轉)。  發出命令時，如果主要複本處於線上並且正在認可交易，可能會發生部分資料遺失。

## <a name="remove-secondary-database"></a>移除次要資料庫

此作業會永久終止對次要資料庫的複寫，並將次要資料庫的角色變更為一般讀寫資料庫。 如果與次要資料庫的連線中斷，命令將會成功，但次要資料庫必須等到連線恢復後才會變成讀寫資料庫。  

1. 在 [Azure 入口網站](https://portal.azure.com) 中，瀏覽至「異地複寫」合作關係中的主要資料庫。
2. 在 SQL Database 頁面上，選取 [異地複寫]。
3. 在 [次要] 清單中，選取您想要從「異地複寫」合作關係中移除的資料庫。
4. 按一下 [ **停止複寫** ]。

    ![移除次要](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. 隨即開啟確認視窗。 按一下 [是]  以從異地複寫合作關係中移除資料庫。 (將它設定為讀寫資料庫不屬於任何複寫的一部分。)

## <a name="next-steps"></a>後續步驟

* 若要深入了解作用中異地複寫，請參閱[作用中異地複寫](active-geo-replication-overview.md)。
* 若要深入了解自動容錯移轉群組，請參閱[自動容錯移轉群組](auto-failover-group-overview.md)
* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](business-continuity-high-availability-disaster-recover-hadr-overview.md)。