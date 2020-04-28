---
title: 使用入口網站建立鄰近放置群組
description: 瞭解如何使用 Azure 入口網站建立鄰近放置群組。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: cynthn
ms.openlocfilehash: 6a14e2bd7385430c3d0fbec06259a876af556e38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190396"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>使用入口網站建立鄰近放置群組

若要盡可能接近 Vm，達到最低可能的延遲，您應該將它們部署在[鄰近放置群組](co-location.md#proximity-placement-groups)內。

鄰近放置群組是一種邏輯群組，用來確保 Azure 計算資源實際位於彼此接近的位置。 鄰近放置群組適用于需要低延遲的工作負載。

> [!NOTE]
> 鄰近放置群組不能搭配專用主機使用。
>
> 如果您想要將可用性區域與放置群組搭配使用，您必須確定放置群組中的 Vm 也全都位於相同的可用性區域中。
>

## <a name="create-the-proximity-placement-group"></a>建立鄰近位置群組

1. 在搜尋中輸入**鄰近位置群組**。
1. 在搜尋結果中的 [**服務**] 底下，選取 [**鄰近放置群組**]。
1. 在 [**鄰近放置群組**] 頁面中，選取 [**新增**]。
1. 在 [基本]**** 索引標籤中的 [專案詳細資料]**** 底下，確認已選取正確的訂用帳戶。
1. 在 [**資源群組**] 中，選取 **[新建] 以建立**新的群組，或從下拉式選單選取已存在的空白資源群組。 
1. 在 [**區域**] 中，選取您想要建立鄰近放置群組的位置。
1. 在 [**鄰近位置組名**] 中輸入名稱，然後選取 [**審核 + 建立**]。
1. 通過驗證之後，請選取 [**建立**] 以建立鄰近位置群組。

    ![建立近接位置群組的螢幕擷取畫面](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>建立 VM

1. 在入口網站中建立 VM 時，請移至 [ **Advanced** ] 索引標籤。 
1. 在**鄰近放置群組**選取專案中，選取正確的放置群組。 

    ![在入口網站中建立新的 VM 時的鄰近放置群組區段的螢幕擷取畫面](./media/ppg/vm-ppg.png)

1. 當您完成所有其他必要的選取專案時，請選取 [**審查 + 建立**]。
1. 通過驗證之後，請選取 [**建立**] 以在放置群組中部署 VM。


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>將可用性設定組中的 Vm 新增至鄰近放置群組

如果 VM 是可用性設定組的一部分，則您必須先將可用性設定組新增至放置群組，然後再新增 Vm。

1. 在[入口網站](https://portal.azure.com)中，搜尋 [*可用性*設定組]，然後從結果中選取您的可用性設定組。
1. 選取 VM，然後在 VM 的頁面上選取 [**停止**]，以 Stop\deallocate 可用性設定組中的每個 vm，然後選取 **[確定]** 以停止 vm。
1. 在可用性設定組的頁面上，確認所有 Vm 的**狀態**都是 [**已停止（已解除配置）**]。
1. 在左側功能表中 **，選取 [** 設定]。
1. 在 [**鄰近位置群組**] 底下，從下拉式選單選取 [放置群組]，然後選取 [**儲存**]。
1. 從左側功能表中選取 **[總覽**]，以再次查看 vm 清單。 
1. 選取可用性設定組中的每個 VM，然後在每個 VM 的頁面上選取 [**啟動**]。 


## <a name="add-existing-vm-to-placement-group"></a>將現有的 VM 新增至放置群組 


1. 在 VM 的頁面上，選取 [**停止**]。
1. 一旦 VM 的 [狀態] 列為 [**已停止（已解除配置）**]，**請選取左側**功能表上的 [設定]。
1. 在 [**鄰近位置群組**] 底下，從下拉式選單選取 [放置群組]，然後選取 [**儲存**]。
1. 從左側功能表中選取 **[總覽**]，然後選取 [**啟動**] 以重新開機 VM。

 

## <a name="next-steps"></a>後續步驟

您也可以使用[Azure PowerShell](proximity-placement-groups.md)來建立鄰近放置群組。

