---
title: ExpressRoute：路由篩選-Microsoft 對等互連： Azure PowerShell
description: 本文說明如何使用 PowerShell 針對 Microsoft 對等互連設定路由篩選
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 3fa53258321b22e1683122edca1816f6d4c291b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618608"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>針對 Microsoft 對等互連設定路由篩選：PowerShell
> [!div class="op_single_selector"]
> * [Azure 入口網站](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

路由篩選是透過 Microsoft 對等互連使用支援服務子集的方式。 這篇文章中的步驟可協助您設定和管理 ExpressRoute 線路的路由篩選。

Office 365 服務（例如 Exchange Online、SharePoint Online 和商務用 Skype），以及 Azure 公用服務（例如儲存體和 SQL DB）都可透過 Microsoft 對等互連來存取。 Azure 公用服務可針對每個區域選取，但無法針對每個公用服務加以定義。

在 ExpressRoute 線路中設定 Microsoft 對等互連且已連結路由篩選時，針對這些服務選取的所有前置詞都會透過建立的 BGP 工作階段進行公告。 BGP 社群值附加至每個前置詞，來識別透過前置詞提供的服務。 如需 BGP 社群值和它們對應之服務的清單，請參閱 [BGP 社群](expressroute-routing.md#bgp)。

如果您需要連線到所有服務，則會透過 BGP 公告大量前置詞。 這會大幅增加網路內路由器維護的路由資料表大小。 如果您計劃僅使用透過 Microsoft 對等互連提供的服務子集，您可以用兩種方式減少路由資料表大小。 您可以：

- 在 BGP 社群上套用路由篩選，以篩選不想要的前置詞。 這是標準網路做法，在許多網路經常使用。

- 定義路由篩選，並將其套用到 ExpressRoute 線路。 路由篩選是新的資源，可讓您選取計劃透過 Microsoft 對等互連使用的服務清單。 ExpressRoute 路由器只會傳送屬於路由篩選中所識別之服務的前置詞清單。

### <a name="about-route-filters"></a><a name="about"></a>關於路由篩選

當您的 ExpressRoute 線路上設定 Microsoft 對等互連時，Microsoft 網路邊緣路由器會建立一組 BGP 會話與邊緣路由器（您或您的連線提供者）。 沒有路由會公告至您的網路。 若要讓路由公告至您的網路，您必須建立與路由篩選的關聯。

路由篩選可讓您識別想要透過 ExpressRoute 線路的 Microsoft 對等互連使用的服務。 基本上，它是所有 BGP 社區值的允許清單。 一旦定義路由篩選資源，並且連結至 ExpressRoute 線路，對應到 BGP 社群值的所有前置詞都會公告至您的網路。

您必須具有透過 ExpressRoute 使用 Office 365 服務的授權，才能在上面連結路由篩選與 Office 365 服務。 如果您未獲授權透過 ExpressRoute 使用 Office 365 服務，連結路由篩選的作業會失敗。 如需授權程序的詳細資訊，請參閱 [Azure ExpressRoute for Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)。

> [!IMPORTANT]
> 在 2017 年 8 月 1 日以前設定之 ExpressRoute 線路的 Microsoft 對等互連，會透過 Microsoft 對等互連公告所有服務首碼，即使未定義路由篩選也一樣。 在 2017 年 8 月 1 日當日或以後設定之 ExpressRoute 線路的 Microsoft 對等互連，不會公告任何首碼，直到路由篩選連結至線路為止。
> 
> 

### <a name="workflow"></a><a name="workflow"></a>工作流程

若要能夠成功透過 Microsoft 對等互連連線到服務，您必須完成下列設定步驟：

- 您必須具有已佈建 Microsoft 對等互連的使用中 ExpressRoute 線路。 您可以使用下列指示來完成這些工作：
  - 繼續之前，請[建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md)，並由您的連線提供者來啟用該線路。 ExpressRoute 線路必須處於已佈建及已啟用的狀態。
  - [建立 Microsoft 對等互連](expressroute-circuit-peerings.md)，如果您直接管理 BGP 工作階段。 或者，讓您的連線提供者為您的線路佈建 Microsoft 對等互連。

-  您必須建立及設定路由篩選。
    - 識別您透過 Microsoft 對等互連使用的服務
    - 識別與服務相關聯的 BGP 社群值清單
    - 建立規則以允許與 BGP 社群值相符的前置詞清單

-  您必須將路由篩選連結至 ExpressRoute 線路。

## <a name="before-you-begin"></a>開始之前

開始設定之前，請確定符合下列準則：

 - 開始設定之前，請檢閱[必要條件](expressroute-prerequisites.md)和[工作流程](expressroute-workflows.md)。

 - 您必須擁有作用中的 ExpressRoute 線路。 繼續之前，請遵循指示來 [建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md) ，並由您的連線提供者來啟用該線路。 ExpressRoute 線路必須處於已佈建及已啟用的狀態。

 - 您必須擁有使用中 Microsoft 對等互連。 請遵循[建立和修改對等互連設定](expressroute-circuit-peerings.md)一文中的指示。


### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>登入您的 Azure 帳戶

開始這項設定之前，您必須登入您的 Azure 帳戶。 cmdlet 會提示您 Azure 帳戶的登入認證。 登入之後，它會下載您的帳戶設定以供 Azure PowerShell 使用。

以提高的權限開啟 PowerShell 主控台並連線至您的帳戶。 使用下列範例來協助您連線。 如果您使用 Azure Cloud Shell，則不需要執行此 Cmdlet，因為您將會自動登入。

```azurepowershell
Connect-AzAccount
```

如果您有多個 Azure 訂用帳戶，請檢查帳戶的訂用帳戶。

```azurepowershell-interactive
Get-AzSubscription
```

指定您要使用的訂用帳戶。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>步驟 1：取得前置詞和 BGP 社群值的清單

### <a name="1-get-a-list-of-bgp-community-values"></a>1. 取得 BGP 社區值的清單

使用下列 Cmdlet 來取得與可透過 Microsoft 對等互連存取之服務相關聯的 BGP 社群值清單，以及與其相關聯的前置詞清單：

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. 建立您想要使用的值清單

製作您想要在路由篩選中使用的 BGP 社群值清單。

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>步驟 2：建立路由篩選和篩選規則

路由篩選只能有一個規則，且規則的類型必須是 'Allow'。 此規則可以具有與其相關聯的 BGP 社群值清單。

### <a name="1-create-a-route-filter"></a>1. 建立路由篩選

首先，建立路由篩選。 ' AzRouteFilter ' 命令只會建立路由篩選資源。 建立資源之後，您必須建立規則，然後將它附加到路由篩選物件。 執行下列命令以建立路由篩選資源：

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. 建立篩選規則

您可以使用以逗號分隔的清單格式，指定一組 BGP 社群，如範例所示。 執行下列命令以建立新規則：
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. 將規則新增至路由篩選

執行下列命令以將篩選規則新增至路由篩選：
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>步驟 3：將路由篩選連接到 ExpressRoute 線路

若您只有 Microsoft 對等互連，請執行下列命令，將路由器篩選附加在 ExpressRoute 線路上：

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>常見工作

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>若要取得路由篩選的屬性

若要取得路由篩選的屬性，請使用下列步驟：

1. 執行下列命令以取得路由篩選資源：

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. 藉由執行下列命令以取得路由篩選資源的路由篩選規則：

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>若要更新路由篩選的屬性

如果路由篩選已連接到線路，更新 BGP 社群清單會自動透過已建立的 BGP 工作階段傳播適當前置詞公告變更。 您可以使用下列命令來更新路由篩選的 BGP 社群清單：

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>若要從 ExpressRoute 線路取消連結路由篩選

一旦從 ExpressRoute 線路取消連結路由篩選，就不會透過 BGP 工作階段公告任何前置詞。 您可以使用下列命令以從 ExpressRoute 線路取消連結路由篩選：
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>若要刪除路由篩選

您只能在路由篩選尚未連結至任何線路時刪除路由篩選。 請在嘗試刪除之前，確認路由篩選尚未連結至任何線路。 您可以使用下列命令來刪除路由篩選：

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
