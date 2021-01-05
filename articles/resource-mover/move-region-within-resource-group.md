---
title: 使用 Azure 資源移動器將資源移至另一個區域
description: 瞭解如何使用 Azure 資源移動器將資源群組內的資源移至另一個區域。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 79224c14fc5182df7a699864af3d78c9be36259f
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797263"
---
# <a name="move-resources-across-regions-from-resource-group"></a>跨區域 (資源群組) 移動資源

在本文中，您將瞭解如何將特定資源群組中的資源移至不同的 Azure 區域。 在資源群組中，您可以選取要移動的資源。 然後，您可以使用 [Azure 資源移動器](overview.md)移動它們。

> [!IMPORTANT]
> Azure Resource Mover 目前處於公開預覽狀態。


## <a name="prerequisites"></a>必要條件

- 您需要訂用帳戶的 *擁有* 者存取權，而您想要移動的資源位於該訂用帳戶。
    - 當您第一次在 Azure 訂用帳戶中新增特定來源和目的地對應的資源時，資源移動器會建立 [系統指派的受控識別](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (先前稱為「受控服務識別」 (MSI) # A3 （由訂用帳戶信任）。
    - 若要建立身分識別，並為其指派必要的角色 (來源訂用帳戶中的參與者或使用者存取管理員)，您用來新增資源的帳戶需要訂用帳戶「擁有者」權限。 [深入了解](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) Azure 角色。
- 訂用帳戶需要足夠的配額，才能在目的地區域中建立來源資源。 如果不是，請要求額外的限制。 [深入了解](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 確認與您要移動 VM 的目標區域相關聯的定價和費用。 請使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)協助確認。
- 確認資源移動器支援您要移動的資源：
    - Azure VM 和相關聯的磁碟
    - NIC
    - 可用性設定組
    - Azure 虛擬網路
    - 公用 IP 位址
    - 網路安全性群組 (NSG)。
    - 內部和公用負載平衡器
    - Azure SQL 資料庫和彈性集區


## <a name="check-vm-requirements"></a>檢查 VM 需求

1. 請確認您想要移動的 VM 已受到支援。

    - [確認](support-matrix-move-region-azure-vm.md#windows-vm-support)支援的 Windows VM。
    - [確認](support-matrix-move-region-azure-vm.md#linux-vm-support)支援的 Linux VM 和核心版本。
    - 檢查支援的[計算](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[儲存體](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)和[網路](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)設定。
2. 請確定 Vm 具有最新的受信任根憑證，以及 (CRL) 的更新憑證撤銷清單。 
    - 在執行 Windows 的 Azure Vm 上，安裝最新的 Windows 更新。
    - 在執行 Linux 的 Vm 上，請遵循 Linux 散發者指導方針，以確保電腦具有最新的憑證和 CRL。 
3. 允許從 VM 進行輸出連線：
    - 如果您使用以 URL 為基礎的防火牆 Proxy 來控制輸出連線能力，請允許存取這些 [URL](support-matrix-move-region-azure-vm.md#url-access)
    - 如果您使用網路安全性群組 (NSG) 規則來控制輸出連線能力，請建立這些[服務標記規則](support-matrix-move-region-azure-vm.md#nsg-rules)。

## <a name="select-resources-to-move"></a>選取要移動的資源

選取您要移動的資源。 您將資源移至來源區域訂用帳戶中的目的地區域。 如果要變更訂用帳戶，可以在移動資源後執行此動作。

> [!NOTE]
>  請勿選取相關聯的磁片，否則作業將會失敗。 相關聯的磁片會自動包含在 VM 移動中。

1. 在 Azure 入口網站中，開啟相關的資源群組。
2. 在 [資源群組] 頁面中，選取您要移動的資源。
3. 選取 [**移**  >  **至另一個區域**]。

    ![將資源移至不同區域的選取範圍](./media/move-region-within-resource-group/select-move-region.png)
    
4. 在 [ **來源 + 目的地**] 中，選取您要移動資源的目的地區域。 然後選取 [下一步]。


    ![選取目的地區域的 [來源] 和 [目的地] 頁面](./media/move-region-within-resource-group/source-target.png)


7. 在 [ **要移動** 的資源 **] 中選取 [下一步]**。  
8. 在 [ **選取資源**] 中，選取您要移動的資源。 您只能新增支援移動的資源。 然後選取 [完成]。
9. 在 [ **移動資源**] 中選取 **[下一步]**。 
10. 在 [ **檢查 + 新增**] 中，檢查來源和目標詳細資料。
11. 確認您瞭解所要移動資源的相關中繼資料會儲存在針對此用途所建立的資源群組中，而且您可以讓資源移動器建立系統管理的身分識別來存取訂用帳戶資源。
1. 選取 [ **繼續** ] 開始新增資源。

    ![檢查詳細資料並繼續移動的摘要頁面](./media/move-region-within-resource-group/summary.png)    

11. [新增資源] 作業隨即啟動。 當作業完成時，通知會顯示已新增資源，且部署成功。
13. 在 [通知] 中，選取 [ **新增要移動的資源**]。

    ![通知中顯示的訊息](./media/move-region-within-resource-group/notification.png)    


14. 選取通知之後，您選取的資源會新增至 Azure 資源移動器中樞的移動集合中。  資源移動器可協助您檢查相依性，然後開始將資源移至目的地區域。

## <a name="resolve-dependencies"></a>解析相依性

您要移動的資源會出現在 [ **跨區域** ] 頁面中，處於「 *準備暫* 止」狀態。 開始驗證，如下所示：

1. 如果資源在 [**問題**] 欄中顯示 [驗證相依性 *]* 訊息，請選取 [驗證相依性 **]** 按鈕。 驗證程序開始。

    ![用來驗證相依性的按鈕](./media/move-region-within-resource-group/validate-dependencies.png)

2. 如果找到相依性，請選取 [新增相依性 **]**。 
3. 在 **新增相依性** 中，選取相依資源 > **新增相依性**。 在通知中監視進度。

    ![加入相依性的按鈕](./media/move-region-within-resource-group/add-dependencies.png)

3. 視需要新增其他相依性，並依需要驗證相依性。 選取 **[** 重新整理] 以確保資源顯示最新狀態。

4. 在 **跨區域** 頁面上，確認資源現在處於「準備擱置」狀態，且沒有任何問題。

    ![顯示所有資源準備擱置狀態的頁面](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>移動來源資源群組 

來源資源群組必須存在於目的地區域中，您才能準備和移動資源。 

### <a name="prepare-to-move-the-source-resource-group"></a>準備移動來源資源群組

請如下所述加以準備：

1. 在 **跨區域** 中，選取來源資源群組 > **準備**。
2. 在 [ **準備資源**] 中，選取 [ **準備**]。
1. 
    ![用來準備來源資源群組的按鈕](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    在準備過程中，Resource Mover 會使用資源群組設定產生 Azure Resource Manager (ARM) 範本。 資源群組內的資源不會受到影響。
    
> [!NOTE]
>  準備好資源群組之後，會處於「起始移動擱置」狀態。 重新整理以顯示最新狀態。

![顯示起始暫止狀態的狀態](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>移動來源資源群組

開始移動，如下所示：

1. 在 **跨區域** 中，選取資源群組 > **起始移動**
2. ln **移動資源**，請選取 [ **起始移動**]。 資源群組會移至「正在起始移動」狀態。
3. 起始移動後，系統會根據產生的 ARM 範本來建立目標資源群組。 來源資源群組會移至「認可移動擱置」狀態。

![顯示認可移動的狀態](./media/move-region-availability-zone/commit-move-pending.png)

若要認可並完成移動程序：

1. 在 **跨區域** 中，選取資源群組 > **認可移動**
2. ln **移動資源**，請選取 [ **認可**]。

> [!NOTE]
> 認可移動後，來源資源群組會處於「刪除來源擱置」狀態。

## <a name="modify-target-settings"></a>修改目標設定

如果您不想要移動來源資源，可以執行下列其中一項：

- 使用與來源區域中資源相同的名稱和設定，在目的地區域中建立資源。
- 在目的地區域中建立新的對等資源。 除了您指定的設定，系統會使用與來源相同的設定來建立目標資源。
- 使用目的地區域中的現有資源。

修改設定，如下所示：

1. 若要修改設定，請在資源的 [ **目的地** 設定] 欄中選取專案。
2. 在 [ **目的地** 設定] 頁面中，指定您要使用的目標設定。
    只會針對您正在編輯的資源進行變更。 您必須個別更新任何相依的資源。   
    
您所修改的確切設定取決於資源類型。 [深入瞭解](modify-target-settings.md) 如何編輯目標設定。

## <a name="prepare-resources-to-move"></a>準備要移動的資源

現在已移動來源資源群組，您可以準備移動其他資源。

1. 在 **跨區域** 中，選取要準備的資源。 

    ![選取準備其他資源的頁面](./media/move-region-availability-zone/prepare-other.png)

2. 選取 [準備]。

> [!NOTE]
> - 在準備程式期間，Azure Site Recovery 行動代理程式會安裝在 Vm 上，以供複寫。
> - VM 資料會定期複寫至目標區域。 這不會影響來源 VM。
> - 資源移動會產生其他來源資源的 ARM 範本。
> - 準備好資源之後，就會處於「起始移動擱置」狀態。

![顯示資源處於起始移動擱置狀態的頁面](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>起始移動

準備好資源後，您就可以開始移動。

1. 在 **跨區域** 中，選取狀態為「起始移動擱置」的資源。 然後選取 [ **起始移動**]。
2. 在 [ **移動資源**] 中，選取 [ **起始移動**]。

    ![針對 [起始移動] 按鈕選取](./media/move-region-within-resource-group/initiate-move.png)

3. 在通知列中追蹤移動進度。


> [!NOTE]
> - 針對 VM，會在目標區域中建立複本 VM。 來源 VM 已關閉，而且會出現停機時間 (通常以分鐘計算)。<br/>
> - Resource Mover 會使用已備妥的 ARM 範本重新建立其他資源。 如此通常不會有停機時間。<br/> 
> - 針對負載平衡器，不會複製 NAT 規則。 認可移動之後，請在目的地區域中建立它們。
> - 若為公用 IP 位址，則不會複製 DNS 名稱標籤。 認可移動之後，請重新建立標籤。
> - 準備資源之後，就會處於 *認可移動擱置* 中狀態。


## <a name="discard-or-commit"></a>要捨棄或認可？

起始移動後，您可以決定要認可移動還是加以捨棄。 

- **捨棄**：如果您要測試，而且不想實際移動來源資源，建議您捨棄移動。 捨棄移動會將資源傳回「起始移動擱置」狀態。
- **認可**：認可完成移至目標區域的作業。 認可後，來源資源將處於「刪除來源擱置」狀態，您可以決定是否要加以刪除。


## <a name="discard-the-move"></a>捨棄移動 

您可以捨棄移動，如下所示：

1. 在 [ **跨區域**] 中，選取 [狀態 *認可移動擱置* 中的資源]，然後選取 [ **捨棄移動**]。
2. 在 [ **捨棄移動**] 中，選取 [ **捨棄**]。
3. 在通知列中追蹤移動進度。
4. 當通知顯示移動成功時， **請選取 [** 重新整理]。 

> [!NOTE]
> 針對 Vm，在捨棄資源之後，它們就會處於 *起始移動暫* 止狀態。

## <a name="commit-the-move"></a>認可移動

如果要完成移動程序，請認可移動。 


1. 在 [ **跨區域**] 中，選取 [狀態 *認可移動擱置* 中的資源]，然後選取 [ **認可移動**]。
2. 在 [ **認可資源**] 中，選取 [ **認可**]。

    ![用來認可資源以完成移動的頁面](./media/move-region-within-resource-group/commit-resources.png)

3. 在通知列中追蹤認可進度。

> [!NOTE]
> - 認可移動後，VM 會停止複寫。 來源 VM 不會受到認可的影響。
> - 認可不會影響來源網路資源。
> - 認可移動後，資源會處於「刪除來源擱置」狀態。

## <a name="configure-settings-after-the-move"></a>移動後設定

1. 因為不會針對公用 IP 位址複製 DNS 名稱標籤，所以在移動完成之後，請流覽至目標資源並更新標籤。 
2. 對於內部負載平衡器，由於不會複製 NAT 規則，請流覽至目的地區域中建立的資源，並更新 NAT 規則。
3. 行動服務不會自動從 VM 解除安裝。 請手動解除安裝，如果您打算再次移動伺服器，請加以保留。
## <a name="delete-source-resources-after-commit"></a>認可後刪除來源資源

移動後，您可以選擇是否要除除來源區域中的資源。 

1. 在 [ **跨區域**] 中，選取您要刪除之每個來源資源的名稱。
2. 在每個資源的屬性頁面上，選取 [刪除]。

## <a name="delete-additional-resources-created-for-move"></a>刪除為移動建立的其他資源

移動後，您可以手動刪除移動集合，以及建立的 Site Recovery 資源。

- 移動集合預設為隱藏。 若要查看，您必須開啟隱藏的資源。
- 必須刪除快取儲存體的鎖定，才能刪除。

如下所示加以刪除： 

1. 找出資源群組中 ```RegionMoveRG-<sourceregion>-<target-region>``` 來源區域內的資源。
2. 檢查移動集合中的所有 VM 和其他來源資源是否已移動/刪除。 這可確保沒有任何擱置中的資源正在使用這些項目。
2. 刪除資源：

    - 移動集合名稱為 ```movecollection-<sourceregion>-<target-region>```。
    - 快取儲存體帳戶名稱為 ```resmovecache<guid>```
    - 保存庫名稱為 ```ResourceMove-<sourceregion>-<target-region>-GUID```。

## <a name="next-steps"></a>後續步驟


[瞭解](about-move-process.md) 移動流程。