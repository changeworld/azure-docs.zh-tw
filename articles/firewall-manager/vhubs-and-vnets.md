---
title: Azure 防火牆管理員的架構選項有哪些？
description: 使用中樞虛擬網路或安全的虛擬中樞架構搭配 Azure 防火牆管理員來比較和對比。
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 09/14/2020
ms.author: victorh
ms.openlocfilehash: 71ff23e749139087f24da406474403167dcc1c0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563143"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Azure 防火牆管理員的架構選項有哪些？

Azure 防火牆管理員可以針對兩種網路架構類型提供安全性管理：

- **安全虛擬中樞**

   [Azure 虛擬 WAN 中樞](../virtual-wan/virtual-wan-about.md#resources)是一項由 Microsoft 管理的資源，可讓您輕鬆建立中樞和輪輻架構。 當安全性和路由原則與這類中樞相關聯時，即稱為 *[安全的虛擬中樞](secured-virtual-hub.md)*。 
- **中樞虛擬網路**

   這是您自行建立和管理的標準 Azure 虛擬網路。 當安全性原則與這類中樞相關聯時，我們即稱之為「安全虛擬網路」。 目前僅支援 Azure 防火牆原則。 您可以將包含工作負載伺服器和服務的輪輻虛擬網路對等互連。 您也可以在獨立虛擬網路中管理未對等互連到任何輪輻的防火牆。

## <a name="comparison"></a>比較

下表將比較這兩種架構選項，並可協助您決定哪一個適合您組織的安全性需求：


|  |**中樞虛擬網路**|**安全的虛擬中樞**  |
|---------|---------|---------|
|**基礎資源**     |虛擬網路|虛擬 WAN 中樞|
|**中樞 & 輪輻**     |使用虛擬網路對等互連|使用中樞虛擬網路連線自動建立|
|**內部內部部署連線能力**     |VPN 閘道最多 10 Gbps 和30個 S2S 連接;ExpressRoute|更具擴充性的 VPN 閘道，高達 20 Gbps 和 1000 S2S 連接;Express Route|
|**使用 SDWAN 的自動化分支連線能力**      |不支援|支援|
|**每個區域的中樞**     |每個區域有多個虛擬網路|每個區域的單一虛擬中樞。 多個中樞可能具有多個虛擬 Wan|
|**Azure 防火牆-多個公用 IP 位址**      |由客戶提供|自動產生|
|**Azure 防火牆可用性區域**     |支援|尚未提供|
|**具有協力廠商安全性即服務合作夥伴的 Advanced Internet security**     |客戶已針對所選合作夥伴服務建立及管理的 VPN 連線能力|透過安全性合作夥伴提供者流程和合作夥伴管理體驗來自動化|
|**將流量路由傳送至中樞的集中式路由管理**     |客戶管理的使用者定義路由|支援使用 BGP|
|**多個安全性提供者支援**|支援手動設定協力廠商防火牆的強制通道|對兩個安全性提供者的自動化支援：適用于私人流量篩選的 Azure 防火牆，以及網際網路篩選的協力廠商|
|**應用程式閘道上的 Web 應用程式防火牆** |虛擬網路支援|目前支援輪輻網路|
|**網路虛擬裝置**|虛擬網路支援|目前支援輪輻網路|
|**Azure DDoS 保護標準支援**|是|否|

## <a name="next-steps"></a>後續步驟

- 檢閱 [Azure 防火牆管理員部署概觀](deployment-overview.md)
- 了解[安全虛擬中樞](secured-virtual-hub.md)。