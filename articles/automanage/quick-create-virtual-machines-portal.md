---
title: 快速入門 - 在 Azure 入口網站中啟用 VM 的 Azure Automanage
description: 了解如何在 Azure 入口網站中，於新 VM 或現有 VM 上快速啟用虛擬機器的 Automanage。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 43e0805a828a2ed32d23984b6ffef95a7e710953
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943730"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中啟用虛擬機器的 Azure Automanage

開始使用適用於虛擬機器的 Azure Automanage，方法是使用 Azure 入口網站在新的或現有的虛擬機器上啟用自動管理。


## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)再開始。

> [!NOTE]
> 免費試用帳戶無法存取本教學課程中使用的虛擬機器。 請升級為隨用隨付訂用帳戶。

> [!IMPORTANT]
> 需要有下列 RBAC 權限才能啟用 Automanage：**擁有者**角色或**參與者**以及**使用者存取系統管理員**角色。


## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com/)。


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>在現有 VM 上啟用 VM 的 Automanage

1. 在搜尋列中，搜尋並選取 [Automanage – Azure 虛擬機器最佳做法]。

2. 選取 [在現有 VM 上啟用]。

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="在現有 VM 上啟用。":::

3. 在 [選取機器] 刀鋒視窗上：
    1. 依 [訂用帳戶] 和 [資源群組] 來篩選 VM 清單。
    1. 核取每部欲上線虛擬機器的核取方塊。
    1. 按一下 [選取]**** 按鈕。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="從可用 VM 清單中選取現有 VM。":::

4. 在 [組態設定檔] 底下，按一下 [瀏覽並變更設定檔及喜好設定]。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="瀏覽並變更設定檔及喜好設定。":::

5. 在 [選取組態設定檔 + 喜好設定] 刀鋒視窗上：
    1. 選取左側的設定檔：[開發/測試] 可用於測試，[生產] 可用於生產。
    1. 按一下 [選取]**** 按鈕。

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="瀏覽生產組態設定檔。":::

6. 按一下 [啟用]**** 按鈕。


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>在新 VM 上啟用 VM 的 Automanage

1. 遵循[快速入門 - 在 Azure 入口網站中建立 Windows VM](..\virtual-machines\windows\quick-create-portal.md) 中的建立步驟。

2. VM 部署好之後，您會進入 [部署狀態] 頁面，且頁面底部有建議的 [後續步驟]。

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="位於 [部署] 頁面底部的 [後續步驟] 區段。":::

3. 在 [後續步驟] 底下，選取 [啟用 Automanage 虛擬機器最佳做法]。

4. 在 [Automanage – Azure 虛擬機器最佳做法] 頁面上，[機器] 會自動由新建立的 VM 填入。

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="新建立的 VM 將會顯示為選取的機器。":::

5. 在 [組態設定檔] 底下，按一下 [瀏覽並變更設定檔及喜好設定]。

6. 在 [選取組態設定檔 + 喜好設定] 刀鋒視窗上：
    1. 選取左側的設定檔：[開發/測試] 可用於測試，[生產] 可用於生產。
    1. 按一下 [選取]**** 按鈕。

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="瀏覽生產組態設定檔。":::

7. 按一下 [啟用]**** 按鈕。


## <a name="disable-automanage-for-vms"></a>停用 VM 的 Automanage

停用自動管理即可快速停止使用虛擬機器的 Azure Automanage。

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="在虛擬機器上停用 Automanage。":::

1. 移至 [Automanage – Azure 虛擬機器最佳做法] 頁面，其中會列出所有自動管理的 VM。
1. 選取所要停用虛擬機器旁的核取方塊。
1. 按一下 [停用自動管理] 按鈕。
1. 先仔細閱讀所產生快顯視窗中的訊息，然後同意 [停用]。


## <a name="clean-up-resources"></a>清除資源

如果您之前建立了新資源群組來試用虛擬機器的 Azure Automanage，但已不再需要使用，則可以刪除該資源群組。 刪除群組也會刪除 VM 和資源群組中的所有資源。

Azure Automanage 會建立用來儲存資源的預設資源群組。 檢查具有「DefaultResourceGroupRegionName」和「AzureBackupRGRegionName」命名慣例的資源群組，以清除所有資源。

1. 選取 [資源群組]。
1. 在資源群組的頁面上，選取 [刪除]。
1. 出現提示時，請確認資源群組的名稱，然後選取 [刪除]。


## <a name="next-steps"></a>下一步

在本快速入門中，您已啟用 VM 的 Azure Automanage。 

請探索在虛擬機器上啟用 Automanage 時，要如何建立及套用自訂的喜好設定。 

> [!div class="nextstepaction"]
> [VM 的 Azure Automanage - 自訂組態設定檔](virtual-machines-custom-preferences.md)
