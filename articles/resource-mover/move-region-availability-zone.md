---
title: 使用 Azure 資源移動器將 Azure Vm 移至另一個區域中的可用性區域
description: 瞭解如何使用 Azure 資源移動器將 Azure Vm 移至可用性區域。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542995"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>將 Azure Vm 移至另一個區域中的可用性區域

在本文中，瞭解如何使用 [Azure 資源移動器](overview.md)，將 azure vm (以及相關的網路/儲存體資源) 移至不同 Azure 區域中的可用性區域。

[Azure 可用性區域](../availability-zones/az-overview.md#availability-zones) 可協助保護您的 azure 部署不受資料中心失敗的影響。 每個可用性區域由一或多個資料中心組成，配備了電力、冷卻系統及網路系統。 為確保恢復功能，所有 [已啟用的區域](../availability-zones/az-region.md)中至少有三個不同的區域。 使用資源移動器，您可以移動：

- 目的地區域中的可用性區域/可用性設定組的單一實例 VM。
- 可用性設定組中的 VM 會指向目的地區域中的可用性區域/可用性設定組。
- 來源區域可用性區域中的 VM，指向目的地區域中的可用性區域。


> [!IMPORTANT]
> Azure Resource Mover 目前處於公開預覽狀態。

如果您想要將 Vm 移至相同區域中的不同可用性區域，請 [參閱這篇文章](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)。

## <a name="prerequisites"></a>必要條件

- 您要移動的資源所在之訂用帳戶的 *擁有* 者存取權。
    - 當您第一次在 Azure 訂用帳戶中新增特定來源和目的地對應的資源時，資源移動器會建立 [系統指派的受控識別](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (先前稱為「受控服務識別」 (MSI) # A3 （由訂用帳戶信任）。
    - 若要建立身分識別，並為其指派必要的角色 (來源訂用帳戶中的參與者或使用者存取管理員)，您用來新增資源的帳戶需要訂用帳戶「擁有者」權限。 [深入了解](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) Azure 角色。
- 訂用帳戶需要足夠的配額，才能在目的地區域中建立來源資源。 如果不是，請要求額外的限制。 [深入了解](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 確認與您要移動 VM 的目標區域相關聯的定價和費用。 請使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)協助確認。
    


## <a name="check-vm-requirements"></a>檢查 VM 需求

1. 請確認您想要移動的 VM 已受到支援。

    - [確認](support-matrix-move-region-azure-vm.md#windows-vm-support)支援的 Windows VM。
    - [確認](support-matrix-move-region-azure-vm.md#linux-vm-support)支援的 Linux VM 和核心版本。
    - 檢查支援的[計算](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[儲存體](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)和[網路](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)設定。
2. 請確認您想要移動的 VM 已開啟。
3. 請確定 Vm 具有最新的受信任根憑證，以及 (CRL) 的更新憑證撤銷清單。 
    - 在執行 Windows 的 Azure Vm 上，安裝最新的 Windows 更新。
    - 在執行 Linux 的 Vm 上，請遵循 Linux 散發者指導方針，以確保電腦具有最新的憑證和 CRL。 
4. 允許從 VM 進行輸出連線：
    - 如果您使用以 URL 為基礎的防火牆 Proxy 來控制輸出連線能力，請允許存取這些 [URL](support-matrix-move-region-azure-vm.md#url-access)
    - 如果您使用網路安全性群組 (NSG) 規則來控制輸出連線能力，請建立這些[服務標記規則](support-matrix-move-region-azure-vm.md#nsg-rules)。

## <a name="select-resources-to-move"></a>選取要移動的資源

選取您要移動的資源。

- 您可以在所選來源區域中的資源群組之間，選取任何支援的資源類型。
- 您將資源移至來源區域訂用帳戶中的目的地區域。 如果要變更訂用帳戶，可以在移動資源後執行此動作。

1. 在 Azure 入口網站中，搜尋並選取 [資源移動器]。 然後，在 **服務** 底下，選取 [Azure Resource Mover]。

    ![搜尋資源移動器](./media/move-region-availability-zone/search.png)

2. 請 **Overview** 先選取 [**開始** 使用]。

    ![按鈕以開始使用](./media/move-region-availability-zone/get-started.png)

3. 在 **移動資源** > **來源 + 目的地** 中，選取來源訂用帳戶和區域。
4. 在 **目的地** 中，選取您要將 VM 移至哪一個區域。 然後按一下 [下一步]  。

     ![要填入來源和目的地訂用帳戶/區域的頁面](./media/move-region-availability-zone/source-target.png)

6. 在 **要移動的資源** 中，按一下 [選取資源]。
7. 在 **選取資源** 中，選取 VM。 您只能新增支援移動的資源。 然後按一下 [完成]。 在要 **移動的資源** 中，按一下 [下一步]。

    ![選取要移動之 VM 的頁面](./media/move-region-availability-zone/select-vm.png)
8. 在 **檢閱 + 新增** 中，檢查來源和目的地設定。

    ![用來檢閱設定並繼續移動的頁面](./media/move-region-availability-zone/review.png)

9. 按一下 [繼續]，開始新增資源。
10. 成功新增程序後，按一下通知圖示中的 [新增要移動的資源]。

    ![通知中的訊息](./media/move-region-availability-zone/notification.png)

按一下通知之後，資源會出現在 [ **跨區域** ] 頁面上

> [!NOTE]
> 按一下通知之後，資源會出現在 [ **跨區域** ] 頁面上的 [ *準備暫* 止] 狀態。
> - 如果要從移動集合中移除資源，執行這項作業的方法取決於您在移動程序中進行到哪一個步驟。 [深入了解](remove-move-resources.md)。

## <a name="resolve-dependencies"></a>解析相依性

1. 如果資源在 **問題** 資料行中顯示「驗證相依性」訊息，請按一下 [驗證相依性] 按鈕。 驗證流程。
2. 如果找到相依性，請按一下 [新增相依性]。 
3. 在 **新增相依性** 中，選取相依資源 > **新增相依性**。 在通知中監視進度。

    ![加入相依性的按鈕](./media/move-region-availability-zone/add-dependencies.png)

3. 視需要新增其他相依性，然後再次驗證相依性。 

    ![新增其他相依性的頁面](./media/move-region-availability-zone/add-additional-dependencies.png)

4. 在 **跨區域** 頁面上，確認資源現在處於「準備擱置」狀態，且沒有任何問題。

    ![顯示資源處於準備擱置狀態的頁面](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>移動來源資源群組 

來源資源群組必須存在於目的地區域中，您才能準備和移動 Vm。 

### <a name="prepare-to-move-the-source-resource-group"></a>準備移動來源資源群組

請如下所述加以準備：

1. 在 **跨區域** 中，選取來源資源群組 > **準備**。
2. 在 **準備資源** 中，按一下 [準備]。

    ![用來準備來源資源群組的按鈕](./media/move-region-availability-zone/prepare-resource-group.png)

    在準備過程中，Resource Mover 會使用資源群組設定產生 Azure Resource Manager (ARM) 範本。 資源群組內的資源不會受到影響。

> [!NOTE]
>  準備好資源群組之後，會處於「起始移動擱置」狀態。 

![顯示起始暫止狀態的狀態](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>移動來源資源群組

開始移動，如下所示：

1. 在 **跨區域** 中，選取資源群組 > **起始移動**
2. 在 **移動資源** 中，按一下 [起始移動]。 資源群組會移至「正在起始移動」狀態。
3. 起始移動後，系統會根據產生的 ARM 範本來建立目標資源群組。 來源資源群組會移至「認可移動擱置」狀態。

![顯示認可移動的狀態](./media/move-region-availability-zone/commit-move-pending.png)

若要認可並完成移動程序：

1. 在 **跨區域** 中，選取資源群組 > **認可移動**
2. 在 **移動資源** 中，按一下 [認可]。

> [!NOTE]
> 認可移動後，來源資源群組會處於「刪除來源擱置」狀態。


## <a name="add-a-target-availability-zone"></a>新增目標可用性區域

在移動其餘資源之前，我們會為 VM 設定目標可用性區域。

1. 在 [ **跨區域** ] 頁面中，按一下您要移動之 VM 的 [ **目的地** 設定] 欄中的連結。

    ![VM 屬性](./media/move-region-availability-zone/select-vm-settings.png)

3. 在 [ **設定設定**] 中，指定目的地 VM 的設定。 您可以設定目的地區域中的 VM，如下所示：
    -  在目的地區域中建立新的對等資源。 除了您指定的設定，系統會使用與來源相同的設定來建立目標資源。
    - 選取目的地區域中的現有 VM。 

4. 在 [ **區域**] 中，選取要在 VM 移動時放置 VM 的區域。

    只會針對您正在編輯的資源進行變更。 您必須個別更新任何相依的資源。

5. 在 **SKU** 中，指定您想要指派給目標 VM 的 [Azure 層](..//virtual-machines/sizes.md) 。
6. 如果您想要在可用性區域中的可用性設定組內執行目標 VM，請在 [ **可用性設定組**] 中選取可用性設定組。
7. 選取 [儲存變更]。

    ![VM 設定](./media/move-region-availability-zone/vm-settings.png)


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

1. 在 **跨區域** 中，選取狀態為「起始移動擱置」的資源。 然後按一下 [**起始移動**]
2. 在 **移動資源** 中，按一下 [起始移動]。

    ![起始資源移動的頁面](./media/move-region-availability-zone/initiate-move.png)

3. 在通知列中追蹤移動進度。


> [!NOTE]
> - 針對 VM，會在目標區域中建立複本 VM。 來源 VM 已關閉，而且會出現停機時間 (通常以分鐘計算)。
> - Resource Mover 會使用已備妥的 ARM 範本重新建立其他資源。 如此通常不會有停機時間。
> - 準備資源之後，就會處於 *認可移動擱置* 中狀態。


![顯示認可移動暫止狀態中之資源的頁面](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>要捨棄或認可？

起始移動後，您可以決定要認可移動還是加以捨棄。 

- **捨棄**：如果您要測試，而且不想實際移動來源資源，建議您捨棄移動。 捨棄移動會將資源傳回「起始移動擱置」狀態。
- **認可**：認可完成移至目標區域的作業。 認可後，來源資源將處於「刪除來源擱置」狀態，您可以決定是否要加以刪除。

## <a name="discard-the-move"></a>捨棄移動 

您可以捨棄移動，如下所示：

1. 在 **跨區域** 中，選取具有「認可移動擱置」狀態的資源，然後按一下 [捨棄移動]。
2. 在 **捨棄移動** 中，按一下 [捨棄]。
3. 在通知列中追蹤移動進度。
 

> [!NOTE]
> 針對 Vm，在捨棄資源之後，它們就會處於 *起始移動暫* 止狀態。

## <a name="commit-the-move"></a>認可移動

如果要完成移動程序，請認可移動。 

1. 在 **跨區域** 中，選取具有「認可移動擱置」狀態的資源，然後按一下 [認可移動]。
2. 在 **認可資源** 中，按一下 [認可]。

    ![用來認可資源以完成移動的頁面](./media/move-region-availability-zone/commit-resources.png)

3. 在通知列中追蹤認可進度。

> [!NOTE]
> - 認可移動後，VM 會停止複寫。 來源 VM 不會受到認可的影響。
> - 認可不會影響來源網路資源。
> - 認可移動後，資源會處於「刪除來源擱置」狀態。

![顯示資源處於*刪除來源擱置*狀態的頁面](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>移動後設定

行動服務不會自動從 VM 解除安裝。 請手動解除安裝，如果您打算再次移動伺服器，請加以保留。


## <a name="delete-source-resources-after-commit"></a>認可後刪除來源資源

移動後，您可以選擇是否要除除來源區域中的資源。

1. 在 **跨區域** 中，按一下您想要刪除之每個來源資源的名稱。
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

[瞭解](about-move-process.md) 移動流程。