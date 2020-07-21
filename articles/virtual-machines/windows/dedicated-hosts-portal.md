---
title: 使用入口網站部署 Azure 專用主機
description: 使用入口網站將 Vm 部署至專用主機。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 444f7cd7094a688c43d9aedce67d293e3c32e273
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508554"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>使用入口網站將 Vm 部署至專用主機

本文會引導您瞭解如何建立 Azure [專用主機](dedicated-hosts.md)來裝載您的虛擬機器 (VM)。 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>建立 VM

1. 選擇 Azure 入口網站左上角的 [建立資源]。
1. 在 [**新增**] 頁面的 [**熱門**] 底下，選取 [ **Windows Server 2016 Datacenter**]。
1. 在 [**基本**] 索引標籤的 [**專案詳細資料**] 底下，確認已選取正確的訂用帳戶，然後選取 [ *myDedicatedHostsRG* ] 作為**資源群組**。 
1. 在 [執行個體詳細資料]**** 底下，輸入 myVM** 作為 [虛擬機器名稱]****，然後選擇 [美國東部]** 作為您的 [位置]****。
1. 在 [**可用性選項**] 中，選取 [**可用性區域**]，從下拉式選單選取*1* 。
1. 針對 [大小]，選取 [**變更大小**]。 在可用大小清單中，從 Esv3 系列選擇一個，例如 [**標準 E2s v3**]。 您可能需要清除篩選準則，才能看到所有可用的大小。
1. 在 [系統管理員帳戶]  底下提供使用者名稱 (例如 azureuser  ) 和密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](faq.md#what-are-the-password-requirements-when-creating-a-vm)。
1. 在 [輸入連接埠規則] 下方選擇 [允許選取的連接埠]，然後從下拉式清單中選取 [RDP (3389)]。
1. 在頁面頂端，選取 [ **Advanced** ] 索引標籤，然後在 [**主機**] 區段中，針對 [**主機群組**] 和 [ *myhost 代表*]**選取 [** *myHostGroup* ]。 
    ![選取主機群組和主機](./media/dedicated-hosts-portal/advanced.png)
1. 保留其餘預設值，然後在頁面底部選取 [檢閱 + 建立] 按鈕。
1. 當您看到驗證已通過的訊息時，請選取 [**建立**]。

## <a name="add-an-existing-vm"></a>新增現有的 VM 

您可以將現有的 VM 新增至專用主機，但必須先 Stop\Deallocated. VM 將 VM 移至專用主機之前，請確定已支援 VM 設定：

- VM 大小必須與專用主機位於相同大小的系列。 例如，如果您的專用主機已 DSv3，則 VM 大小可能會 Standard_D4s_v3，但不能是 Standard_A4_v2。 
- VM 必須位於與專用主機相同的區域中。
- VM 不能是鄰近放置群組的一部分。 將 VM 移至專用主機之前，請先從鄰近放置群組中移除它。 如需詳細資訊，請參閱[將 VM 移出鄰近位置群組](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
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

- 如需詳細資訊，請參閱[專用主機](dedicated-hosts.md)概觀。 

- [這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有範例範本，範例中使用區域和容錯網域來獲得區域中的最大復原。

- 您也可以使用[Azure PowerShell](dedicated-hosts-powershell.md)部署專用主機。
