---
title: Azure PowerShell 指令碼範例 - 限制網路流量 | Microsoft Docs
description: Azure PowerShell 指令碼範例 - 建立具有 Web 應用程式防火牆和虛擬機器擴展集的應用程式閘道，它會使用 OWASP 規則來限制流量。
services: application-gateway
documentationcenter: networking
author: vhorne
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: fd36775e15376054bed34d9e828344c529313d3b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76273412"
---
# <a name="restrict-web-traffic-using-azure-powershell"></a>使用 Azure PowerShell 限制網路流量

這個指令碼會建立具有 Web 應用程式防火牆的應用程式閘道，它使用虛擬機器擴展集作為後端伺服器。 Web 應用程式防火牆會根據 OWASP 規則限制網路流量。 執行指令碼之後，您可以使用應用程式閘道的公用 IP 位址來進行測試。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss-waf.ps1 "Create application gateway with WAF")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、應用程式閘道和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 建立子網路組態。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 使用子網路組態建立虛擬網路。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立應用程式閘道的公用 IP 位址。 |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | 建立組態，該組態讓子網路與應用程式閘道產生關聯。 |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | 建立組態，該組態將公用 IP 位址指派給應用程式閘道。 |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | 指派要用來存取應用程式閘道的連接埠。 |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | 為應用程式閘道建立後端集區。 |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | 設定後端集區的設定。 |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | 建立接聽程式。 |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | 建立路由規則。 |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | 指定應用程式閘道的層級和容量。 |
| [New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/new-azapplicationgatewaywebapplicationfirewallconfiguration) | 建立 Web 應用程式防火牆組態。 |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | 建立應用程式閘道。 |
| [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | 建立擴展集的儲存體設定檔。 |
| [Set-AzVmssOsProfile](/powershell/module/az.compute/set-azvmssosprofile) | 定義擴展集的作業系統。 |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | 定義擴展集的網路介面。 |
| [New-AzVmss](/powershell/module/az.compute/new-azvm) | 建立虛擬機器擴展集。 |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | 建立儲存體帳戶。 |
| [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) | 設定記錄資料的診斷。 |
| [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) | 取得應用程式閘道的公用 IP 位址。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 | 
## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure 應用程式閘道文件](../powershell-samples.md)中找到其他應用程式閘道的 PowerShell 指令碼範例。
