---
title: 什麼是 Azure 防火牆管理器體系結構選項？
description: 使用 Azure 防火牆管理器比較和對比使用中心虛擬網路或安全虛擬中心體系結構。
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444572"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>什麼是 Azure 防火牆管理器體系結構選項？

Azure 防火牆管理器可以為兩種網路架構類型提供安全管理：

- **安全虛擬中心**

   [Azure 虛擬 WAN 中心](../virtual-wan/virtual-wan-about.md#resources)是 Microsoft 管理的資源，可讓您輕鬆創建中心架構和分支體系結構。 當安全和路由策略與此類中心關聯時，它稱為*[安全虛擬中心](secured-virtual-hub.md)*。 
- **中心虛擬網路**

   這是您自己創建和管理的標準 Azure 虛擬網路。 當安全性原則與此類中心關聯時，它稱為*中心虛擬網路*。 此時，僅支援 Azure 防火牆策略。 您可以對包含工作負載伺服器和服務的分支虛擬網路進行對等。 您還可以管理獨立虛擬網路中未與任何分支對等的防火牆。

## <a name="comparison"></a>比較

下表比較了這兩個體系結構選項，並可以説明您確定哪個選項適合組織的安全要求：


|  |**中樞虛擬網路**|**安全虛擬中心**  |
|---------|---------|---------|
|**基礎資源**     |虛擬網路|虛擬廣域網路中心|
|**中心&分支**     |使用虛擬網路對等互連|使用集線器虛擬網路連接實現自動化|
|**上置連接**     |VPN 閘道高達 10 Gbps 和 30 S2S 連接;快速路線|更可擴展的 VPN 閘道高達 20 Gbps 和 1000 S2S 連接;快速路線|
|**使用 SDWAN 實現自動分支連接**      |不支援|支援|
|**每個區域的樞紐**     |每個區域有多個虛擬網路|每個區域的單個虛擬中心。 多個具有多個虛擬 WAN 的集線器|
|**Azure 防火牆 = 多個公共 IP 位址**      |由客戶提供|自動生成。 由 GA 提供。|
|**Azure 防火牆可用性區域**     |支援|預覽版不可用。 由 GA 提供|
|**以協力廠商安全作為服務合作夥伴的高級互聯網安全性**     |客戶建立和託管 VPN 連接，以合作夥伴服務為首選|通過受信任的安全合作夥伴流程和合作夥伴管理經驗實現自動化|
|**集中路由管理，將流量路由到樞紐**     |客戶管理的使用者定義路由|支援使用 BGP|
|**應用程式閘道上的 Web 應用程式防火牆** |虛擬網路中受支援|目前在分支網路中支援|
|**網路虛擬設備**|虛擬網路中受支援|目前在分支網路中支援|

## <a name="next-steps"></a>後續步驟

- 檢閱 [Azure 防火牆管理員預覽部署概觀](deployment-overview.md)
- 瞭解[安全虛擬中心](secured-virtual-hub.md)。