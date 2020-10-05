---
title: 在適用于 Vm 的 Azure Automanage 中建立自訂喜好設定
description: 瞭解如何在適用于 Vm 的 Azure Automanage 中調整設定檔，並設定您自己的喜好設定。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 377677c9e5e81487059241db68baff639a3de033
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715036"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>在適用于 Vm 的 Azure Automanage 中建立自訂喜好設定

適用于虛擬機器的 Azure Automanage 最佳作法具有預設設定設定檔，可在需要時進行調整。 本文將說明當您在新的或現有的 VM 上啟用 automanagement 時，您可以如何設定自己的設定設定檔喜好設定。

我們目前支援 [Azure 備份](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) 和 [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration)的自訂。


> [!NOTE]
> 啟用 Automanage 時，您無法在 VM 上變更設定檔或喜好設定。 您將需要停用該 VM 的 Automanage，然後使用所需的設定設定檔和喜好設定來重新啟用 Automanage。


## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)再開始。

> [!NOTE]
> 免費試用帳戶無法存取本教學課程中使用的虛擬機器。 請升級為隨用隨付訂用帳戶。

> [!IMPORTANT]
> 若要啟用 Automanage： **擁有** 者角色或 **參與者** 以及 **使用者存取系統管理員** 角色，則需要下列 Azure RBAC 許可權。


## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com/)。


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>在現有 VM 上啟用 VM 的 Automanage

1. 在搜尋列中，搜尋並選取 [Automanage – Azure 虛擬機器最佳做法]。

2. 選取 [在現有 VM 上啟用]。

3. 在 [選取機器] 刀鋒視窗上：
    1. 依 [訂用帳戶] 和 [資源群組] 來篩選 VM 清單。
    1. 核取每部欲上線虛擬機器的核取方塊。
    1. 按一下 [選取]**** 按鈕。

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="從可用 VM 清單中選取現有 VM。":::

4. 在 [組態設定檔] 底下，按一下 [瀏覽並變更設定檔及喜好設定]。

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="從可用 VM 清單中選取現有 VM。":::

5. 在 [ **選取設定設定檔 + 喜好** 設定] 分頁上，選取左側的設定檔：用於測試的 *開發/測試* *、生產環境的生產環境* 。

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="從可用 VM 清單中選取現有 VM。":::

6. 在選擇的設定檔中，您可以在 [設定 **喜好** 設定] 下，針對特定服務進行調整。
    1. 按一下 [ **建立新的喜好**設定]。
    1. 在 [ **建立設定喜好** 設定] 分頁中，填寫 [基本] 索引標籤：
        1. 訂用帳戶
        1. 資源群組
        1. 喜好設定名稱
        1. 區域

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="從可用 VM 清單中選取現有 VM。":::

7. 移至 [喜好設定] 索引標籤，並調整您想要的設定喜好設定。
        
    > [!NOTE]
    > 變更設定檔設定時，只允許在我們的最佳作法上限和下限內仍適用的調整。

8. 檢查您的設定設定檔。
9. 按一下 [建立] 按鈕。

10. 按一下 [啟用]**** 按鈕。


## <a name="disable-automanage-for-vms"></a>停用 VM 的 Automanage

停用自動管理即可快速停止使用虛擬機器的 Azure Automanage。

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="從可用 VM 清單中選取現有 VM。":::

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

取得常見問題中最常回答的常見問題。 

> [!div class="nextstepaction"]
> [常見問題集](faq.md)