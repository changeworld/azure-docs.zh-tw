---
title: 使用 Azure 門戶部署 Azure 專用主機
description: 使用 Azure 門戶將 VM 部署到專用主機。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 195a19ef881f235ad8e42f23b53da9e667ef88d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086744"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>使用門戶將 VM 部署到專用主機

本文將指導您如何創建 Azure[專用主機](dedicated-hosts.md)來託管虛擬機器 （VM）。 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>建立 VM

1. 選擇 Azure 入口網站左上角的 [建立資源]****。
1. 在 Azure Marketplace 資源清單上方的搜尋方塊中，依 Canonical 搜尋並選取**Ubuntu Server 16.04 LTS**，然後選擇 [建立]****。
1. 在 **"基本"** 選項卡中，**在"專案詳細資訊**"下，請確保選擇了正確的訂閱，然後選擇*my 專用主機RG*作為**資源組**。 
1. 在 [執行個體詳細資料]**** 底下，輸入 myVM** 作為 [虛擬機器名稱]****，然後選擇 [美國東部]** 作為您的 [位置]****。
1. 在 **"可用性"選項**中，選擇 **"可用性區域**"，從下拉清單中選擇*1。*
1. 對於大小，選擇 **"更改大小**"。 在可用尺寸清單中，從 Esv3 系列中選擇一個，如**標準 E2s v3**。 您可能需要清除篩選器才能查看所有可用大小。
1. 在 [系統管理員帳戶]**** 下方選取 [SSH 公開金鑰]****，輸入您的使用者名稱，然後將您的公開金鑰貼到文字方塊中。 請移除公開金鑰中的任何前置或尾端的空白字元。

    ![系統管理員帳戶](./media/quick-create-portal/administrator-account.png)

1. 在**入站連接埠規則** > **"公共入站埠**"下，選擇 **"允許選定的埠**"，然後從下拉清單中選擇**SSH （22）。** 
1. 在頁面頂部，選擇 **"高級**"選項卡，在 **"主機**"部分中，為**主機組**選擇*myHostGroup，* 為 **"主機**"選擇*myHost*組。 
    ![選擇主機組和主機](./media/dedicated-hosts-portal/advanced.png)
1. 保留其餘預設值，然後在頁面底部選取 [檢閱 + 建立]**** 按鈕。
1. 當您看到驗證已通過的消息時，選擇 **"創建**"。

可能需要幾分鐘的時間才能部署好 VM。

## <a name="add-an-existing-vm"></a>添加現有 VM 

您可以將退出的 VM 添加到專用主機，但 VM 必須首先處於"停止_交易"的位置。 在將 VM 移動到專用主機之前，請確保 VM 配置受支援：

- VM 大小必須與專用主機的大小相同。 例如，如果您的專用主機是 DSv3，則 VM 大小可以Standard_D4s_v3，但它不可能是Standard_A4_v2。 
- VM 需要與專用主機位於同一區域。
- VM 不能是鄰近放置組的一部分。 在將 VM 移動到專用主機之前，請從接近放置組中刪除 VM。 有關詳細資訊，請參閱將[VM 移出鄰近放置組](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM 不能位於可用性集中。
- 如果 VM 位於可用性區域中，則它必須與主機組位於同一可用性區域。 VM 和主機組的可用性地區設定必須匹配。

使用[門戶](https://portal.azure.com)將 VM 移動到專用主機。

1. 打開 VM 的頁面。
1. 選擇 **"停止"** 以停止\取消分配 VM。
1. 從左側功能表中選擇 **"配置**"。
1. 從下拉式功能表中選擇主機組和主機。
1. 完成後，選擇"在頁面頂部**保存**"。
1. 將 VM 添加到主機後，從左側功能表中選擇 **"概述**"。
1. 在頁面頂部，選擇 **"開始"** 以重新開機 VM。

## <a name="next-steps"></a>後續步驟

- 有關詳細資訊，請參閱[專用主機](dedicated-hosts.md)概述。

- [此處](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的示例範本，它同時使用區域和容錯域，以實現區域中的最大恢復能力。

- 您還可以使用[Azure CLI](dedicated-hosts-cli.md)部署專用主機。



