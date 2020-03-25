---
title: 教學課程 - 使用 Azure PowerShell 建立自訂的 VM 映像
description: 在本教學課程中，您會了解如何使用 Azure PowerShell 在 Azure 中建立自訂虛擬機器映像
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7360798f2f95184145a856babf501e3080cbaaf4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76274195"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>教學課程：使用 Azure PowerShell 建立 Azure VM 的自訂映像

自訂映像類似 Marketplace 映像，但您要自行建立它們。 您可使用自訂映像來啟動部署，並確保多個 VM 之間的一致性。 在本教學課程中，您將使用 PowerShell 建立自己的 Azure 虛擬機器自訂映像。 您會了解如何：

> [!div class="checklist"]
> * 執行 sysprep 及一般化 VM
> * 建立自訂映像
> * 從自訂映像建立 VM
> * 列出訂用帳戶中的所有映像
> * 删除映像

在公開預覽中，我們有 [Azure VM映像產生器](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview)服務。 只要在範本中描述您的自訂，其就會處理本文中的映像建立步驟。 [嘗試 Azure 映像產生器 (預覽)](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder)。

## <a name="before-you-begin"></a>開始之前

下列步驟將詳細說明如何將現有 VM 轉換成可重複使用的自訂映像，以便讓您用來建立新的 VM 執行個體。

若要完成本教學課程中的範例，您目前必須具有虛擬機器。 如有需要，這個[指令碼範例](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)可以為您建立一部虛擬機器。 逐步完成教學課程之後，請視需要取代資源群組和 VM 名稱。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="prepare-vm"></a>準備 VM

若要建立虛擬機器的映像，您需要將來源 VM 一般化、解除配置，然後使用 Azure 將來源 VM 標示為一般化，藉此準備來源 VM。

### <a name="generalize-the-windows-vm-using-sysprep"></a>使用 Sysprep 將 Windows VM 一般化

Sysprep 會移除您的所有個人帳戶資訊以及其他項目，並準備電腦以做為映像。 如需 Sysprep 的詳細資訊，請參閱 [如何使用 Sysprep：簡介](https://technet.microsoft.com/library/bb457073.aspx)。


1. 連接至虛擬機器。
2. 以系統管理員身分開啟 [命令提示字元] 視窗。 切換至 *%windir%\system32\sysprep* 目錄，然後執行 `sysprep.exe`。
3. 在 [系統準備工具]  對話方塊中，選取 [進入系統全新體驗 (OOBE)]  ，並確認已勾選 [一般化]  核取方塊。
4. 在 [關機選項]  中選取 [關機]  ，然後按一下 [確定]  。
5. Sysprep 完成時，會關閉虛擬機器。 **不要重新啟動 VM**。

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>解除配置並將 VM 標示為一般化

若要建立映像，必須將 VM 解除配置並在 Azure 中標示為一般化。

使用 [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) \(英文\) 將 VM 解除配置。

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

使用 `-Generalized`Set-AzVm[ 將虛擬機器的狀態設定為 ](https://docs.microsoft.com/powershell/module/az.compute/set-azvm)。 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>建立映像

現在您可以使用 [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) 和 [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage) 建立 VM 的映象。 下列範例會從名為 myVM  的 VM 建立名為 myImage  的映像。

取得虛擬機器。 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

建立映像組態。

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

建立映像。

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>從映像建立 VM

現在您已有映像，您可以從映像建立一個或多個新的 VM。 從自訂映像建立 VM 類似於使用 Marketplace 映像建立 VM。 使用 Marketplace 映像時，您必須提供關於映像、映像提供者、供應項目、SKU 和版本的資訊。 使用針對 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Cmdlet 簡化的參數集時，只要自訂映像位在同一個資源群組，您就只需要提供自訂映像的名稱。 

該範例會根據 *myResourceGroup* 中的 *myImage* 映像建立名為 *myVMfromImage* 的 VM。


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

我們建議您將來自單一映像的並行部署數目限制為 20 部 VM。 如果您打算從相同的自訂映像大規模、並行部署超過 20 部 VM，則應使用具有多個映像複本的[共用映像庫](shared-image-galleries.md)。 


## <a name="image-management"></a>映像管理 

以下範例是一些常見的映像管理作業，以及如何使用 PowerShell 完成這些作業。

依名稱列出所有映像。

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

删除映像。 此範例會刪除 myResourceGroup  中名為 myImage  的映像。

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>後續步驟

您在本教學課程中建立了自訂 VM 映像。 您已了解如何︰

> [!div class="checklist"]
> * 執行 sysprep 及一般化 VM
> * 建立自訂映像
> * 從自訂映像建立 VM
> * 列出訂用帳戶中的所有映像
> * 删除映像

請前進到下一個教學課程，以了解如何建立高可用性的虛擬機器。

> [!div class="nextstepaction"]
> [建立高可用性 VM](tutorial-availability-sets.md)



