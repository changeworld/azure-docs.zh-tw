---
title: Azure 虛擬網路的 IPv6 概述
titlesuffix: Azure Virtual Network
description: IPv6 描述 Azure 虛擬網路中的 IPv6 終結點和數據路徑。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 03b279ead6b1f5d26ae92b63a8780a61dfd711bb
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420560"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>什麼是 Azure 虛擬網路的 IPv6?

Azure 虛擬網路 (VNet) 的 IPv6 使您能夠在 Azure 中託管應用程式,在虛擬網路中以及從 Internet 中使用 IPv6 和 IPv4 連接。 由於公共 IPv4 位址的耗盡,移動和物聯網 (IoT) 的新網路通常基於 IPv6 構建。 即使是歷史悠久的 ISP 和行動網路也正在轉換為 IPv6。 僅限 IPv4 的服務在現有市場和新興市場都處於真正的劣勢。 雙堆疊 IPv4/IPv6 連接使 Azure 託管的服務能夠透過全球可用的雙堆疊服務跨越這一技術差距,這些服務可輕鬆與現有的 IPv4 和這些新 IPv6 設備和網路連接。

Azure 的原始 IPv6 連接使為 Azure 中託管的應用程式提供雙堆疊 (IPv4/IPv6) 的 Internet 連接變得容易。 它允許為入站和出站啟動的連接簡單部署具有負載平衡 IPv6 連接的 VM。 此功能仍然可用,更多資訊[可在此處](../load-balancer/load-balancer-ipv6-overview.md)獲取。
Azure 虛擬網路的IPv6功能更加充分,支援在Azure中部署完整的IPv6解決方案體系結構。


下圖描述了 Azure 中的簡單雙堆疊 (IPv4/IPv6) 部署:

![IPv6 網路部署圖](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>優點

適用於 Azure VNET 權益的 IPv6:

- 説明將 Azure 託管應用程式的覆蓋範圍擴展到不斷增長的行動和物聯網市場。
- 雙堆疊 IPv4/IPv6 VM 可提供最大的服務部署靈活性。 單個服務實例可以同時連接支援 IPv4 和 IPv6 的 Internet 用戶端。
- 基於長期建立、穩定的 Azure VM 到 Internet IPv6 連接。
- 默認情況下安全,因為 IPv6 與 Internet 的連接僅在您在部署中顯式請求時建立。

## <a name="capabilities"></a>功能

Azure VNet 的 IPv6 包括以下功能:

- Azure 客戶可以定義 IPv6 虛擬網路位址空間,以滿足其應用程式、客戶的需求,或無縫整合到其本地 IP 空間中。
- 雙堆疊 (IPv4 和 IPv6) 虛擬網路具有雙堆疊子網路,使應用程式能夠在其虛擬網路或 - Internet 中同時連接 IPv4 和 IPv6 資源。
    > [!IMPORTANT]
    > IPv6 的子網的大小必須完全為 /64。  如果您決定啟用子網路由到本地網路,這可確保未來的相容性,因為某些路由器只能接受 /64 IPv6 路由。  
- 使用網路安全組的 IPv6 規則保護您的資源。
    - Azure 平台的分散式拒絕服務 (DDoS) 保護擴展到針對 Internet 的公共 IP
- 使用使用者定義的路由自定義虛擬網路中 IPv6 流量的路由,尤其是在利用網路虛擬設備增強應用程式時。
- Linux 與 Windows 虛擬機器都可以將 IPv6 用於 Azure VNET
- [標準 IPv6 公共負載均衡器](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)支援建立彈性、可擴充的應用程式,其中包括:
    - 可選的 IPv6 運行狀況探測,用於確定哪些後端池實例是健康的,因此可以接收新的流。
    - 可選的出站規則,可對出站連接提供完全聲明性控制,以根據特定需求擴展和調整此功能。
    - 可選的多種前端配置,使單個負載均衡器能夠使用多個 IPv6 公共 IP 位址 - 相同的前端協定和埠可以在前端位址之間重複使用。
    - 選擇的 IPv6 連接埠可以使用負載平衡規則的*浮動 IP*功能在後端介面上重複使用 
- [標準 IPv6 內部負載均衡器](ipv6-dual-stack-standard-internal-load-balancer-powershell.md)支援在 Azure VNET 中創建彈性多層應用程式。  
- 基本 IPv6 公共負載均衡器支援與舊部署相容
- [保留的 IPv6 公共 IP 位址和位址範圍](ipv6-public-ip-address-prefix.md)提供穩定、可預測的 IPv6 位址,便於為公司和客戶提供 Azure 託管應用程式的白名單。
- 實例級公共 IP 直接向各個 VM 提供 IPv6 互聯網連接。
- [將 IPv6 添加到僅使用 IPv4 的現有部署](ipv6-add-to-existing-vnet-powershell.md)- 此功能使您能夠輕鬆將 IPv6 連接添加到現有的僅 IPv4 部署中,而無需重新創建部署。  在此過程中,IPv4 網路流量不受影響,因此,根據您的應用程式和作業系統,您甚至可以將 IPv6 添加到即時服務。    
- 允許 Internet 用戶端使用其選擇的協定無縫存取雙堆疊應用程式,並支援 IPv6 (AAAA) 記錄的 Azure DNS。 
- 使用 IPv6 創建可自動擴展到負載的雙堆疊應用程式。
- [虛擬網路 (VNET) 對等互連](virtual-network-peering-overview.md)( 區域內和全域對等互連 - 使您能夠看似無時連接雙堆疊 VNET- 對等網路中 VM 上的 IPv4 和 IPv6 端點將能夠相互通訊。 在將部署轉換為雙堆疊時,您甚至可以使用僅限 IPv4 的 VNET 對等雙堆疊。 
- IPv6 故障排除和診斷可用於負載均衡器指標/警報和網路觀察程式功能,如數據包捕獲、NSG 流日誌、連接故障排除和連接監控。   

## <a name="scope"></a>影響範圍
Azure VNET 的 IPv6 是一個基本功能集,使客戶能夠在 Azure 中託管雙堆疊 (IPv4_IPv6) 應用程式。  我們打算隨著時間的推移向更多 Azure 網路功能添加 IPv6 支援,並最終提供 Azure PaaS 服務的雙堆疊版本,但與此同時,所有 Azure PaaS 服務都可以通過雙堆疊虛擬機上的 IPv4 終結點進行訪問。   

## <a name="limitations"></a>限制
Azure 虛擬網路版本的目前 IPv6 具有以下限制:
- Azure 虛擬網路的IPv6在所有全域 Azure 區域中都可用,但僅在全域 Azure 中可用,但尚未在政府雲中提供。
- ExpressRoute 和 VPN 閘道不能在啟用 IPv6 的 VNET 中使用,直接使用或與「使用遠端閘道」對等。 
- Azure 平臺 (AKS 等) 不支援容器的 IPv6 通訊。  

## <a name="pricing"></a>定價

IPv6 Azure 資源和頻寬的費率與IPv4相同。 IPv6 沒有額外的或不同的費用。 您可以找到有關公共 IP[位址](https://azure.microsoft.com/pricing/details/ip-addresses/)、[網路頻寬](https://azure.microsoft.com/pricing/details/bandwidth/)或[負載均衡器](https://azure.microsoft.com/pricing/details/load-balancer/)定價的詳細資訊。

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[Azure PowerShell 部署 IPv6 雙堆疊應用程式](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)。
- 瞭解如何使用[Azure CLI 部署 IPv6 雙堆疊應用程式](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)。
- 瞭解如何[使用資源管理員樣本 (JSON) 部署 IPv6 雙堆疊應用程式](ipv6-configure-standard-load-balancer-template-json.md)
