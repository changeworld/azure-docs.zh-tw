---
title: 使用 Azure Resource Mover 跨區域移動 Azure VM
description: 了解如何使用 Azure Resource Mover 將 Azure VM 移至另一個區域
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 49b7a3700bf497ad868b7c4ab1f0802564b61bf3
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652273"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>教學課程：跨區域移動 Azure VM

在本文中，您將了解如何使用 [Azure Resource Mover](overview.md)，將 Azure VM 和相關的網路/儲存體資源移至不同的 Azure 區域。

> [!NOTE]
> Azure Resource Mover 目前處於公開預覽狀態。


在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢查必要條件和需求。
> * 選取您要移動的資源。
> * 解析資源相依性。
> * 準備及移動來源資源群組。 
> * 準備及移動其他資源群組。
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
- 確認與您要移動 VM 的目標區域相關聯的定價和費用。 請使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)協助確認。
    

## <a name="check-vm-requirements"></a>檢查 VM 需求

1. 請確認您想要移動的 VM 已受到支援。

    - [確認](support-matrix-move-region-azure-vm.md#windows-vm-support)支援的 Windows VM。
    - [確認](support-matrix-move-region-azure-vm.md#linux-vm-support)支援的 Linux VM 和核心版本。
    - 檢查支援的[計算](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[儲存體](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)和[網路](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)設定。
2. 請確認您想要移動的 VM 已開啟。
3. 請確定 VM 具有最新的受信任根憑證，以及已更新的憑證撤銷清單 (CRL)。 若要這樣做：
    - 在 Windows VM 上，安裝最新的 Windows 更新。
    - 在 Linux VM 上，遵循散發者指導，讓電腦擁有最新的憑證和 CRL。 
4. 允許從 VM 進行輸出連線：
    - 如果您使用以 URL 為基礎的防火牆 Proxy 來控制輸出連線能力，請允許存取這些 [URL](support-matrix-move-region-azure-vm.md#url-access)
    - 如果您使用網路安全性群組 (NSG) 規則來控制輸出連線能力，請建立這些[服務標記規則](support-matrix-move-region-azure-vm.md#nsg-rules)。

## <a name="select-resources"></a>選取資源 

選取您要移動的資源。

- 在所選來源區域內的資源群組中，會顯示所有支援的資源類型。
- 您可以將資源移至與來源區域位於相同訂用帳戶中的目標區域。 如果要變更訂用帳戶，可以在移動資源後執行此動作。

1. 在 Azure 入口網站中，搜尋並選取 [資源移動器]。 然後，在**服務**底下，選取 [Azure Resource Mover]。

    ![Azure 入口網站中資源移動器的搜尋結果](./media/tutorial-move-region-virtual-machines/search.png)

2. 在**概觀**中，按一下 [開始使用]。

    ![新增要移至另一個區域之資源的按鈕](./media/tutorial-move-region-virtual-machines/get-started.png)

3. 在**移動資源** > **來源 + 目的地**中，選取來源訂用帳戶和區域。
4. 在**目的地**中，選取您要將 VM 移至哪一個區域。 然後按一下 [下一步]  。
5. 在**中繼資料區域**中，選取您要在其中儲存所要移動之資源的相關中繼資料。 系統會特別針對此用途建立資源群組。 然後按一下 [下一步]  。

    ![選取來源和目標區域的頁面](./media/tutorial-move-region-virtual-machines/source-target.png)

6. 在**要移動的資源** 中，按一下 [選取資源]。
7. 在**選取資源**中，選取 VM。 您只能新增[支援移動的資源](#check-vm-requirements)。 然後按一下 [完成]。

    ![選取要移動之 VM 的頁面](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  在要**移動的資源**中，按一下 [下一步]。
9. 在**檢閱 + 新增**中，檢查來源和目的地設定。 請確認您了解有關移動的中繼資料將會儲存在中繼資料區域中針對此用途所建立的資源群組中。

    ![用來檢閱設定並繼續移動的頁面](./media/tutorial-move-region-virtual-machines/review.png)
10. 按一下 [繼續]，開始新增資源。
11. 成功新增程序後，按一下通知圖示中的 [新增要移動的資源]。
12. 按一下通知之後，檢閱**跨區域**頁面上的資源。

> [!NOTE]
> - 新增的資源處於「準備擱置」狀態。
> - 如果要從移動集合中移除資源，執行這項作業的方法取決於您在移動程序中進行到哪一個步驟。 [深入了解](remove-move-resources.md)。

## <a name="resolve-dependencies"></a>解析相依性

1. 如果資源在**問題**資料行中顯示「驗證相依性」訊息，請按一下 [驗證相依性] 按鈕。 驗證程序開始。
2. 如果找到相依性，請按一下 [新增相依性]。 
3. 在**新增相依性**中，選取相依資源 > **新增相依性**。 在通知中監視進度。

    ![新增相依性](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. 視需要新增其他相依性，然後再次驗證相依性。 
    ![新增其他相依性的頁面](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)

4. 在**跨區域**頁面上，確認資源現在處於「準備擱置」狀態，且沒有任何問題。

    ![顯示資源處於準備擱置狀態的頁面](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

> [!NOTE]
> 如果要在開始移動前編輯目標設定，請在資源的**目的地組態**資料行中選取連結，然後編輯設定。 如果您編輯目標 VM 設定，目標 VM 大小不應小於來源 VM 大小。  

## <a name="move-the-source-resource-group"></a>移動來源資源群組 

在準備和移動 VM 之前，VM 資源群組必須存在於目標區域中。 

### <a name="prepare-to-move-the-source-resource-group"></a>準備移動來源資源群組

在準備過程中，Resource Mover 會使用資源群組設定產生 Azure Resource Manager (ARM) 範本。 資源群組內的資源不會受到影響。

請如下所述加以準備：

1. 在**跨區域**中，選取來源資源群組 > **準備**。
2. 在**準備資源**中，按一下 [準備]。

    ![準備資源群組](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> 準備好資源群組之後，會處於「起始移動擱置」狀態。 

 
### <a name="move-the-source-resource-group"></a>移動來源資源群組

開始移動，如下所示：

1. 在**跨區域**中，選取資源群組 > **起始移動**
2. 在**移動資源**中，按一下 [起始移動]。 資源群組會移至「正在起始移動」狀態。
3. 起始移動後，系統會根據產生的 ARM 範本來建立目標資源群組。 來源資源群組會移至「認可移動擱置」狀態。

    ![按一下起始移動按鈕](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

若要認可並完成移動程序：

1. 在**跨區域**中，選取資源群組 > **認可移動**。
2. 在**移動資源**中，按一下 [認可]。

> [!NOTE]
> 認可移動後，來源資源群組會處於「刪除來源擱置」狀態。

## <a name="prepare-resources-to-move"></a>準備要移動的資源

現在已移動來源資源群組，您可以準備移動其他資源。

1. 在**跨區域**中，選取要準備的資源。 

    ![選取準備其他資源的頁面](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. 選取 [準備]。 

> [!NOTE]
> - 在準備程序中，系統會在 VM 上安裝 Azure Site Recovery 行動代理程式以進行複寫。
> - VM 資料會定期複寫至目標區域。 這不會影響來源 VM。
> - 資源移動會產生其他來源資源的 ARM 範本。
> - 準備好資源之後，就會處於「起始移動擱置」狀態。

![顯示資源處於起始移動擱置狀態的頁面](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>起始移動

準備好資源後，您就可以開始移動。 

1. 在**跨區域**中，選取狀態為「起始移動擱置」的資源。 然後按一下 [起始移動]。
2. 在**移動資源**中，按一下 [起始移動]。

    ![按一下起始移動按鈕](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. 在通知列中追蹤移動進度。

> [!NOTE]
> - 針對 VM，會在目標區域中建立複本 VM。 來源 VM 已關閉，而且會出現停機時間 (通常以分鐘計算)。
> - Resource Mover 會使用已備妥的 ARM 範本重新建立其他資源。 如此通常不會有停機時間。
> - 移動資源後，資源會處於「認可移動擱置」狀態。

![顯示資源處於*刪除來源擱置*狀態的頁面](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


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
> 捨棄資源後，VM 就會處於「起始移動擱置」狀態。

## <a name="commit-the-move"></a>認可移動

如果要完成移動程序，請認可移動。 

1. 在**跨區域**中，選取具有「認可移動擱置」狀態的資源，然後按一下 [認可移動]。
2. 在**認可資源**中，按一下 [認可]。

    ![用來認可資源以完成移動的頁面](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. 在通知列中追蹤認可進度。

> [!NOTE]
> - 認可移動後，VM 會停止複寫。 來源 VM 不會受到認可的影響。
> - 認可不會影響來源網路資源。
> - 認可移動後，資源會處於「刪除來源擱置」狀態。

![顯示資源處於*刪除來源擱置*狀態的頁面](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>移動後設定

行動服務不會自動從 VM 解除安裝。 請手動解除安裝，如果您打算再次移動伺服器，請加以保留。

## <a name="delete-source-resources-after-commit"></a>認可後刪除來源資源

移動後，您可以選擇是否要除除來源區域中的資源。 

1. 在**跨區域**中，按一下您想要刪除之每個來源資源的名稱。
2. 在每個資源的屬性頁面上，選取 [刪除]。

## <a name="delete-additional-resources-created-for-move"></a>刪除為移動建立的其他資源

移動後，您可以手動刪除移動集合，以及建立的 Site Recovery 資源。

- 移動集合預設為隱藏。 若要查看，您必須開啟隱藏的資源。
- 必須刪除快取儲存體的鎖定，才能刪除。

如下所示加以刪除： 
1. 尋找資源群組中的資源 ```RegionMoveRG-<sourceregion>-<target-region>```。
2. 檢查是否已移動或刪除來源區域中的所有 VM 和其他來源資源。 這可確保沒有任何擱置中的資源正在使用這些項目。
2. 刪除資源：

    - 移動集合名稱為 ```movecollection-<sourceregion>-<target-region>```。
    - 快取儲存體帳戶名稱為 ```resmovecache<guid>```
    - 保存庫名稱為 ```ResourceMove-<sourceregion>-<target-region>-GUID```。
## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 將 Azure VM 移至另一個 Azure 區域。
> * 已將與 VM 相關聯的資源移至另一個區域。

現在，試著將 Azure SQL 資料庫和彈性集區移至另一個區域。

> [!div class="nextstepaction"]
> [移動 Azure SQL 資源](./tutorial-move-region-sql.md)
