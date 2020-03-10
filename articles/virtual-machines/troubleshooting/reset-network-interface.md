---
title: 如何重設 Azure Windows VM 的網路介面| Microsoft Docs
description: 示範如何重設 Azure Windows VM 的網路介面
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: a8bd12d98b76d5848753987c4f7bcb76d4e2266d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391073"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>如何重設 Azure Windows VM 的網路介面 

本文說明如何重設 Azure Windows VM 的網路介面，以在下列事項之後，解決無法連線到 Microsoft Azure Windows 虛擬機器 (VM) 時的問題：

* 您停用預設網路介面 (NIC)。 
* 您手動設定 NIC 的靜態 IP。 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>重設網路介面

### <a name="for-vms-deployed-in-resource-group-model"></a>適用於部署在資源群組模型中的 VM

1.  移至 [Azure 入口網站](https://ms.portal.azure.com)。
2.  選取受影響的虛擬機器。
3.  選取 [網絡]，然後選取 VM 的網路介面。

    ![網路介面位置](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  選取 [IP 組態]。
5.  選取 IP。 
6.  如果 [私人 IP 指派] 不是 [靜態]，請將它變更為 [靜態]。
7.  將 [IP 位址] 變更為子網路中可用的其他 IP 位址。
8. 虛擬機器會重新啟動，以便對系統初始化新的 NIC。
9.  嘗試使用 RDP 連線到您的電腦。 如果成功，您可以自行決定是否將私人 IP 位址變更回原始位址， 或是維持現有設定。 

#### <a name="use-azure-powershell"></a>使用 Azure PowerShell

1. 確定您已安裝[最新的 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
2. 開啟已提高權限的 Azure PowerShell 工作階段 (以系統管理員的身分執行)。 執行下列命令：

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. 嘗試使用 RDP 連線到您的電腦。  如果成功，您可以自行決定是否將私人 IP 位址變更回原始位址， 或是維持現有設定。

### <a name="for-classic-vms"></a>適用於傳統 VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

若要重設網路介面，請按照以下步驟操作：

#### <a name="use-azure-portal"></a>使用 Azure 入口網站

1.  移至 [Azure 入口網站]( https://ms.portal.azure.com)。
2.  選取 [虛擬機器 (傳統)]。
3.  選取受影響的虛擬機器。
4.  選取 [IP 位址]。
5.  如果 [私人 IP 指派] 不是 [靜態]，請將它變更為 [靜態]。
6.  將 [IP 位址] 變更為子網路中可用的其他 IP 位址。
7.  選取 [儲存]。
8.  虛擬機器會重新啟動，以便對系統初始化新的 NIC。
9.  嘗試使用 RDP 連線到您的電腦。 如果成功，您可以選擇將私人 IP 位址還原回原始位址。  

#### <a name="use-azure-powershell"></a>使用 Azure PowerShell

1. 確定您已安裝[最新的 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
2. 開啟已提高權限的 Azure PowerShell 工作階段 (以系統管理員的身分執行)。 執行下列命令：

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. 嘗試使用 RDP 連線到您的電腦。 如果成功，您可以自行決定是否將私人 IP 位址變更回原始位址， 或是維持現有設定。 

## <a name="delete-the-unavailable-nics"></a>刪除無法使用的 NIC
可以使用遠端桌面連線到機器之後，您必須刪除舊的 NIC 以避免潛在的問題：

1.  開啟 [裝置管理員]。
2.  選取 [檢視] > [顯示隱藏的裝置]。
3.  選取 [網路介面卡]。 
4.  檢查名稱為「Microsoft Hyper-V 網路介面卡」的介面卡。
5.  您可能會看到顯示為灰色的無法使用介面卡。在介面卡上按一下滑鼠右鍵，然後選取 [卸載]。

    ![NIC 的影像](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > 僅解除安裝無法使用且名稱為「Microsoft Hyper-V 網路介面卡」的介面卡。 如果您解除安裝任何其他隱藏的介面卡，可能會導致其他的問題。
    >
    >

6.  現在應該已經從系統清除所有無法使用的介面卡。
