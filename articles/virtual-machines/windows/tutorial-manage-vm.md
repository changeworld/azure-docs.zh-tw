---
title: 教學課程 - 使用 Azure PowerShell 建立和管理 Windows VM
description: 在本教學課程中，您將了解如何使用 Azure PowerShell 在 Azure 中建立和管理 Windows VM
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 3e52a808b187e3823acfee2c260986518f2f6f49
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977998"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>教學課程：使用 Azure PowerShell 建立和管理 Windows VM

Azure 虛擬機器提供完全可設定且彈性的計算環境。 本教學課程將說明基本的 Azure 虛擬機器 (VM) 部署工作，例如選取 VM 大小、選取 VM 映像、部署 VM。 您會了解如何：

> [!div class="checklist"]
> * 建立及連線到 VM
> * 選取及使用 VM 映像
> * 檢視及使用特定 VM 大小
> * 調整 VM 的大小
> * 檢視及了解 VM 狀態

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立資源群組。

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 資源群組必須在虛擬機器之前建立。 在下列範例中，會在 *EastUS* 區域中建立名為 *myResourceGroupVM* 的資源群組：

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

在建立或修改 VM 時，會指定資源群組，在本教學課程的整個過程中可以看到此操作。

## <a name="create-a-vm"></a>建立 VM

建立 VM 時，有數個選項可供使用，例如作業系統映像、網路組態和系統管理認證。 此範例會執行 Windows Server 2016 Datacenter 的預設版本，建立名為 *myVM* 的 VM。

使用 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-6) 來設定 VM 上系統管理員帳戶所需的使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential
```

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 建立 VM。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>連接到 VM

完成部署之後，請建立 VM 的遠端桌面連線。

執行下列命令，以傳回 VM 的公用 IP 位址。 記下這個 IP 位址，您便可以使用瀏覽器進行連線，以在未來步驟中測試 Web 連線能力。

```azurepowershell-interactive
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

在本機電腦上使用下列命令，建立 VM 的遠端桌面工作階段。 請將 IP 位址取代為 VM 的 publicIPAddress  。 出現提示時，請輸入您在建立 VM 時所使用的認證。

```powershell
mstsc /v:<publicIpAddress>
```

在 [Windows 安全性]  視窗中，選取 [更多選擇]  ，然後選取 [使用不同的帳戶]  。 輸入您為 VM 建立的使用者名稱和密碼，然後按一下 [確定]  。

## <a name="understand-marketplace-images"></a>了解 Marketplace 映像

Azure Marketplace 包含許多可用來建立新 VM 的映像。 在先前步驟中，已使用 Windows Server 2016 Datacenter 映像建立 VM。 在此步驟中，PowerShell 模組用來搜尋 Marketplace 中的其他 Windows 映像，其也可用來作為新 VM 的基底。 這個程序包含尋找發行者、供應項目、SKU 和版本號碼 (選擇性) 來[識別](cli-ps-findimage.md#terminology)映像。

使用 [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) 命令傳回映像發行者清單：

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

使用 [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) 傳回映像提供清單。 使用此命令時，會根據名為 `MicrosoftWindowsServer` 的指定發行者篩選傳回的清單：

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

結果將如下列範例所示： 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) 命令會接著根據發行者和供應項目名稱篩選，以傳回映像名稱清單。

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

結果將如下列範例所示： 

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

此資訊可用來以特定映像部署 VM。 此範例會使用最新版的 Windows Server 2016 with Containers 映像部署 VM。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

`-AsJob` 參數會以背景工作建立 VM，因此會傳回 PowerShell 提示。 您可以使用 `Get-Job` Cmdlet 檢視背景作業的詳細資料。

## <a name="understand-vm-sizes"></a>了解 VM 大小

VM 大小會決定可供 VM 使用的計算資源 (例如 CPU、GPU 和記憶體) 數量。 虛擬機器必須適用於工作負載的 VM 大小來建立。 如果工作負載增加，可以調整現有虛擬機器的大小。

### <a name="vm-sizes"></a>VM 大小

下表會將大小分類成各種使用案例。  

| 類型                     | 一般大小           |    描述       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [一般用途](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| 平衡的 CPU 對記憶體。 適用於開發/測試及小型到中型應用程式和資料解決方案。  |
| [計算最佳化](../sizes-compute.md)   | Fsv2          | CPU 與記憶體的比例高。 適用於中流量應用程式、網路設備，以及批次處理。        |
| [記憶體最佳化](../sizes-memory.md)    | Esv3、Ev3、M、DSv2、Dv2  | 記憶體與核心的比例高。 適用於關聯式資料庫、中型到大型快取，以及記憶體內分析。                 |
| [儲存體最佳化](../sizes-storage.md)      | Lsv2、Ls              | 高磁碟輸送量及 IO。 適用於巨量資料、SQL 及 NoSQL 資料庫。                                                         |
| [GPU](../sizes-gpu.md)          | NV、NVv2、NC、NCv2、NCv3、ND            | 以大量圖形轉譯和視訊編輯為目標的特製化 VM。       |
| [高效能](../sizes-hpc.md) | H        | 我們的最強大 CPU VM，可搭配選用的高輸送量網路介面 (RDMA)。 |

### <a name="find-available-vm-sizes"></a>尋找可用的 VM 大小

若要查看特定區域中可用的 VM 大小清單，請使用 [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) 命令。

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>調整 VM 的大小

在部署 VM 之後，可以調整其大小以增加或減少資源配置。

在調整 VM 的大小之前，請檢查目前的 VM 叢集上是否有您所要的大小。 [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) 命令會傳回大小清單。

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

如果此大小可用，即可從已開機狀態調整 VM 的大小，但是會在作業期間重新開機。

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

如果目前的叢集上沒有您所需的大小，則必須先將 VM 解除配置，才能進行調整大小的作業。 將 VM 解除配置後，暫存磁碟上的所有資料都會移除，且公用 IP 位址將會變更，除非正在使用靜態 IP 位址。

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>VM 電源狀態

Azure VM 的電源狀態可以是許多電源狀態的其中一種。 


| 電源狀態 | 描述
|----|----|
| 啟動中 | 虛擬機器正在啟動。 |
| 執行中 | 虛擬機器未執行。 |
| 停止中 | 虛擬機器正在停止。 |
| 已停止 | VM 已停止。 處於已停止狀態的虛擬機器仍然會產生運算費用。  |
| 正在解除配置 | VM 正在解除配置。 |
| 已解除配置 | 表示 VM 已從 Hypervisor 中移除，但仍可在控制平面中使用。 處於 `Deallocated` 狀態的虛擬機器不會產生計算費用。 |
| - | VM 的電源狀態不明。 |


若要取得特定 VM 的狀態，請使用 [Get-AzVM](/powershell/module/az.compute/get-azvm) 命令。 請務必為 VM 和資源群組指定有效的名稱。

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

輸出會如下列範例所示：

```powershell
Status
------
PowerState/running
```

若要取得訂用帳戶中所有 VM 的電源狀態，請使用[虛擬機器 - 列出所有 API](/rest/api/compute/virtualmachines/listall)，並將參數 **statusOnly** 設定為 True。

## <a name="management-tasks"></a>管理工作

在 VM 的生命週期內，您可能會執行一些管理工作，例如啟動、停止或刪除 VM。 此外，您可以建立指令碼來自動執行重複或複雜的工作。 使用 Azure PowerShell，可以從命令列或在指令碼中執行許多常見的管理工作。

### <a name="stop-a-vm"></a>停止 VM

使用 [Stop-AzVM](/powershell/module/az.compute/stop-azvm) 停止及解除配置 VM：

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

如果您想要將 VM 保留在佈建狀態，請使用 -StayProvisioned 參數。

### <a name="start-a-vm"></a>啟動 VM

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>刪除資源群組

當您刪除資源群組時，會刪除資源群組內的所有項目。

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解基本的 VM 建立和管理，像是如何：

> [!div class="checklist"]
> * 建立及連線到 VM
> * 選取及使用 VM 映像
> * 檢視及使用特定 VM 大小
> * 調整 VM 的大小
> * 檢視及了解 VM 狀態

請前進到下一個教學課程，以了解 VM 磁碟。  

> [!div class="nextstepaction"]
> [建立和管理 VM 磁碟](./tutorial-manage-data-disk.md)