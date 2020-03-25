---
title: Azure PowerShell 指令碼範例 - Docker
description: Azure PowerShell 指令碼範例 - Docker
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/02/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 25c9712769c2fb9ee54a78989143625e325a9299
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74040173"
---
# <a name="create-a-docker-host-with-powershell"></a>用 PowerShell 建立 Docker 主機

這個指令碼會建立啟用 Docker 功能的虛擬機器，並啟動執行 NGINX 的容器。 執行這個指令碼之後，您可以透過 Azure 虛擬機器的 FQDN 存取 NGINX Web 伺服器。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-docker-host/create-docker-host.ps1 "Create Docker host")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 建立子網路組態。 此組態可使用於虛擬網路建立程序。 |
| [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。 |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。 |
| [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) | 建立網路安全性群組規則組態。 建立 NSG 時，此組態用來建立 NSG 規則。 |
| [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) | 建立網路安全性群組。 |
| [Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得子網路資訊。 建立網路介面時會使用此資訊。 |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | 建立網路介面。 |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | 建立 VM 組態。 此組態包括 VM 名稱、作業系統和系統管理認證等資訊。 建立 VM 時會使用此組態。 |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | 建立虛擬機器。 |
| [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) | 將 VM 擴充功能新增至虛擬機器。 在此範例中，Docker 擴充功能用來設定 Docker 和執行 NGINX Docker 容器。 |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure Linux VM 文件](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
