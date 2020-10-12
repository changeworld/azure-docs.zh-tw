---
title: '跨區域負載平衡器 (預覽) '
titleSuffix: Azure Load Balancer
description: Azure Load Balancer 的跨區域負載平衡器層級總覽。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: d55f52b5e99a7a617e2bec8bea4d6e6ef687730a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336525"
---
# <a name="cross-region-load-balancer-preview"></a>跨區域負載平衡器 (預覽) 

Azure Load Balancer 會將抵達負載平衡器前端的輸入流量分散至後端集區實例。

Azure Standard Load Balancer 支援跨區域負載平衡，可啟用地理冗余的 HA 案例，例如：

* 來自多個區域的連入流量。
* [立即的全域容錯移轉](#regional-redundancy) 至下一個最佳的區域部署。
* 將跨區域的負載分配到最接近的 Azure 區域，並具備 [ultra 延遲](#ultra-low-latency)。
* 能夠在單一端點後方 [擴大/縮小](#ability-to-scale-updown-behind-a-single-endpoint) 。
* [靜態 IP](#static-ip)
* [用戶端 IP 保留](#client-ip-preservation)
* [在現有的負載平衡器解決方案上建立](#build-cross-region-solution-on-existing-azure-load-balancer) ，無需學習曲線

> [!IMPORTANT]
> 跨區域負載平衡器目前為預覽狀態，且未正式推出。  若要存取跨區域負載平衡器的預覽，請聯絡： [crossregionlb@microsoft.com](mailto:crossregionlb@microsoft.com) 。 </br> </br>
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

跨區域負載平衡可提供與「區域標準」負載平衡器一樣高效能和低延遲的優點。 

跨區域負載平衡器的前端 IP 設定是靜態的，而且會在 [大部分的 Azure 區域](#participating-regions)中公告。

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="跨區域負載平衡器的圖表。" border="true":::

> [!NOTE]
> 負載平衡器在跨區域負載平衡器上負載平衡規則的後端埠應符合區域標準負載平衡器上負載平衡規則/輸入 nat 規則的前端埠。 

### <a name="regional-redundancy"></a>區域冗余

將全域前端公用 IP 位址新增至現有的負載平衡器，以設定區域冗余。 

如果某個區域失敗，流量會路由傳送至下一個最接近的健康區域負載平衡器。  

跨區域負載平衡器的健康情況探查每隔20秒會收集可用性的相關資訊。 如果有一個區域負載平衡器將其可用性降至0，則跨區域負載平衡器會偵測到失敗。 區域負載平衡器接著會離開旋轉。 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="跨區域負載平衡器的圖表。" border="true":::

### <a name="ultra-low-latency"></a>Ultra-低延遲

鄰近性負載平衡演算法是以使用者的地理位置和區域部署為基礎。 

從用戶端啟動的流量會達到最接近的參與區域，並透過 Microsoft 全球網路骨幹行進，以抵達最接近的區域部署。 

例如，您在 Azure 區域中有一個具有標準負載平衡器的跨區域負載平衡器：

* 美國西部
* 北歐

如果流程是從西雅圖開始，流量會進入美國西部。 此區域是來自西雅圖的最接近參與區域。 流量會路由傳送至最接近的區域負載平衡器，也就是美國西部。

Azure 跨區域負載平衡器會使用地理鄰近性的負載平衡演算法來進行路由決策。 

區域負載平衡器設定的負載分配模式，會在多個區域負載平衡器用於地理鄰近時，用來進行最終的路由決定。

如需詳細資訊，請參閱[設定 Azure Load Balancer 的分送模式](https://docs.microsoft.com/azure/load-balancer/load-balancer-distribution-mode)。


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>能夠在單一端點後方擴大/縮小

當您向客戶公開跨區域負載平衡器的全域端點時，您可以在全域端點後方新增或移除區域部署，而不會對客戶造成影響。 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>靜態 IP
跨區域負載平衡器隨附靜態公用 IP，可確保 IP 位址維持不變。 若要深入瞭解靜態 IP，請參閱 [這裡](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#allocation-method)

### <a name="client-ip-preservation"></a>用戶端 IP 保留
跨區域負載平衡器是一種第4層傳遞網路負載平衡器。 此傳遞會保留封包的原始 IP。  原始 IP 可供在虛擬機器上執行的程式碼使用。 這項保留可讓您套用 IP 位址特有的邏輯。

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>建立現有 Azure Load Balancer 的跨區域解決方案
跨區域負載平衡器的後端集區包含一或多個區域負載平衡器。 

將您現有的負載平衡器部署新增至跨區域負載平衡器，以取得高可用性的跨區域部署。

[**首頁] 區域**是部署跨區域負載平衡器的位置。 此區域不會影響流量的路由方式。 如果主要區域停止運作，則不會影響流量的流程。

### <a name="home-regions"></a>首頁區域
* 美國東部 2
* 美國西部
* 西歐
* 東南亞
* 美國中部
* 北歐
* 東亞

> [!NOTE]
> 您只能在上述8個區域的其中一個區域部署跨區域負載平衡器。

**參與的區域**是負載平衡器的全域公用 IP 可用的位置。 

由使用者啟動的流量會透過 Microsoft 核心網路傳送到最接近的參與區域。 

跨區域負載平衡器會將流量路由傳送至適當的區域負載平衡器。

### <a name="participating-regions"></a>參與區域
* 美國東部 
* 西歐 
* 美國中部 
* 美國東部 2 
* 美國西部 
* 北歐 
* 美國中南部 
* 美國西部 2 
* 英國南部 
* 東南亞 
* 美國中北部 
* 日本東部 
* 東亞 
* 美國中西部 
* 澳大利亞東南部 
* 澳大利亞東部 
* 印度中部 

## <a name="limitations"></a>限制

* 跨區域前端 IP 設定僅限公用。 目前不支援內部前端。

* 私人或內部負載平衡器無法新增至跨區域負載平衡器的後端集區 

* 不支援跨區域 IPv6 前端 IP 設定。 

* 目前無法設定健康情況探查。 預設健康情況探查每隔20秒會自動收集關於區域負載平衡器的可用性資訊。 

## <a name="pricing-and-sla"></a>價格和 SLA
跨區域負載平衡器會共用標準負載平衡器的 [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) 。

 
## <a name="next-steps"></a>後續步驟

- 請參閱 [建立公用標準負載平衡器](quickstart-load-balancer-standard-public-portal.md) ，開始使用負載平衡器。
- 深入了解 [Azure Load Balancer](load-balancer-overview.md)。
- 負載平衡器 [常見問題](load-balancer-faqs.md)
