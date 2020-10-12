---
title: 使用 Azure 入口網站部署 Azure 專用主機
description: 使用 Azure 入口網站將 Vm 和擴展集部署到專用主機。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/04/2020
ms.author: cynthn
ms.openlocfilehash: a6bef4944207e26f2de93daa89fa1418c5c44c4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91373081"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>使用入口網站將 Vm 和擴展集部署到專用主機 

本文會引導您瞭解如何建立 Azure [專用主機](dedicated-hosts.md)來裝載您的虛擬機器 (VM)。 


> [!IMPORTANT]
> 本文也涵蓋 Vm 和擴展集實例的自動放置。 自動放置目前處於公開預覽狀態。
> 若要參與預覽，請完成預覽上線問卷 [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) 。
> 若要在 Azure 入口網站中流覽預覽功能，您必須使用此 URL： [https://aka.ms/vmssadh](https://aka.ms/vmssadh) 。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="limitations"></a>限制

- 專用主機可用的大小和硬體類型因區域而異。 若要深入瞭解，請參閱主機[定價頁面](https://aka.ms/ADHPricing)。

## <a name="create-a-host-group"></a>建立主機群組

**主機群組**是代表專用主機集合的資源。 您會在區域和可用性區域中建立主機群組，並在其中新增主機。 在規劃高可用性時，還有其他選項。 您可以使用下列其中一個或兩個選項搭配您的專用主機： 
- 跨越多個可用性區域。 在此情況下，您必須在想要使用的每個區域中都有一個主機群組。
- 跨越多個對應至實體機架的容錯網域。 
 
不論是哪一種情況，您都必須為主機群組提供容錯網域計數。 如果您不想在您的群組中跨越容錯網域，請使用容錯網域計數 1。 

您也可以決定同時使用可用性區域和容錯網域。 

在此範例中，我們將建立使用1個可用性區域和2個容錯網域的主機群組。 


1. 開啟 Azure [入口網站](https://portal.azure.com)。 如果您想要試用 **自動放置**的預覽，請使用下列 URL： [https://aka.ms/vmssadh](https://aka.ms/vmssadh) 。
1. 選取左上角的 [ **建立資源** ]。
1. 搜尋 **主機群組** ，然後從結果中選取 [ **主機群組** ]。
1. 在 [ **主機群組** ] 頁面中，選取 [ **建立**]。
1. 選取您要使用的訂用帳戶，然後選取 [ **建立新** 的] 以建立新的資源群組。
1. 輸入 *myDedicatedHostsRG* 做為 **名稱** ，然後選取 **[確定]**。
1. 針對 [ **主機組名**]，輸入 *myHostGroup*。
1. 在 [位置] 中，選取 [美國東部]。
1. 針對 [ **可用性區域**]，選取 **1**。
1. 針對 [ **容錯網域計數**]，選取 [ **2**]。
1. 如果您使用 **自動放置** URL，請選取此選項，將 vm 和擴展集實例自動指派給此群組中的可用主機。
1. 選取 [ **審核 + 建立** ]，然後等候驗證。
1. 當您看到 [ **通過驗證** ] 訊息後，請選取 [ **建立** ] 以建立主機群組。

建立主機群組應該只需要幾分鐘的時間。


## <a name="create-a-dedicated-host"></a>建立專用主機

現在建立主機群組中的專用主機。 除了主機的名稱之外，您還必須提供主機的 SKU。 主機 SKU 會擷取支援的 VM 系列，以及專用主機的硬體世代。

如需主機 SKU 和定價的詳細資訊，請參閱 [Azure 專用主機定價](https://aka.ms/ADHPricing)。

如果您為主機群組設定容錯網域計數，系統會要求您指定主機的容錯網域。  

1. 選取左上角的 [ **建立資源** ]。
1. 搜尋 **專用主機** ，然後從結果中選取 [ **專用主機** ]。
1. 在 [ **專用主機** ] 頁面中，選取 [ **建立**]。
1. 選取您要使用的訂用帳戶識別碼。
1. 選取 [ *myDedicatedHostsRG* ] 作為 **資源群組**。
1. 在 [ **實例詳細資料**] 中，輸入 *Myhost 代表* 做為 **名稱** ，並選取 [ *美國東部* ] 作為位置。
1. 在 **[硬體設定檔**] 中，選取 [ *標準 Es3 系列-類型 1* ] 作為 [ **大小家族**]，選取 [ *myHostGroup* ] 作為 [ **主機群組** ]，然後選取 [ *1* ] 作為 [ **容錯網域**] 保留其餘欄位的預設值。
1. 當您完成時，請選取 [ **審核 + 建立** ] 並等候驗證。
1. 當您看到 [ **通過驗證** ] 訊息後，請選取 [ **建立** ] 以建立主機。

## <a name="create-a-vm"></a>建立 VM

1. 選擇 Azure 入口網站左上角的 [建立資源]。
1. 在 Azure Marketplace 資源清單上方的搜尋方塊中，搜尋並選取您要使用的映射，然後選擇 [ **建立**]。
1. 在 [ **基本** ] 索引標籤的 [ **專案詳細資料**] 底下，確認已選取正確的訂用帳戶，然後選取 [ *myDedicatedHostsRG* ] 作為 **資源群組**。 
1. 在 [執行個體詳細資料]**** 底下，輸入 myVM** 作為 [虛擬機器名稱]****，然後選擇 [美國東部]** 作為您的 [位置]****。
1. 在 [ **可用性選項** ] 中選取 [ **可用性區域**]，然後從下拉式清單中選取 *1* 。
1. 針對 [大小]，選取 [ **變更大小**]。 在可用大小清單中，從 Esv3 系列選擇一個，例如 **標準 E2s v3**。 您可能需要清除篩選準則，才能看到所有可用的大小。
1. 視需要在 [ **基本** ] 索引標籤上完成其餘的欄位。
1. 在頁面頂端，選取 [ **Advanced** ] 索引標籤，然後在 [ **主機** ] 區段中選取 [ *myHostGroup* ] 作為主機 **群組** ，並選取 [ *myhost 代表* ] 作為 **主機**。 
    ![選取主機群組和主機](./media/dedicated-hosts-portal/advanced.png)
1. 保留其餘預設值，然後在頁面底部選取 [檢閱 + 建立] 按鈕。
1. 當您看到已通過驗證的訊息時，請選取 [ **建立**]。

可能需要幾分鐘的時間才能部署好 VM。

## <a name="create-a-scale-set-preview"></a> (預覽版建立擴展集) 

> [!IMPORTANT]
> 專用主機上的虛擬機器擴展集目前處於公開預覽狀態。
>
> 若要參與預覽，請完成預覽上線問卷 [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) 。
>
> 若要在 Azure 入口網站中流覽預覽功能，您必須使用此 URL： [https://aka.ms/vmssadh](https://aka.ms/vmssadh) 。
>
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 
>
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

當您部署擴展集時，請指定主機群組。

1. 搜尋 *擴展集* ，並從清單中選取 **虛擬機器擴展集** 。
1. 選取 **[新增]** 以建立新的擴展集。
1. 在 [ **基本** ] 索引標籤上完成欄位，但您通常會這麼做，但請確定您選取的 VM 大小來自您為專用主機選擇的系列，例如 **標準 E2s v3**。
1. 在 [ **Advanced** ] 索引標籤上，針對 [ **分散演算法** ] 選取 [ **最大分配**]
1. 在 [ **主機群組**] 中，從下拉式清單中選取主機群組。 如果您最近建立了群組，可能需要幾分鐘的時間才會加入清單。

## <a name="add-an-existing-vm"></a>新增現有的 VM 

您可以將現有的 VM 新增至專用主機，但必須先將 VM Stop\Deallocated。 將 VM 移至專用主機之前，請確定支援 VM 設定：

- VM 大小必須與專用主機位於相同大小的系列中。 例如，如果您的專用主機是 DSv3，則可以 Standard_D4s_v3 VM 大小，但不能是 Standard_A4_v2。 
- VM 必須位於與專用主機相同的區域中。
- VM 不能是鄰近放置群組的一部分。 將 VM 移至專用主機之前，請先從鄰近放置群組移除 VM。 如需詳細資訊，請參閱 [將 VM 移出鄰近位置群組](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM 不能位於可用性設定組中。
- 如果 VM 位於可用性區域中，它必須與主機群組位於相同的可用性區域。 VM 和主機群組的可用性區域設定必須相符。

使用 [入口網站](https://portal.azure.com)將 VM 移至專用主機。

1. 開啟 VM 的頁面。
1. 選取 [ **停止** ] 以 stop\deallocate VM。
1. 從左側功能表 **中選取 [** 設定]。
1. 從下拉式功能表中選取主機群組和主機。
1. 當您完成時，請選取頁面頂端的 [ **儲存** ]。
1. 將 VM 新增至主機之後，請從左側功能表中選取 **[總覽** ]。
1. 在頁面頂端，選取 [ **開始** ] 以重新開機 VM。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱[專用主機](dedicated-hosts.md)概觀。

- [這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有範例範本，範例中使用區域和容錯網域來獲得區域中的最大復原。

- 您也可以使用 [Azure CLI](./linux/dedicated-hosts-cli.md) 或 [PowerShell](./windows/dedicated-hosts-powershell.md)來部署專用主機。
