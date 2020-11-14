---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a8d1b27fc040e6aed0bdeeb86b2e6c4df13f87c3
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "92540887"
---
您可以建立 VM 的遠端桌面連線，以連線至已部署至 VNet 的 VM。 一開始確認您可以連線至 VM 的最佳方法是使用其私人 IP 位址 (而不是電腦名稱) 進行連線。 這樣一來，您會測試以查看您是否可以連線，而不是否已正確設定名稱解析。

1. 找出私人 IP 位址。 在 Azure 入口網站中或使用 PowerShell 查看 VM 的屬性，即可找到 VM 的私人 IP 位址。

   * Azure 入口網站 - 在 Azure 入口網站中尋找您的虛擬機器。 檢視 VM 的屬性。 系統會列出私人 IP 位址。

   * PowerShell - 使用範例來檢視資源群組中的 VM 和私人 IP 位址清單。 使用此範例前，您不需要加以修改。

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

1. 確認您已使用點對站 VPN 連線來連線至 VNet。
1. 在工作列上的搜尋方塊中輸入「RDP」或「遠端桌面連線」以開啟遠端桌面連線，然後選取 [遠端桌面連線]。 您也可以使用 PowerShell 中的 'mstsc' 命令開啟遠端桌面連線。 
1. 在 [遠端桌面連線] 中，輸入 VM 的私人 IP 位址。 您可以按一下 [顯示選項] 來調整其他設定，然後進行連線。

**疑難排解連接**

如果您無法透過 VPN 連線與虛擬機器連線，請檢查下列各項：

* 確認您的 VPN 連線成功。

* 確認您是連線至 VM 的私人 IP 位址。

* 如果您可以使用私人 IP 位址 (而非電腦名稱) 來連線至 VM，請確認您已正確設定 DNS。 如需 VM 的名稱解析運作方式的詳細資訊，請參閱 [VM 的名稱解析](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

* 如需 RDP 連線的詳細資訊，請參閱[針對 VM 的遠端桌面連線進行疑難排解](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)。