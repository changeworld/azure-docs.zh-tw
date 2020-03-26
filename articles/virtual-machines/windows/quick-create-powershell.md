---
title: 快速入門 - 使用 Azure PowerShell 建立 Windows VM
description: 在本快速入門中，您會了解如何使用 Azure PowerShell 來建立 Windows 虛擬機器
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 607ad17af42b88737d8ca4ebc379504d9b0b40df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "74073448"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>快速入門：使用 PowerShell 在 Azure 中建立 Windows 虛擬機器

Azure PowerShell 模組用於從 PowerShell 命令列或在指令碼中建立和管理 Azure 資源。 本快速入門說明如何使用 Azure PowerShell 模組，在 Azure 中部署執行 Windows Server 2016 的虛擬機器 (VM)。 您也會以 RDP 連線至 VM 並安裝 IIS 網頁伺服器，以顯示作用中的 VM。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。


## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>建立虛擬機器

使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 建立 VM。 提供每個資源的名稱，而 `New-AzVM` Cmdlet 會建立這些名稱 (如果尚未存在)。

出現提示時，請提供使用者名稱與密碼以作為 VM 的登入認證：

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

部署完成後，以 RDP 連線至 VM。 若要查看作用中的 VM，接著會安裝 IIS 網頁伺服器。

若要查看 VM 的公用 IP 位址，請使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) Cmdlet：

```powershell
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

從您的本機電腦使用下列命令，建立遠端桌面工作階段。 以 VM 的公用 IP 位址取代 IP 位址。 

```powershell
mstsc /v:publicIpAddress
```

在 [Windows 安全性]  視窗中，選取 [更多選擇]  ，然後選取 [使用不同的帳戶]  。 輸入 **localhost**\\*username* 作為使用者名稱，並輸入您為虛擬機器建立的密碼，然後按一下 [確定]  。

您可能會在登入過程中收到憑證警告。 按一下 [是]  或 [繼續]  以建立連線

## <a name="install-web-server"></a>安裝 Web 伺服器

若要查看作用中的 VM，請安裝 IIS 網頁伺服器。 在 VM 上開啟 PowerShell 提示字元並執行下列命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

完成時，關閉與 VM 的 RDP 連線。

## <a name="view-the-web-server-in-action"></a>檢視作用中的網頁伺服器

安裝 IIS 後，現在經由網際網路在您的 VM 上開啟連接埠 80，請使用所選的網頁瀏覽器來檢視預設 IIS 歡迎使用畫面。 使用上一個步驟所取得 VM 的公用 IP 位址。 下列範例示範預設的 IIS 網站：

![IIS 預設網站](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已部署簡單的虛擬機器、對網路流量開啟網路連接埠，以及安裝基本的網頁伺服器。 若要深入了解 Azure 虛擬機器，請繼續 Windows VM 的教學課程。

> [!div class="nextstepaction"]
> [Azure Windows 虛擬機器教學課程](./tutorial-manage-vm.md)
