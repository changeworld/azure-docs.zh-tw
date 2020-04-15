---
title: 教學課程 - Azure 中 Windows VM 的高可用性
description: 在本教學課程中，您會了解如何使用 Azure PowerShell 在可用性設定組中部署高可用性的虛擬機器
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 59bf06d2b279bad792bdc42a7c3b6acc2bc304b8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985706"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>教學課程：使用 Azure PowerShell 建立及部署高可用性的虛擬機器

在本教學課程中，您可了解如何使用「可用性設定組」，增加虛擬機器 (VM) 的可用性和可靠性。 「可用性設定組」可確保您在 Azure 上部署的 VM 會分散到叢集中多個各自獨立的硬體節點。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立可用性設定組
> * 在可用性設定組中建立 VM
> * 檢查可用的 VM 大小
> * 檢查 Azure Advisor


## <a name="availability-set-overview"></a>可用性設定組概觀

可用性設定組是讓 VM 資源在部署時彼此隔離的邏輯群組功能。 Azure 可確保您置於可用性設定組內的 VM 會橫跨多部實體伺服器、計算機架、儲存體單位和網路交換器執行。 如果 Azure 發生硬體或軟體故障，則只有一部分的 VM 會受影響，您的整體解決方案會維持正常運作。 可用性設定組是建置可靠雲端解決方案時不可或缺的功能。

請設想典型的 VM 型解決方案，在此解決方案中，您可能有 4 部前端 Web 伺服器，以及 2 部後端 VM。 使用 Azure 時，建議您先定義兩個可用性設定組，再部署 VM︰一個適用於 Web 層，另一個適用於後端層。 當您建立新的 VM 時，您會將可用性設定組指定為參數。 Azure 可確保多個實體硬體資源中的 VM 彼此隔離。 如果用來執行其中一部 Web 伺服器的實體硬體發生問題，您伺服器的其他執行個體會繼續執行，因為它們位於不同硬體上。

如果您想要在 Azure 內部署可靠的 VM 架構解決方案，請使用可用性設定組。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="create-an-availability-set"></a>建立可用性設定組

位置中的硬體已分為多個更新網域和容錯網域。 **更新網域**是一組虛擬機器和可同時重新啟動的基礎實體硬體。 相同**容錯網域**中的 VM 會共用一般儲存體以及通用電源和網路交換器。  

您可以使用 [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) 建立可用性設定組。 在此範例中，更新和容錯網域數目為 2  ，而 可用性設定組會命名為 myAvailabilitySet  。

建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

使用 [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) 搭配 `-sku aligned` 參數建立受控的可用性設定組。

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>建立位於可用性設定組內的 VM
您必須將 VM 建立於可用性設定組內，才能確保 VM 會在硬體中正確地分散。 您無法在建立可用性設定組之後，將現有的 VM 新增到其中。 


您在透過 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 建立 VM 時，會使用 `-AvailabilitySetName` 參數來指定可用性設定組的名稱。

首先，使用 [Get-credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 設定 VM 的系統管理員使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential
```

現在，請使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 在可用性設定組中建立兩個 VM。

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

建立及設定這兩部 VM 需要幾分鐘的時間。 完成後，您會有兩個分散於基礎硬體上的虛擬機器。 

如果您在入口網站中移至 [資源群組]   > [myResourceGroupAvailability]   > [myAvailabilitySet]  來查看可用性設定組，您應會看到 VM 分散在 2 個容錯和更新網域上。

![入口網站中的可用性設定組](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>檢查可用的 VM 大小 

在可用性設定組中建立 VM 時，需要知道硬體上有哪些可用的 VM 大小。 使用 [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) 命令，取得可在可用性設定組中部署之虛擬機器的所有可用大小。

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>檢查 Azure Advisor 

您也可以使用 Azure Advisor，取得改善 VM 可用性方式的詳細資訊。 Azure Advisor 可分析您的組態和使用量遙測，然後建議可協助您改善 Azure 資源的成本效益、效能、可用性和安全性的解決方案。

登入 [Azure 入口網站](https://portal.azure.com)，選取 [所有服務]  ，然後輸入 **Advisor**。 Advisor 儀表板會顯示所選取訂用帳戶的個人化建議。 如需詳細資訊，請參閱[開始使用 Azure Advisor](../../advisor/advisor-get-started.md)。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立可用性設定組
> * 在可用性設定組中建立 VM
> * 檢查可用的 VM 大小
> * 檢查 Azure Advisor

請前進到下一個教學課程，以了解虛擬機器擴展集。

> [!div class="nextstepaction"]
> [建立 VM 擴展集](tutorial-create-vmss.md)


