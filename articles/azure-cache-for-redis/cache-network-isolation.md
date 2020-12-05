---
title: Azure Cache for Redis 網路隔離選項
description: 在本文中，您將瞭解如何根據您的需求判斷最適合的網路隔離解決方案。 我們將探討 Azure Private Link、Azure 虛擬網路 (VNet) 插入和 Azure 防火牆規則的基本概念，並提供其優點與限制。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ef284661d44f700cf0b5282efcd2e6f7b94fa3b6
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621513"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Azure Cache for Redis 網路隔離選項 
在本文中，您將瞭解如何根據您的需求判斷最適合的網路隔離解決方案。 我們將探討 Azure Private Link、Azure 虛擬網路 (VNet) 插入和 Azure 防火牆規則的基本概念，並提供其優點與限制。  

## <a name="azure-private-link-public-preview"></a>Azure Private Link (公開預覽)  
Azure Private Link 提供從虛擬網路到 Azure PaaS 服務的私人連線能力。 它可簡化網路架構，並藉由消除公用網際網路的資料暴露，來保護 Azure 中端點之間的連線。 

### <a name="advantages"></a>優點
* 在基本、標準和高階 Azure Cache for Redis 實例上支援。 
* 藉由使用 [Azure Private Link](../private-link/private-link-overview.md)，您可以透過私人端點從虛擬網路連線到 Azure 快取實例，這會在虛擬網路內的子網中獲指派私人 IP 位址。 如此一來，來自 VNet 內的快取實例也可公開使用。  
* 私人端點建立之後，就可以透過旗標限制存取公用網路 `publicNetworkAccess` 。 依預設，此旗標會設為 `Disabled` ，這只允許存取私用連結。 您可以 `Enabled` 使用 PATCH 要求將值設定為或 `Disabled` 。 如需詳細資訊，請參閱 [Azure Private Link (Preview) Azure Cache for Redis ](cache-private-link.md)。 
* 所有外部快取相依性都不會影響 VNet 的 NSG 規則。

### <a name="limitations"></a>限制 
* 已停用私人端點 (NSG) 的網路安全性群組。 但是，如果子網上有其他資源，則 NSG 強制會套用至這些資源。
* 目前尚不支援異地複寫、防火牆規則、入口網站主控台支援、每個叢集快取的多個端點、防火牆持續性和 VNet 插入快取。 
* 若要連接到叢集快取，必須將 `publicNetworkAccess` 設定為， `Disabled` 而且只能有一個私人端點連接。

> [!NOTE]
> 將私人端點新增至快取實例時，由於 DNS 的原因，所有 Redis 流量都會移至私人端點。
> 確定先前的防火牆規則會先進行調整。  
>
>

## <a name="azure-virtual-network-injection"></a>Azure 虛擬網路插入 
VNet 是您在 Azure 中私人網路的基本組建區塊。 VNet 可讓許多 Azure 資源安全地彼此通訊，以及與網際網路和內部部署網路通訊。 VNet 就像是傳統的網路，您可以在自己的資料中心操作，但有 Azure 基礎結構、規模、可用性和隔離的優點。 

### <a name="advantages"></a>優點
* 當 Azure Cache for Redis 實例是以 VNet 設定時，它不是可公開定址，而且只能從 VNet 內的虛擬機器和應用程式存取。  
* 當 VNet 與受限制的 NSG 原則結合時，它有助於降低資料遭到外泄的風險。 
* VNet 部署可為您的 Azure Cache for Redis 以及子網、存取控制原則及其他功能提供增強的安全性和隔離，以進一步限制存取。 
* 支援異地複寫。 

### <a name="limitations"></a>限制
* VNet 插入快取僅適用于 Premium Azure Cache for Redis。 
* 使用 VNet 插入快取時，您必須開啟 VNet 來快取相依性，例如 Crl/PKI、AKV、Azure 儲存體、Azure 監視器等等。  


## <a name="azure-firewall-rules"></a>Azure 防火牆規則
[Azure 防火牆](../firewall/overview.md) 是受控的雲端式網路安全性服務，可保護您的 Azure VNet 資源。 它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端擴充性。 您可以橫跨訂用帳戶和虛擬網路集中建立、強制執行以及記錄應用程式和網路連線原則。  

### <a name="advantages"></a>優點
* 設定防火牆規則時，只有來自指定 IP 位址範圍的用戶端連線可以連接至快取。 系統一律會允許來自「Azure Redis 快取」監視系統的連線，即使已設定防火牆規則也一樣。 也允許您定義的 NSG 規則。  

### <a name="limitations"></a>限制
* 防火牆規則可以與 VNet 插入快取搭配使用，但目前不能搭配私人端點使用。 


## <a name="next-steps"></a>後續步驟
* 瞭解如何為 [Premium Azure Cache for Redis 實例設定 VNet 插入](cache-how-to-premium-vnet.md)快取。  
* 瞭解如何設定 [所有 Azure Cache for Redis 層的防火牆規則](cache-configure.md#firewall)。 
* 瞭解如何 [設定所有 Azure Cache for Redis 層的私人端點](cache-private-link.md)。