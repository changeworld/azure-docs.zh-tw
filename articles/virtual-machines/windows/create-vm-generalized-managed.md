---
title: 在 Azure 中從受控映射建立 VM
description: 使用 Azure PowerShell 或入口網站，從一般化受控映射建立 Windows 虛擬機器。
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: b0c6689b66037067a4c5174738945b7c6fabd5b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086312"
---
# <a name="create-a-vm-from-a-managed-image"></a>從受控映像建立 VM

您可以使用 Azure 入口網站或 PowerShell，從 Azure 受控 VM 映像建立多部虛擬機器 (VM)。 受控 VM 映像包含建立 VM 所需的資訊，包括 OS 和資料磁碟。 組成映像的虛擬硬碟 (VHD) (包括 OS 磁碟和任何資料磁碟) 會儲存為受控磁碟。 

在建立新的 VM 之前，您必須[建立受控 vm 映射](capture-image-resource.md)以作為來源映射，並將映射的讀取權限授與任何應具有映射存取權的使用者。 


## <a name="use-the-portal"></a>使用入口網站

1. 移至[Azure 入口網站](https://portal.azure.com)以尋找受控映射。 搜尋並選取 [**映射**]。
3. 從清單選取您要使用的映像。 映像的 [概觀]**** 頁面隨即開啟。
4. 從功能表選取 [建立 VM]****。
5. 輸入虛擬機器資訊。 在此輸入的使用者名稱和密碼將用於登入虛擬機器。 完成時選取 [確定]****。 您可以在現有資源群組中建立新的 VM，或選擇 [建立新項目]**** 來建立用於儲存 VM 的新資源群組。
6. 選取 VM 的大小。 若要查看更多大小，請選取 [**全部查看**] 或 [變更**支援的磁片類型**] 篩選器。 
7. 在 [設定]**** 下，視需要變更，然後選取 [確定]****。 
8. 在摘要頁面上，您應該會看到您的映像名稱列出為**私人映像**。 選取 [確定]**** 以開始虛擬機器部署。


## <a name="use-powershell"></a>使用 PowerShell

您可以使用 PowerShell，透過針對 [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Cmdlet 設定的簡化參數從映像建立 VM。 映像必須位在與您將要建立 VM 映像的同一個資源群組中。

 

針對 [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 設定的簡化參數只會要求您提供名稱、資源群組和映像名稱，以便從映像建立 VM。 New-AzVm 會使用 **-Name** 參數的值作為自動建立的所有資源名稱。 在此範例中，會對每個資源提供更詳細的名稱，但會讓 Cmdlet 自動建立這些資源。 您也可以事先建立資源 (例如虛擬網路)，並將資源名稱傳遞至 Cmdlet。 如果可藉由名稱找到資源，New-AzVm 即會使用現有的資源。

下列範例會在 *myResourceGroup* 資源群組中從名稱為 *myImage* 的映像建立名稱為 *myVMFromImage* 的 VM。 


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



## <a name="next-steps"></a>後續步驟
[使用 Azure PowerShell 模組來建立和管理 Windows Vm](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

