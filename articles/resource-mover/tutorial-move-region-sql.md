---
title: 使用 Azure Resource Mover 在區域之間移動 Azure SQL 資源
description: 了解如何使用 Azure Resource Mover 將 Azure SQL 資源移至另一個區域
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 0718151039d88ffb76a07ce082c08fb011dab88b
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652280"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>教學課程：將 Azure SQL Database 資源移到另一個區域

在本教學課程中，您將了解如何使用 [Azure Resource Mover](overview.md)，將 Azure SQL 資料庫和彈性集區移至不同的 Azure 區域。

> [!NOTE]
> Azure Resource Mover 目前處於預覽狀態。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢查必要條件和需求。
> * 選取您要移動的資源。
> * 解析資源相依性。
> * 準備 SQL Server，並將其移至目標區域。
> * 準備並移動資料庫和彈性集區。
> * 決定您是否要捨棄或認可移動。 
> * 移動後，選擇性地移除來源區域中的資源。 

> [!NOTE]
> 教學課程顯示嘗試案例的最快路徑，並使用預設選項。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。 然後登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="prerequisites"></a>必要條件

-  檢查您在包含要移動之資源的訂用帳戶上，有「擁有者」存取權。
    - 第一次在 Azure 訂用帳戶中新增特定來源和目的地組的資源時，Resource Mover 會建立訂用帳戶所信任的[系統指派受控識別](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (之前稱為「受控服務識別 (MSI)」)。
    - 若要建立身分識別，並為其指派必要的角色 (來源訂用帳戶中的參與者或使用者存取管理員)，您用來新增資源的帳戶需要訂用帳戶「擁有者」權限。 [深入了解](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) Azure 角色。
- 訂用帳戶需要足夠的配額，才能建立您要在目標區域中移動的資源。 如果沒有配額，請[要求額外的限制](/azure/azure-resource-manager/management/azure-subscription-service-limits)。
- 確認與您要移動資源的目標區域相關聯的定價和費用。 請使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)協助確認。
    

## <a name="check-sql-requirements"></a>檢查 SQL 需求

1. [檢查](support-matrix-move-region-sql.md)哪些資料庫/彈性集區功能受支援可移至另一個區域。
2. 在目標區域中，為每個來源伺服器建立目標伺服器。 [深入了解](/azure/azure-sql/database/active-geo-replication-security-configure#how-to-configure-logins-and-users)。
4. 如果資料庫使用透明資料加密 (TDE) 進行加密，而且您在 Azure Key Vault 中使用自己的加密金鑰，請[了解如何](../key-vault/general/move-region.md)將金鑰保存庫移至另一個區域。
5. 如果已啟用 SQL 資料同步，則系統支援移動成員資料庫的作業。 移動後，您必須將 SQL 資料設定為同步到新的目標資料庫。
6. 移動之前，請先移除進階資料安全性設定。 移動後，在目標區域的 SQL Server 層級中[設定](/azure/sql-database/sql-database-advanced-data-security)。
7. 如果已啟用稽核，原則會在移動之後重設為預設值。 移動後，再次[設定稽核](/azure/sql-database/sql-database-auditing)。
7. 來源資料庫的備份保留原則會一併傳送到目標資料庫。 [深入了解](/azure/sql-database/sql-database-long-term-backup-retention-configure )在移動後修改設定。
8. 在移動之前移除伺服器層級防火牆規則。 在移動期間，系統會將資料庫層級的防火牆規則從來源伺服器複製到目標伺服器。 移動後，為目標區域中的 SQL Server [設定防火牆規則](/azure/sql-database/sql-database-server-level-firewall-rule) 。
9. 移動之前，請先移除自動調整設定。 移動之後，[再次設定自動調整](/azure/sql-database/sql-database-automatic-tuning-enable)。
10. 移動之前，請先移除資料庫警示設定。 移動後[重設](/azure/sql-database/sql-database-insights-alerts-portal)。
    
## <a name="select-resources"></a>選取資源

選取您要移動的資源。

- 您可以在選取的來源區域中的任何資源群組中，選取任何支援的資源類型。
- 您可以將資源移至與來源區域位於相同訂用帳戶中的目標區域。 如果要變更訂用帳戶，可以在移動資源後執行此動作。

1. 在 Azure 入口網站中，搜尋並選取 [資源移動器]。 然後，在**服務**底下，選取 [Azure Resource Mover]。

     ![Azure 入口網站中資源移動器的搜尋結果](./media/tutorial-move-region-sql/search.png)

2. 在**概觀**中，按一下 [開始使用]。

    ![新增要移至另一個區域之資源的按鈕](./media/tutorial-move-region-sql/get-started.png)

3. 在**移動資源** > **來源 + 目的地**中，選取來源訂用帳戶和區域。
4. 在**目的地**中，選取要將資源移至哪一個區域。 然後按一下 [下一步]  。
5. 在**中繼資料區域**中，選取您要在其中儲存所要移動之資源的相關中繼資料。 系統會特別針對此用途建立資源群組。 然後按一下 [下一步]  。

    ![選取來源和目標區域的頁面](./media/tutorial-move-region-sql/source-target.png)

6. 在**要移動的資源**中，按一下 [選取資源]。
7. 在**選取資源**中，選取資源。 您只能新增支援移動的資源。 然後按一下 [完成]。

    ![選取要移動之 SQL 資源的頁面](./media/tutorial-move-region-sql/select-resources.png)

8. 在要**移動的資源**中，按一下 [下一步]。

9. 在**檢閱 + 新增**中，檢查來源和目的地設定。 請確認您了解有關移動的中繼資料將會儲存在中繼資料區域中針對此用途所建立的資源群組中。


    ![用來檢閱設定並繼續移動的頁面](./media/tutorial-move-region-sql/review.png)

10. 按一下 [繼續]，開始新增資源。
11. 成功新增程序後，按一下通知圖示中的 [新增要移動的資源]。
12. 按一下通知之後，檢閱**跨區域**頁面上的資源。


> [!NOTE]
> 
> - SQL Server 現在處於「手動指派擱置」狀態。
> - 其他新增的資源處於「準備擱置」狀態。
> - 如果要從移動集合中移除資源，執行這項作業的方法取決於您在移動程序中進行到哪一個步驟。 [深入了解](remove-move-resources.md)。

## <a name="resolve-dependencies"></a>解析相依性


1. 在**跨區域**中，如果資源在**問題**資料行中顯示「驗證相依性」訊息，請按一下 [驗證相依性] 按鈕。 驗證程序開始。
2. 如果找到相依性，請按一下 [新增相依性]。

    ![加入相依性的按鈕](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. 在**新增相依性**中，選取相依資源 > **新增相依性**。 在通知中監視進度。

4. 視需要新增其他相依性，然後再次驗證相依性。 

5. 在**跨區域**頁面上，確認資源現在處於「準備擱置」狀態，且沒有任何問題。

    ![顯示資源處於準備擱置狀態的頁面](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>移動 SQL Server

指派目的地區域中的目標 SQL Server，並認可移動。

### <a name="assign-a-target-sql-server"></a>指派目標 SQL Server

1. 在**跨區域**之 SQL Server資源的**目的地組態**欄中，按一下 [未指派 資源]。
2. 在目標區域中選取現有的 SQL Server 資源。 
    
    ![顯示設定為認可移動擱置的 SQL Server 狀態之項目](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> 來源 SQL Server 狀態變更為「認可移動擱置」。 

### <a name="commit-the-sql-server-move"></a>認可 SQL Server 移動

1. 在**跨區域**中，選取 SQL Server，然後按一下 [認可移動]。
2. 在**認可資源**中，按一下 [認可]。

    ![認可 SQL Server 移動的頁面](./media/tutorial-move-region-sql/commit-sql-server.png)

3. 在通知列中追蹤移動進度。

> [!NOTE]
> 認可後，SQL Server 現在處於「刪除來源擱置」狀態。


## <a name="prepare-resources-to-move"></a>準備要移動的資源

移動來源 SQL Server 後，您可以準備移動其他資源。

## <a name="prepare-an-elastic-pool"></a>準備彈性集區

1. 在**跨區域**中，選取來源彈性集區 (demo-test1-elasticpool in our walkthrough)，然後按一下 [準備]。

    ![準備資源的按鈕](./media/tutorial-move-region-sql/prepare-elastic.png)

2. 在**準備資源**中，按一下 [準備]。
3. 當通知顯示準備程序成功時，按一下 [重新整理]。

> [!NOTE]
> 彈性集區現在處於「起始移動擱置」狀態。

## <a name="prepare-a-single-database"></a>準備單一資料庫

1. 在**跨區域**中，選取單一資料庫 (並非在彈性集區中)，然後按一下 [準備]。

    ![準備已選取資源的按鈕](./media/tutorial-move-region-sql/prepare-db.png)

2. 在**準備資源**中，按一下 [準備]。
3. 當通知顯示準備程序成功時，按一下 [重新整理]。

> [!NOTE]
> 資料庫現在處於「起始移動擱置」狀態，並已在目標區域中建立。


## <a name="move-the-pool-and-prepare-pool-databases"></a>移動集區並準備集區資料庫

若要準備彈性集區中的資料庫，彈性集區必須處於「認可移動擱置」狀態。 若要移到這個狀態，請起始集區的移動。

#### <a name="initiate-move---elastic-pool"></a>起始移動 - 彈性集區

1. 在**跨區域**中，選取來源彈性集區 (demo-test1-elasticpool in our walkthrough)，然後按一下 [起始移動]。
2. 在**移動資源**中，按一下 [起始移動]。

    
    ![用來起始彈性集區移動的按鈕](./media/tutorial-move-region-sql/initiate-elastic.png)

1. 在通知列中追蹤移動進度。
1. 當通知顯示移動成功時，按一下 [重新整理]。

> [!NOTE]
> 彈性集區現在處於「認可移動擱置」狀態。

#### <a name="prepare-database"></a>準備資料庫

1. 在**跨區域**中，選取資料庫 (demo-test2-sqldb in our walkthrough)，然後按一下 [準備]。
2. 在**準備資源**中，按一下 [準備]。

    ![用來準備彈性集區中資料庫的按鈕](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

準備期間，會在目標區域中建立目標資料庫，並開始進行資料複寫。 準備好之後，資料庫會處於「起始移動擱置」狀態。 

![準備彈性集區中已選取資料庫的按鈕](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>移動資料庫

開始移動資料庫。
1. 在**跨區域**中，選取狀態為「起始移動擱置」的資源。 然後按一下 [起始移動]。
2. 在**移動資源**中，按一下 [起始移動]。

    ![起始移動的頁面](./media/tutorial-move-region-sql/initiate-move.png)

3. 在通知列中追蹤移動進度。

> [!NOTE]
> 資料庫現在處於「認可移動擱置」狀態。


## <a name="discard-or-commit"></a>要捨棄或認可？

起始移動後，您可以決定要認可移動還是加以捨棄。 

- **捨棄**：如果您要測試，而且不想實際移動來源資源，建議您捨棄移動。 捨棄移動會將資源傳回「起始移動擱置」狀態。
- **認可**：認可完成移至目標區域的作業。 認可後，來源資源將處於「刪除來源擱置」狀態，您可以決定是否要加以刪除。


## <a name="discard-the-move"></a>捨棄移動 

您可以捨棄移動，如下所示：

1. 在**跨區域**中，選取具有「認可移動擱置」狀態的資源，然後按一下 [捨棄移動]。
2. 在**捨棄移動**中，按一下 [捨棄]。
3. 在通知列中追蹤移動進度。


> [!NOTE]
> - 捨棄資源後，資源就會處於「起始移動擱置」狀態。
> - 如果只有彈性集區，請捨棄進度，而系統會刪除在目標區域中建立的彈性集區。
> - 如果有與資料庫相關聯的彈性集區的狀態為「認可移動擱置」，您就無法捨棄彈性集區。
> - 如果您捨棄 SQL 資料庫，則不會刪除目標區域資源。 

如果要在捨棄之後再次開始移動，請選取 SQL 資料庫或彈性集區，然後再次起始移動。


## <a name="commit-the-move"></a>認可移動

完成移動資料庫和彈性集區，如下所示：


1. 檢查 SQL Server 是否處於「刪除來源擱置」狀態。
2. 在認可之前，請先將資料庫連接字串更新至目標區域。
3. 在**跨區域**中，選取 SQL 資源，然後按一下 [認可移動]。
4. 在**認可資源**中，按一下 [認可]。

    ![認可移動](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. 在通知列中追蹤認可進度。


> [!NOTE]
> 在認可程序期間，SQL 資料庫會出現停機時間。
> 已認可的資料庫和彈性集區現在會處於「刪除來源擱置」狀態。
> 認可之後，在目標資料庫上更新資料庫相關的設定，包括防火牆規則、原則和警示。


## <a name="delete-source-resources-after-commit"></a>認可後刪除來源資源

移動後，您可以選擇是否要除除來源區域中的資源。 

1. 在**跨區域**中，按一下您想要刪除之每個來源資源的名稱。
2. 在每個資源的屬性頁面上，選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 將 Azure SQL 資料庫移至另一個 Azure 區域。
> * 將 Azure SQL 彈性集區移至另一個區域。

現在，試著將 Azure VM 移至另一個區域。

> [!div class="nextstepaction"]
> [移動 Azure VM](./tutorial-move-region-virtual-machines.md)
