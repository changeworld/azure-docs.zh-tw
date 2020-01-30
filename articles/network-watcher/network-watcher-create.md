---
title: 建立 Azure 網路監看員執行個體 | Microsoft Docs
description: 了解如何在 Azure 區域中啟用網路監看員。
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: b091c501fb565fb267c40f686dc037e41f3c90ee
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845133"
---
# <a name="create-an-azure-network-watcher-instance"></a>建立 Azure 網路監看員執行個體

網路監看員是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 案例層級監視可讓您在端對端網路層級檢視診斷問題。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。 網路監看員會透過建立網路監看員資源來啟用。 這項資源可讓您使用網路監看員功能。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>網路監看員會自動啟用
當您建立或更新訂用帳戶中的虛擬網路時，網路監看員將會在虛擬網路的區域中自動啟用。 自動啟用網路監看員不會對您的資源或相關費用造成任何影響。

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>退出自動啟用網路監看員
如果您想要退出自動啟用網路監看員，執行下列命令即可達成：

> [!WARNING]
> 退出自動啟用網路監看員是永久性變更。 一旦退出，便無法在不[連絡支援服務](https://azure.microsoft.com/support/options/)的情況下加入

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>在入口網站中建立網路監看員

瀏覽至 [所有服務] >  [網路] >  [網路監看員]。 您可以選取要啟用網路監看員的所有訂用帳戶。 此動作會在每個可用區域建立網路監看員。

![建立網路監看員](./media/network-watcher-create/figure1.png)

當您使用入口網站啟用網路監看員時，網路監看員執行個體的名稱將會自動設定為 NetworkWatcher_region_name，其中 region_name 對應至啟用該執行個體的 Azure 區域。 例如，在美國中西部區域啟用的網路監看員，名稱會是 NetworkWatcher_westcentralus。

網路監看員執行個體會自動在名為 NetworkWatcherRG 的資源群組中建立。 如果該資源群組尚未存在，系統就會加以建立。

如果您要自訂網路監看員執行個體的名稱，以及放置該執行個體的資源群組名稱，可以使用後續章節中所述的 Powershell、Azure CLI、REST API 或 ARMClient 方法。 在每個選項中，都必須先有資源群組，才能在其中建立網路監看員。  

## <a name="create-a-network-watcher-with-powershell"></a>使用 PowerShell 建立網路監看員

若要建立網路監看員執行個體，請執行下列範例︰

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>使用 Azure CLI 建立網路監看員

若要建立網路監看員執行個體，請執行下列範例︰

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>使用 REST API 建立網路監看員

使用 ARMclient 透過 PowerShell 呼叫 REST API。 您可以在 chocolatey 的 [ARMClient on Chocolatey](https://chocolatey.org/packages/ARMClient) 上找到 ARMClient

### <a name="log-in-with-armclient"></a>使用 ARMClient 登入

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>建立網路監看員

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="delete-a-network-watcher-in-the-portal"></a>在入口網站中刪除網路監看員

瀏覽至 [所有服務] >  [網路] >  [網路監看員]。

如果您還沒有的話，請選取 [總覽] 索引標籤。 使用下拉式清單來選取您想要在其中停用網路監看員的訂用帳戶。
按一下箭號，以展開所選訂用帳戶的區域清單。 針對任何指定的，請使用右邊的3個點來存取內容功能表。
按一下 [停用網路監看員] 以開始停用。 系統會要求您確認此步驟。 按一下 [是] 繼續進行。
在入口網站上，您必須針對每個訂用帳戶中的每個區域個別執行此動作。


## <a name="delete-a-network-watcher-with-powershell"></a>使用 PowerShell 刪除網路監看員

若要刪除網路監看員的實例，請執行下列範例：

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>後續步驟

您已擁有網路監看員執行個體，接下來您可以了解可用的功能︰

* [拓撲](network-watcher-topology-overview.md)
* [封包擷取](network-watcher-packet-capture-overview.md)
* [IP 流量驗證](network-watcher-ip-flow-verify-overview.md)
* [下一個躍點](network-watcher-next-hop-overview.md)
* [安全性群組檢視](network-watcher-security-group-view-overview.md)
* [NSG 流量記錄](network-watcher-nsg-flow-logging-overview.md)
* [虛擬網路閘道疑難排解](network-watcher-troubleshoot-overview.md)

一旦有網路監看員執行個體後，您可以啟用虛擬機器內的封包擷取。 若要了解做法，請參閱[建立警示觸發的封包擷取](network-watcher-alert-triggered-packet-capture.md)
