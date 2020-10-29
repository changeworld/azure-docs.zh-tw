---
title: Azure DDoS 保護基本的最佳作法
description: 瞭解使用 DDoS 保護的最佳安全性作法。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 594a7e2a6977cc2a7052a15e1a007c68c08da259
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905105"
---
# <a name="fundamental-best-practices"></a>基本的最佳做法

下一節提供在 Azure 上建置 DDoS 復原服務的規範性指引。

## <a name="design-for-security"></a>安全性設計

確保安全性在應用程式的整個生命週期 (從設計和實作到部署與作業) 中具有優先順序。 應用程式可能存在一些錯誤 (bug)，可允許數量相對較低的要求過度使用大量資源，因而導致服務中斷。 

若要協助保護在 Microsoft Azure 上執行的服務，您應該對您應用程式架構有良好的了解，而且必須專注於[軟體品質的五大要素](/azure/architecture/guide/pillars)。
您應該了解一般流量、應用程式與其他應用程式之間的連線模型，以及公開至公用網際網路的服務端點。

確保應用程式有足夠的彈性，能夠處理以應用程式本身為目標的阻斷服務，至關重要。 從[安全性開發週期 (SDL)](https://www.microsoft.com/sdl/default.aspx) 開始，安全性和隱私權便會內建於 Azure 平台之中。 SDL 會在每個開發階段處理安全性，並確保 Azure 會持續更新以使其更為安全。

## <a name="design-for-scalability"></a>延展性設計

延展性是指系統能夠處理負載增加的能力。 將您的應用程式設計為可[水平調整](/azure/architecture/guide/design-principles/scale-out)以滿足放大負載的需求，遭遇 DDoS 攻擊時尤其需要。 如果您的應用程式相依於服務的單一執行個體，它會建立單一失敗點。 佈建多個執行個體可讓系統更有彈性且更具延展性。

對於 [Azure App Service](/azure/app-service/app-service-value-prop-what-is)，請選擇可提供多個執行個體的 [App Service 方案](/azure/app-service/overview-hosting-plans)。 對於 Azure 雲端服務，設定您的每個角色以使用[多個執行個體](/azure/cloud-services/cloud-services-choose-me)。 對於 [Azure 虛擬機器](../virtual-machines/index.yml)，確保虛擬機器 (VM) 架構包含多個 VM，而且每個 VM 都包含於[可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)中。 建議您使用 [虛擬機器擴展集](../virtual-machine-scale-sets/overview.md) 來進行自動調整功能。

## <a name="defense-in-depth"></a>深層防禦

深層防禦背後的概念是使用各種不同的防禦策略來管理風險。 將應用程式中的安全性防禦分層，可降低成功攻擊的機會。 建議您使用 Azure 平台的內建功能，為您的應用程式實作安全設計。

例如，攻擊的風險會隨著應用程式大小 ( *介面區* ) 而提高。 您可以使用核准清單來關閉已公開的 IP 位址空間，以及負載平衡器 ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) 和 [Azure 應用程式閘道](/azure/application-gateway/application-gateway-create-probe-portal)) 不需要的接聽埠，藉此減少介面區。 [網路安全性群組 (NSG)](/azure/virtual-network/security-overview) 是減少攻擊面的另一種方法。
您可以使用[服務標記](/azure/virtual-network/security-overview#service-tags)和[應用程式安全性群組](/azure/virtual-network/security-overview#application-security-groups)，將建立安全性規則與設定網路安全性的複雜性近可能降低，直到成為應用程式結構的自然延伸。

應盡可能將 Azure 服務部署於[虛擬網路](/azure/virtual-network/virtual-networks-overview)上。 此種做法可讓服務資源透過私人 IP 位址進行通訊。 根據預設，來自虛擬網路的 Azure 服務流量會使用公用 IP 位址作為來源 IP 位址。 使用[服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)，會在從虛擬網路存取 Azure 服務時，將服務流量切換為使用虛擬網路私人位址作為來源 IP 位址。

我們通常會看到客戶的內部部署資源連同其在 Azure 中的資源遭受攻擊。 如果您將內部部署環境連線到 Azure，建議盡量少讓內部部署資源暴露於公用網際網路上。 您可以藉由在 Azure 中部署已知的公用實體，來使用縮放和進階的 DDoS 保護功能。 由於這些可公開存取的實體通常是 DDoS 攻擊的目標，因此，將它們放置於 Azure，可降低對您內部部署資源的影響。

## <a name="next-steps"></a>下一步

- 瞭解如何 [建立 DDoS 保護計劃](manage-ddos-protection.md)。