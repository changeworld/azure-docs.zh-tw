---
title: 應用程式 HA 的路由流量 - Azure PowerShell - 流量管理器
description: Azure PowerShell 指令碼範例 - 路由傳送流量以達到應用程式的高可用性
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: allensu
ms.openlocfilehash: 183599fccfad1806faae3cb90de225d388b77da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74049240"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-powershell"></a>為高可用性應用程式路由流量 - Azure PowerShell

這個指令碼會建立一個資源群組、兩個 App Service 方案、兩個 Web 應用程式、一個流量管理員設定檔和兩個流量管理員端點。 流量管理員會將流量導向主要區域中的應用程式，然後當主要區域中的應用程式無法使用時，將流量導向至次要區域。 在執行指令碼之前，您必須將 MyWebApp、MyWebAppL1 和 MyWebAppL2 值都變更為整個 Azure 中的唯一值。 在執行指令碼之後，您可以透過 URL mywebapp.trafficmanager.net 來存取主要區域中的應用程式。

如果需要，請使用 Azure PowerShell 指南中的說明安裝 Azure [PowerShell，](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)然後運行`Connect-AzAccount`以創建與 Azure 的連接。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、Web 應用程式、流量管理員設定檔和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | 建立用來存放所有資源的資源群組。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 建立 App Service 方案。 這就像是 Azure Web 應用程式的伺服器陣列。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 在 App Service 方案內建立 Azure Web 應用程式。 |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | 在 App Service 方案內建立 Azure Web 應用程式。 |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | 建立 Azure 流量管理員設定檔。 |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | 新增端點至 Azure 流量管理員設定檔。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/azure/overview)。

您可以在 [Azure 網路概觀文件](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)中找到其他網路 PowerShell 指令碼範例。
