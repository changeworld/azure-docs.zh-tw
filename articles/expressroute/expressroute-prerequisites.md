---
title: Azure ExpressRoute：必要條件
description: 本頁面提供在您可以訂購 Azure ExpressRoute 電路之前必須符合的需求清單。 其中包含一份檢查清單。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: a72eba9bde0745e66bdf8e7efd8eaec7d6a0b186
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74083364"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute 必要條件和檢查清單
若要使用 ExpressRoute 連線到 Microsoft 雲端服務，您必須確認是否符合以下各節中所列的下列需求。

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure 帳戶
* 使用中的有效 Microsoft Azure 帳戶。 需要有此帳戶才能設定 ExpressRoute 循環。 ExpressRoute 循環是 Azure 訂用帳戶內的資源。 即使連線只限于非 Azure Microsoft 雲端服務（例如 Office 365），Azure 訂用帳戶也是一項需求。
* 使用中的 Office 365 訂用帳戶 (如果使用的是 Office 365 服務)。 如需詳細資訊，請參閱本文的「Office 365 特定需求」一節。

## <a name="connectivity-provider"></a>連線提供者

* 您可以與 [ExpressRoute 連線合作夥伴](expressroute-locations.md#partners) 合作來連線到 Microsoft Cloud。 您可以透過 [三種方法](expressroute-introduction.md)在內部部署網路與 Microsoft 之間設定連線。
* 如果您的提供者不是 ExpressRoute 連線合作夥伴，您仍可透過 [雲端交換服務提供者](expressroute-locations.md#connectivity-through-exchange-providers)連線到 Microsoft Cloud。

## <a name="network-requirements"></a>網路需求
* **每個對等互連位置的冗余**： microsoft 需要在 microsoft 的路由器與每個 ExpressRoute 線路上的對等路由器之間設定多餘的 BGP 會話（即使您只有[一個與雲端交換的實體](expressroute-faqs.md#onep2plink)連線時）。
* 嚴重損壞**修復的冗余**： Microsoft 強烈建議您在不同的對等互連位置中至少設定兩個 ExpressRoute 線路，以避免發生單一失敗點。
* **路由**︰根據您連線到 Microsoft Cloud 的方式，您或您的提供者需要設定及管理用於[路由網域](expressroute-circuit-peerings.md)的 BGP 工作階段。 某些乙太網路連線服務提供者或雲端交換服務提供者可能會提供 BGP 管理功能做為附加價值服務。
* **NAT**：Microsoft 只接受透過 Microsoft 對等互連的公用 IP 位址。 如果您在內部部署網路中使用私人 IP 位址，您或您的提供者必須[使用 NAT](expressroute-nat.md) 將私人 IP 位址轉譯成公用 IP 位址。
* **QoS**：商務用 Skype 具有各種服務 (例如語音、視訊、文字)，其所要求的 QoS 處理方式各有差異。 您和您的提供者應該遵循 [QoS 需求](expressroute-qos.md)。
* **網路安全性**︰透過 ExpressRoute 連線到 Microsoft Cloud 時，請考慮[網路安全性](../best-practices-network-security.md)。

## <a name="office-365"></a>Office 365
如果您打算在 ExpressRoute 上啟用 Office 365，請檢閱下列文件以取得 Office 365 需求的詳細資訊。

* [ExpressRoute for Office 365 概觀](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [使用 ExpressRoute for Office 365 進行路由](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [ExpressRoute 的高可用性和容錯移轉](https://aka.ms/erhighavailability)
* [Office 365 URL 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Office 365 的網路規劃與效能調整](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [網路頻寬計算機和工具](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [整合 Office 365 與內部部署環境](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Office 365 上的 ExpressRoute 進階訓練影片](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>後續步驟
* 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
* 尋找 ExpressRoute 連線提供者。 請參閱 [ExpressRoute 合作夥伴和對等位置](expressroute-locations.md)。
* 請參閱[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的需求。
* 設定 ExpressRoute 連線。
  * [建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md)
  * [設定路由](expressroute-howto-routing-arm.md)
  * [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)
