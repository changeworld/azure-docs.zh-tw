---
title: Azure 防火牆管理員的架構選項有哪些？
description: 使用中樞虛擬網路或安全的虛擬中樞架構搭配 Azure 防火牆管理員進行比較和對比。
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444572"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Azure 防火牆管理員的架構選項有哪些？

Azure 防火牆管理員可以針對兩種網路架構類型提供安全性管理：

- **受保護的虛擬中樞**

   [Azure 虛擬 WAN 中樞](../virtual-wan/virtual-wan-about.md#resources)是受 Microsoft 管理的資源，可讓您輕鬆地建立中樞和輪輻架構。 當安全性和路由原則與這類中樞相關聯時，就稱為 *[受保護的虛擬中樞](secured-virtual-hub.md)* 。 
- **中樞虛擬網路**

   這是您自行建立和管理的標準 Azure 虛擬網路。 當安全性原則與這類中樞相關聯時，就稱為「*中樞虛擬網路*」。 目前僅支援 Azure 防火牆原則。 您可以將包含工作負載伺服器和服務的輪輻虛擬網路對等互連。 您也可以在獨立虛擬網路中管理未對等互連到任何輪輻的防火牆。

## <a name="comparison"></a>比較

下表比較這兩個架構選項，並可協助您決定哪一個最適合您組織的安全性需求：


|  |**中樞虛擬網路**|**受保護的虛擬中樞**  |
|---------|---------|---------|
|**基礎資源**     |虛擬網路|虛擬 WAN 中樞|
|**中樞 & 輪輻**     |使用虛擬網路對等互連|使用中樞虛擬網路連線自動化|
|**內部內部部署連線能力**     |VPN 閘道最多 10 Gbps 和30個 S2S 連線;ExpressRoute|更具擴充性的 VPN 閘道高達 20 Gbps 和1000的 S2S 連線;Express Route|
|**使用 SDWAN 的自動分支連接**      |不支援|支援|
|**每個區域的中樞**     |每個區域有多個虛擬網路|每個區域的單一虛擬中樞。 具有多個虛擬 Wan 的多個中樞|
|**Azure 防火牆–多個公用 IP 位址**      |由客戶提供|自動產生。 正式推出。|
|**Azure 防火牆可用性區域**     |支援|不適用於預覽。 正式推出|
|**具有協力廠商安全性即服務合作夥伴的 Advanced Internet security**     |客戶已建立和受控 VPN 連線至選擇的合作夥伴服務|透過受信任的安全性合作夥伴流程和合作夥伴管理體驗來自動化|
|**集中式路由管理以將流量路由傳送至中樞**     |客戶管理的使用者定義路由|使用 BGP 支援|
|**應用程式閘道上的 Web 應用程式防火牆** |虛擬網路中支援|目前在輪輻網路中支援|
|**網路虛擬裝置**|虛擬網路中支援|目前在輪輻網路中支援|

## <a name="next-steps"></a>後續步驟

- 檢閱 [Azure 防火牆管理員預覽部署概觀](deployment-overview.md)
- 了解[安全虛擬中樞](secured-virtual-hub.md)。