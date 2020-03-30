---
title: 教程：門戶中的異地複製&容錯移轉
description: 使用 Azure 門戶為 Azure SQL 資料庫中的單個或池資料庫配置異地複製，並啟動容錯移轉。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 601c537a51e29ae1f107127e1b83c07448eee9ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256428"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>在 Azure 入口網站中為 Azure SQL Database 設定主動式異地複寫，並起始容錯移轉

本文說明您如何使用 [Azure 入口網站](https://portal.azure.com)在 Azure SQL Database 中為[獨立和集區資料庫設定主動式異地複寫](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities)，並起始容錯移轉。

如需單一和集區資料庫的自動容錯移轉群組的相關資訊，請參閱[使用容錯移轉群組搭配單一和集區資料庫的最佳作法](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)。 有關具有託管實例的自動容錯移轉組的資訊，請參閱[使用具有託管實例的容錯移轉組的最佳做法](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances)。

## <a name="prerequisites"></a>Prerequisites

若要使用 Azure 入口網站來設定作用中異地複寫，您需要下列資源：

* Azure SQL 資料庫：您想要複寫到不同地理區域的主要資料庫。

> [!Note]
> 使用 Azure 入口網站時，您只能在與主要資料庫相同的訂用帳戶內建立次要資料庫。 如果次要資料庫必須在不同的訂用帳戶中，請使用 [Create Database REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) 或 [ALTER DATABASE Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)。

## <a name="add-a-secondary-database"></a>新增次要資料庫

下列步驟會在異地複寫合作關係中建立新的次要資料庫。  

若要新增次要資料庫，您必須是訂用帳戶擁有者或共同擁有者。

次要資料庫的名稱會與主要資料庫相同，並且預設會具有相同的服務層級和計算大小。 次要資料庫可以是單一資料庫或集區資料庫。 如需詳細資訊，請參閱[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)。
建立並植入次要複本之後，就會開始從主要資料庫將資料複寫到新的次要資料庫。

> [!NOTE]
> 如果夥伴資料庫已經存在 (例如，因終止先前的「異地複寫」關聯性所導致)，命令將會失敗。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您想要為「異地複寫」設定的資料庫。
2. 在 SQL Database 頁面上，選取 [異地複寫]****，然後選取要建立次要資料庫的區域。 您可以選取裝載主要資料庫之區域以外的任何區域，但我們建議您選取[配對區域](../best-practices-availability-paired-regions.md)。

    ![設定異地複寫](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. 選取或設定伺服器及次要資料庫的定價層。

    ![設定次要資料庫](./media/sql-database-geo-replication-portal/create-secondary.png)
4. (選擇性) 您可以將次要資料庫新增至彈性集區。 若要在集區中建立次要資料庫，請按一下 [彈性集區]****，然後選取目標伺服器上的集區。 集區必須已存在目標伺服器上。 此工作流程不會建立集區。
5. 按一下 [建立] **** 以加入次要資料庫。
6. 將會建立次要資料庫並開始植入程序。

    ![設定次要資料庫](./media/sql-database-geo-replication-portal/seeding0.png)
7. 當植入程序完成時，次要資料庫會顯示其狀態。

    ![植入完成](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>起始容錯移轉

次要資料庫可被切換成為主要資料庫。  

1. 在 [Azure 入口網站](https://portal.azure.com) 中，瀏覽至「異地複寫」合作關係中的主要資料庫。
2. 在 SQL 資料庫邊欄選項卡上，選擇 **"所有設置** > **"進行異地複製**。
3. 在 [次要]**** 清單中，選取要做為新主要資料庫的資料庫，然後按一下 [容錯移轉]****。

    ![failover](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. 按一下 [是] **** 即可開始容錯移轉。

命令會立即將次要資料庫切換為主要角色。 此過程通常在 30 秒或更短時間內完成。

切換角色時，會有一小段時間無法使用這兩個資料庫 (大約為 0 到 25 秒)。 如果主要資料庫有多個次要資料庫，此命令會自動重新設定其他次要複本以連接至新的主要複本。 在正常情況下，完成整個作業所需的時間應該少於一分鐘。

> [!NOTE]
> 此命令的設計目的是要快速復原發生中斷的資料庫。 它會觸發容錯移轉但不進行資料同步 (強制性容錯移轉)。  發出命令時，如果主要複本處於線上並且正在認可交易，可能會發生部分資料遺失。

## <a name="remove-secondary-database"></a>移除次要資料庫

此作業會永久終止對次要資料庫的複寫，並將次要資料庫的角色變更為一般讀寫資料庫。 如果與次要資料庫的連線中斷，命令將會成功，但次要資料庫必須等到連線恢復後才會變成讀寫資料庫。  

1. 在 [Azure 入口網站](https://portal.azure.com) 中，瀏覽至「異地複寫」合作關係中的主要資料庫。
2. 在 SQL 資料庫頁上，選擇**異地複製**。
3. 在 **"秒法"** 清單中，選擇要從異地複製夥伴關係中刪除的資料庫。
4. 按一下 [ **停止複寫**]。

    ![移除次要](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. 隨即開啟確認視窗。 按一下 [是]**** 以從異地複寫合作關係中移除資料庫。 (將它設定為讀寫資料庫不屬於任何複寫的一部分。)

## <a name="next-steps"></a>後續步驟

* 要瞭解有關活動異地複製的更多詳細資訊，請參閱[活動異地複製](sql-database-active-geo-replication.md)。
* 要瞭解自動容錯移轉組，請參閱[自動容錯移轉組](sql-database-auto-failover-group.md)
* 有關業務連續性概述和方案，請參閱[業務連續性概述](sql-database-business-continuity.md)。
