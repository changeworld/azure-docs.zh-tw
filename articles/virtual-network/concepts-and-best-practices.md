---
title: Azure 虛擬網路-概念和最佳作法
description: 深入瞭解 Azure 虛擬網路的概念和最佳作法。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: d279516c1c9c08512c850a0f70eb84c0c1f63166
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111152"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Azure 虛擬網路概念和最佳作法

本文說明 Azure 虛擬網路 (VNet) 的重要概念和最佳作法。

## <a name="vnet-concepts"></a>VNet 概念

- **位址空間︰** 在建立 VNet 時，您必須使用公用和私人 (RFC 1918) 位址指定自訂私人 IP 位址空間。 Azure 會從您指派的位址空間，將私人 IP 位址指派給虛擬網路中的資源。 例如，如果您在位址空間為 10.0.0.0/16 的 VNet 中部署 VM，系統就會對 VM 指派像是 10.0.0.4 等的私人 IP。
- **子網路：** 子網路可讓您將虛擬網路分成一或多個次網路，並將虛擬網路位址空間的一部分配置給每個子網路。 然後，您便可以在特定子網路中部署 Azure 資源。 和傳統網路一樣，子網路也可讓您將 VNet 位址空間分成多個區段，以便適合組織的內部網路使用。 這也會改善位址的配置效率。 您可以使用網路安全性群組來保護子網路內的資源。 如需詳細資訊，請參閱[網路安全性群組](security-overview.md)。
- **區域**：VNet 的範圍為單一區域/位置；不過，您可以使用虛擬網路對等互連將不同區域的多個虛擬網路連在一起。
- **訂用帳戶︰** VNet 的範圍為訂用帳戶。 您可以在每個 Azure [訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和 Azure [區域](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)內實作多個虛擬網路。

## <a name="best-practices"></a>最佳作法

當您 Azure 中建置網路時，請務必記住下列通用設計原則：

- 確保沒有重疊的位址空間。 確定 VNet 位址空間 (CIDR 區塊) 不會與組織的其他網路範圍重疊。
- 子網路不應涵蓋 VNet 的整個位址空間。 預先規劃並保留部分位址空間供日後使用。
- 建議您擁有少量大型 VNet 而非多個小型 VNet。 這可避免造成管理負荷。
- 將網路安全性群組 (NSG) 指派給其下的子網路，以保護您的 VNet。

## <a name="next-steps"></a>後續步驟

 若要開始使用虛擬網路，請建立一個虛擬網路、對其部署一些 VM，然後進行 VM 之間的通訊。 若要深入了解，請參閱[建立虛擬網路](quick-create-portal.md)快速入門。
