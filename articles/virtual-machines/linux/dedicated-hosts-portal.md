---
title: 使用 Azure 入口網站部署 Azure 專用主機
description: 使用 Azure 入口網站將 Vm 部署到專用主機。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 195a19ef881f235ad8e42f23b53da9e667ef88d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79086744"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>使用入口網站將 Vm 部署至專用主機

本文會引導您瞭解如何建立 Azure[專用主機](dedicated-hosts.md)來裝載您的虛擬機器（vm）。 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>建立 VM

1. 選擇 Azure 入口網站左上角的 [建立資源]****。
1. 在 Azure Marketplace 資源清單上方的搜尋方塊中，依 Canonical 搜尋並選取**Ubuntu Server 16.04 LTS**，然後選擇 [建立]****。
1. 在 [**基本**] 索引標籤的 [**專案詳細資料**] 底下，確認已選取正確的訂用帳戶，然後選取 [ *myDedicatedHostsRG* ] 作為**資源群組**。 
1. 在 [執行個體詳細資料]**** 底下，輸入 myVM** 作為 [虛擬機器名稱]****，然後選擇 [美國東部]** 作為您的 [位置]****。
1. 在 [**可用性選項**] 中，選取 [**可用性區域**]，從下拉式選單選取*1* 。
1. 針對 [大小]，選取 [**變更大小**]。 在可用大小清單中，從 Esv3 系列選擇一個，例如 [**標準 E2s v3**]。 您可能需要清除篩選準則，才能看到所有可用的大小。
1. 在 [系統管理員帳戶]**** 下方選取 [SSH 公開金鑰]****，輸入您的使用者名稱，然後將您的公開金鑰貼到文字方塊中。 請移除公開金鑰中的任何前置或尾端的空白字元。

    ![系統管理員帳戶](./media/quick-create-portal/administrator-account.png)

1. 在 [**輸入連接埠規則** > ] [**公用輸入埠**] 底下，選擇 [**允許選取的埠**]，然後從下拉式選單選取 [ **SSH （22）** ]。 
1. 在頁面頂端，選取 [ **Advanced** ] 索引標籤，然後在 [**主機**] 區段中，針對 [**主機群組**] 和 [ *myhost 代表*]**選取 [** *myHostGroup* ]。 
    ![選取主機群組和主機](./media/dedicated-hosts-portal/advanced.png)
1. 保留其餘預設值，然後在頁面底部選取 [檢閱 + 建立]  按鈕。
1. 當您看到驗證已通過的訊息時，請選取 [**建立**]。

可能需要幾分鐘的時間才能部署好 VM。

## <a name="add-an-existing-vm"></a>新增現有的 VM 

您可以將現有的 VM 新增至專用主機，但必須先 Stop\Deallocated. VM 將 VM 移至專用主機之前，請確定已支援 VM 設定：

- VM 大小必須與專用主機位於相同大小的系列。 例如，如果您的專用主機已 DSv3，則 VM 大小可能會 Standard_D4s_v3，但不能是 Standard_A4_v2。 
- VM 必須位於與專用主機相同的區域中。
- VM 不能是鄰近放置群組的一部分。 將 VM 移至專用主機之前，請先從鄰近放置群組中移除它。 如需詳細資訊，請參閱[將 VM 移出鄰近位置群組](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM 不能位於可用性設定組中。
- 如果 VM 位於可用性區域中，它必須與主機群組位於相同的可用性區域。 VM 和主機群組的可用性區域設定必須相符。

使用[入口網站](https://portal.azure.com)將 VM 移至專用主機。

1. 開啟 VM 的頁面。
1. 選取 [**停止**] 以 stop\deallocate VM。
1. 從左側功能表**中選取 [** 設定]。
1. 從下拉式功能表中選取主機群組和主機。
1. 當您完成時，請選取頁面頂端的 [**儲存**]。
1. 將 VM 新增至主機之後，請從左側功能表中選取 **[總覽**]。
1. 在頁面頂端，選取 [**啟動**] 以重新開機 VM。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱[專用主機](dedicated-hosts.md)總覽。

- [這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有範例範本，它會使用區域和容錯網域來取得區域中的最大復原。

- 您也可以使用[Azure CLI](dedicated-hosts-cli.md)部署專用主機。



