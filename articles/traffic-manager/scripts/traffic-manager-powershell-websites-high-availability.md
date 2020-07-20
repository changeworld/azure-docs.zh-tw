---
title: 適用于應用程式 HA 的路由流量-Azure PowerShell-流量管理員
description: Azure PowerShell 指令碼範例：路由傳送流量以達到應用程式的高可用性
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: kumudD
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: rohink
ms.openlocfilehash: 3f448683dbb210ab196e27cfd98a376429d03aec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76934742"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>使用 Azure PowerShell 路由傳送流量以達到應用程式的高可用性

這個指令碼會建立一個資源群組、兩個 App Service 方案、兩個 Web 應用程式、一個流量管理員設定檔和兩個流量管理員端點。 流量管理員會將流量導向主要區域中的應用程式，然後當主要區域中的應用程式無法使用時，將流量導向至次要區域。 在執行指令碼之前，您必須將 MyWebApp、MyWebAppL1 和 MyWebAppL2 值都變更為整個 Azure 中的唯一值。 在執行指令碼之後，您可以透過 URL mywebapp.trafficmanager.net 來存取主要區域中的應用程式。

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzAccount` 來建立與 Azure 的連線。

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
