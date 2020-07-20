---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40ae634897361219c39e60d2161d3576cc44a400
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077530"
---
若要快速建立 VNet，您可以按一下本文的 [試試看]，以在 Azure Cloud Shell 中開啟 PowerShell 主控台。 調整值，然後複製命令並貼到主控台視窗。 

請務必確認您所建立的 VNet 位址空間不會與任何其他想要連線的 Vnet 位址範圍重疊，或是不會與內部部署網路的位址空間重疊。

### <a name="create-a-resource-group"></a>建立資源群組

如果您還未擁有想要使用的資源群組，請建立一個新的。 調整 PowerShell 命令以反映您想要使用的資源群組名稱，然後執行下列 Cmdlet：

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>建立 VNet

調整 PowerShell 命令，以建立適用於您環境的 VNet。

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```
