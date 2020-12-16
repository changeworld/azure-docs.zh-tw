---
title: Azure ExpressRoute： ARP 資料表-疑難排解
description: 本頁面提供的指示，說明如何取得 ExpressRoute 電路 (ARP) 資料表的位址解析通訊協定
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7d8ae2c58979c66ebbbab366d172179bdeee4253
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561574"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>在 Resource Manager 部署模型中取得 ARP 表格
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - 傳統](expressroute-troubleshooting-arp-classic.md)
> 
> 

本文將逐步引導您了解適用於 ExpressRoute 線路的 ARP 表格。

> [!IMPORTANT]
> 本文件旨在協助您診斷並修正簡單的問題。 它無法取代 Microsoft 支援服務。 如果無法使用如下所述的指引來解決問題，您必須向 [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 開啟支援票證。
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>位址解析通訊協定 (ARP) 和 ARP 表格
位址解析通訊協定 (ARP) 是 [RFC 826](https://tools.ietf.org/html/rfc826)中定義的第 2 層通訊協定。 ARP 可用於利用 IP 位址來對應乙太網路位址 (MAC 位址)。

ARP 資料表針對每個對等互連類型提供主要和次要介面的下列資訊：

1. 內部部署路由器介面 IP 位址到 MAC 位址的對應
2. ExpressRoute 路由器介面 IP 位址到 MAC 位址的對應
3. 對應的存留期

ARP 表格可協助您驗證第 2 層組態，並為第 2 層的基礎連線問題進行疑難排解。 

範例 ARP 表格： 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


下一節的資訊能為您說明如何檢視 ExpressRoute 邊緣路由器所看見的 ARP 表格。 

## <a name="prerequisites-for-learning-arp-tables"></a>了解 ARP 表格的必要條件
在進一步進行之前，請確定下列資訊是正確的：

* 至少使用一個對等互連設定的有效 ExpressRoute 線路。 線路必須由連線提供者完全設定。 您或您的連線提供者必須在這個線路上至少設定 Azure 私用、Azure 公用或 Microsoft 對等互連。
* 用來設定對等互連的 IP 位址範圍。 請參閱 [ [ExpressRoute 路由需求] 頁面](expressroute-routing.md) 中的 ip 位址指派範例，以瞭解如何將 ip 位址對應至介面。 您可以藉由檢閱 [ExpressRoute 對等互連組態頁面](expressroute-howto-routing-arm.md)來取得對等互連組態的詳細資訊。
* 在可與這些 IP 位址搭配使用的介面的 MAC 位址上，由網路小組 / 連線提供者所提供的資訊。
* 您必須擁有適用於 Azure 的最新 PowerShell 模組 (版本 1.50 或更新版本)。

> [!NOTE]
> 如果第3層是由服務提供者提供，且入口網站/輸出中的 ARP 資料表為空白，請使用入口網站上的 [重新整理] 按鈕來重新整理電路設定。 此作業將套用在您線路上的正確路由組態。 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>取得適用於 ExpressRoute 線路的 ARP 表格
本節將指示您如何使用 PowerShell 來為每個對等互連檢視 ARP 表格。 您或您的連線提供者必須先設定對等互連，才能有進一步的進展。 每個線路都有兩個路徑 (主要和次要)。 您可以單獨檢查每個路徑的 ARP 表格。

### <a name="arp-tables-for-azure-private-peering"></a>適用於 Azure 私用對等互連的 ARP 表格
下列 Cmdlet 提供適用於 Azure 私用對等互連的 ARP 表格

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure private peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

# ARP table for Azure private peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 
```

針對其中一個路徑的範例輸出如下所示

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>適用於 Azure 公用對等互連的 ARP 表格
下列 Cmdlet 提供適用於 Azure 公用對等互連的 ARP 表格

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure public peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

# ARP table for Azure public peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 
```


針對其中一個路徑的範例輸出如下所示

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>適用於 Microsoft 對等互連的 ARP 表格
下列 Cmdlet 提供適用於 Microsoft 對等互連的 ARP 表格

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Microsoft peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

# ARP table for Microsoft peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 
```


針對其中一個路徑的範例輸出如下所示

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>如何使用此資訊
對等互連的 ARP 表格可用來決定驗證第 2 層組態與連線。 本節將概述 ARP 表格在不同狀況下的呈現方式。

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>當線路處於運作狀態 (預期狀態) 時的 ARP 表格
* ARP 資料表會有內部部署端的專案，其中具有有效的 IP 位址和 MAC 位址。 Microsoft 端可以看到相同的。 
* 內部部署 IP 位址的最後一個八位元一定是奇數。
* Microsoft IP 位址的最後一個八位元一定是偶數。
* 相同的 MAC 位址會出現在 Microsoft 端，以供所有三個對等互連 (主要/次要) 。 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>當內部部署 / 連線提供者端發生問題時的 ARP 表格
如果發生內部部署或連線提供者的問題，ARP 資料表將會顯示兩個專案的其中一項。 您會看到內部部署 MAC 位址顯示為 [不完整]，或只在 ARP 表格中看到 Microsoft 專案。
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```
或
   
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```  

> [!NOTE]
> 向連線提供者開啟支援要求，對這類問題進行偵錯。 如果 ARP 表沒有對應到 MAC 位址之介面的 IP 位址，請檢閱下列條件︰
> 
> 1. 針對 MSEE-PR 和 MSEE 之間的連結所指派的 /30 子網路的第一個 IP 位址，是用在 MSEE-PR 的介面上。 Azure 一律為 MSEE 使用第二個 IP 位址。
> 2. 確認客戶 (C 標籤) 和服務 (S 標籤) VLAN 標籤是否都和「MSEE-PR 與 MSEE」對上的相符。
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>當 Microsoft 端發生問題時的 ARP 表格
* 如果 Microsoft 端有問題，您將不會看到對等互連顯示的 ARP 表格。 
* 向 [Microsoft 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)開啟支援票證。 指出您發生第 2 層連線的問題。 

## <a name="next-steps"></a>後續步驟
* 驗證 ExpressRoute 線路的第3層設定。
  * 取得路由摘要以判斷 BGP 會話的狀態。
  * 取得路由表以判斷哪些前置詞會在 ExpressRoute 之間公告。
* 藉由查看 bytes in/out 來驗證資料傳輸。
* 如果您仍遇到問題，請向 [Microsoft 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 服務開啟支援票證。

