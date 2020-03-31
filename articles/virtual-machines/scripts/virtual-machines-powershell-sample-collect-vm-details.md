---
title: 使用 PowerShell 收集有關訂閱中所有 VM 的詳細資訊
description: 使用 PowerShell 收集有關訂閱中所有 VM 的詳細資訊
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: dcscontentpm
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: 237081380445f2b2e4168ee3afe9a3ed7544fc89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900209"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>使用 PowerShell 收集有關訂閱中所有 VM 的詳細資訊

此腳本創建一個 csv，其中包含提供的訂閱中 VM 名稱、資源組名稱、區域、虛擬網路、子網、私人 IP 位址、作業系統類型和公共 IP 位址。

如果沒有[Azure 訂閱](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請先創建[一個免費帳戶](https://azure.microsoft.com/free)。然後開始。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]**** 即可。 您還可以通過 訪問[https://shell.azure.com/powershell](https://shell.azure.com/powershell)在單獨的瀏覽器選項卡中啟動雲外殼。 選擇 **"複製"** 以複製代碼塊，將其粘貼到雲外殼中，然後按 Enter 以運行它。

## <a name="sample-script"></a>範例指令碼

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

#Provide the name of the csv file to be exported
$reportName = "myReport.csv"

Select-AzSubscription $subscriptionId
$report = @()
$vms = Get-AzVM
$publicIps = Get-AzPublicIpAddress 
$nics = Get-AzNetworkInterface | ?{ $_.VirtualMachine -NE $null} 
foreach ($nic in $nics) { 
    $info = "" | Select VmName, ResourceGroupName, Region, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
    $vm = $vms | ? -Property Id -eq $nic.VirtualMachine.id 
    foreach($publicIp in $publicIps) { 
        if($nic.IpConfigurations.id -eq $publicIp.ipconfiguration.Id) {
            $info.PublicIPAddress = $publicIp.ipaddress
            } 
        } 
        $info.OsType = $vm.StorageProfile.OsDisk.OsType 
        $info.VMName = $vm.Name 
        $info.ResourceGroupName = $vm.ResourceGroupName 
        $info.Region = $vm.Location 
        $info.VirturalNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>指令碼說明
此腳本使用以下命令創建訂閱中 VM 詳細資訊的 csv 匯出。 下表中的每個命令都會連結至命令特定的文件。

|Command|注意|
|-|-|
|[選擇-訂閱](https://docs.microsoft.com/powershell/module/Az.Accounts/Set-AzContext)|設置租戶、訂閱和環境，供 Cmdlet 在當前會話中使用。|
|[Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM)|取得虛擬機器的屬性。|
|[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzPublicIpAddress)|獲取公共 IP 位址。|
|[獲取阿茲網路介面](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzNetworkInterface)|獲取網路介面。|

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/azure/overview)。

您可以在 [Azure Windows VM 文件](https://docs.microsoft.com/azure/virtual-machines/windows/powershell-samples?toc=/azure/virtual-machines/windows/toc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。


