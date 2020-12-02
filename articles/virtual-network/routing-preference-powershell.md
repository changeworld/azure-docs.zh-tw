---
title: 設定公用 IP 位址的路由喜好設定 - Azure PowerShell
titlesuffix: Azure Virtual Network
description: 了解如何使用 Azure PowerShell 設定公用 IP 位址的路由喜好設定。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 47e1701accd0ef7cce2a08b230682f4f8d5d8ed4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491497"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>使用 Azure PowerShell 設定公用 IP 位址的路由喜好設定

本文說明如何使用 Azure PowerShell，透過 ISP 網路 (**網際網路** 選項) 為公用 IP 位址設定路由喜好設定。 建立公用 IP 位址之後，您可以將其與下列 Azure 資源建立關聯，以取得連至網際網路的輸入和輸出流量：

* 虛擬機器
* 虛擬機器擴展集
* Azure Kubernetes Service (AKS)
* 網際網路對應負載平衡器
* 應用程式閘道
* Azure 防火牆

根據預設，公用 IP 位址的路由喜好設定會設為所有 Azure 服務的 Microsoft 全球網路，而且可與任何 Azure 服務相關聯。

> [!IMPORTANT]
> 路由喜好設定目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果您沒有 Azure 訂用帳戶，請立即建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組 6.9.0 版或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) 來建立資源群組。 此範例會在 *eastus* 位置中建立名為 *myResourceGroup* 的資源群組：

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>使用網際網路路由喜好設定建立公用 IP

下列命令會建立一個新的公用 IP，其路由喜好設定類型為「美國東部」 Azure 區域中的「網際網路」：

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

您可以將上述建立的公用 IP 位址與 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器建立關聯。 使用教學課程頁面上的 CLI 章節：[將公用 IP 位址與虛擬機器建立關聯](associate-public-ip-address-vm.md#azure-cli)，以將公用 IP 與您的 VM 產生關聯。 您也可以將公用 IP 位址指派給負載平衡 **前端** 組態，將上述建立的公用 IP 位址與 [Azure Load Balancer](../load-balancer/load-balancer-overview.md) 建立關聯。 此公用 IP 位址作為負載平衡的虛擬 IP 位址 (VIP)。

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

- 深入了解[公用 IP 位址中的路由喜好設定](routing-preference-overview.md)。
- [使用 Azure PowerShell 設定 VM 的路由喜好設定](configure-routing-preference-virtual-machine-powershell.md)。
