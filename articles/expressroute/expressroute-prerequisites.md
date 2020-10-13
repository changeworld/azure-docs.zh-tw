---
title: Azure ExpressRoute：必要條件
description: 本頁面提供在您可以訂購 Azure ExpressRoute 電路之前必須符合的需求清單。 其中包含一份檢查清單。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 87c8a7523222afd3ae4daeb8fb5824025e693cd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569623"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute 必要條件和檢查清單
若要使用 ExpressRoute 連線到 Microsoft 雲端服務，您必須確認是否符合以下各節中所列的下列需求。

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure 帳戶
* 使用中的有效 Microsoft Azure 帳戶。 需要有此帳戶才能設定 ExpressRoute 循環。 ExpressRoute 循環是 Azure 訂用帳戶內的資源。 即使連線只限于非 Azure Microsoft 雲端服務（例如 Microsoft 365），還是需要 Azure 訂用帳戶。
* 使用中的 Microsoft 365 訂用帳戶 (是否) 使用 Microsoft 365 服務。 如需詳細資訊，請參閱本文的 Microsoft 365 特定需求一節。

## <a name="connectivity-provider"></a>連線提供者

* 您可以與 [ExpressRoute 連線合作夥伴](expressroute-locations.md#partners) 合作來連線到 Microsoft Cloud。 您可以透過 [三種方法](expressroute-introduction.md)在內部部署網路與 Microsoft 之間設定連線。
* 如果您的提供者不是 ExpressRoute 連線合作夥伴，您仍可透過 [雲端交換服務提供者](expressroute-locations.md#connectivity-through-exchange-providers)連線到 Microsoft Cloud。

## <a name="network-requirements"></a>網路需求
* **每個對等互連位置的冗余**： microsoft 需要在 microsoft 的路由器與每個 ExpressRoute 線路上的對等互連路由器之間設定多餘的 BGP 會話， (即使您只有 [一個與雲端 exchange](expressroute-faqs.md#onep2plink)) 的實體連線時也一樣。
* 嚴重損壞**修復的冗余**： Microsoft 強烈建議您在不同的對等互連位置中至少設定兩個 ExpressRoute 線路，以避免單一失敗點。
* **路由**︰根據您連線到 Microsoft Cloud 的方式，您或您的提供者需要設定及管理用於[路由網域](expressroute-circuit-peerings.md)的 BGP 工作階段。 某些乙太網路連線服務提供者或雲端交換服務提供者可能會提供 BGP 管理功能做為附加價值服務。
* **NAT**：Microsoft 只接受透過 Microsoft 對等互連的公用 IP 位址。 如果您在內部部署網路中使用私人 IP 位址，您或您的提供者必須[使用 NAT](expressroute-nat.md) 將私人 IP 位址轉譯成公用 IP 位址。
* **QoS**：商務用 Skype 具有各種服務 (例如語音、視訊、文字)，其所要求的 QoS 處理方式各有差異。 您和您的提供者應該遵循 [QoS 需求](expressroute-qos.md)。
* **網路安全性**︰透過 ExpressRoute 連線到 Microsoft Cloud 時，請考慮[網路安全性](../best-practices-network-security.md)。

## <a name="microsoft-365"></a>Microsoft 365
如果您打算在 ExpressRoute 上啟用 Microsoft 365，請參閱下列檔，以取得有關 Microsoft 365 需求的詳細資訊。

* [適用于 Microsoft 365 的 Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)
* [使用 ExpressRoute 進行 Microsoft 365 的路由](/microsoft-365/enterprise/routing-with-expressroute)
* [ExpressRoute 的高可用性與容錯移轉](https://aka.ms/erhighavailability)
* [Microsoft 365 Url 和 IP 位址範圍](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Microsoft 365 的網路規劃和效能調整](/microsoft-365/enterprise/network-planning-and-performance)
* [Microsoft 365 的網路和遷移規劃](/microsoft-365/enterprise/network-and-migration-planning)
* [Microsoft 365 與內部部署環境的整合](/microsoft-365/enterprise/microsoft-365-integration)
* [Office 365 上的 ExpressRoute 進階訓練影片](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>後續步驟
* 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
* 尋找 ExpressRoute 連線提供者。 請參閱 [ExpressRoute 合作夥伴和對等位置](expressroute-locations.md)。
* 請參閱[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的需求。
* 設定 ExpressRoute 連線。
  * [建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md)
  * [設定路由](expressroute-howto-routing-arm.md)
  * [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)
