---
title: Azure ExpressRoute： QoS 需求
description: 此頁面提供設定及管理 QoS 的詳細需求。 討論範圍包括「商務用 Skype」/語音服務。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: cherylmc
ms.openlocfilehash: debc5d91478d0a5c3cc16c7b09f5713ba09b467e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74080090"
---
# <a name="expressroute-qos-requirements"></a>ExpressRoute QoS 需求
商務用 Skype 具有各種工作負載，其所要求的 QoS 處理方式各有差異。 如果您打算透過 ExpressRoute 取用語音服務，應遵守以下所述的需求。

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> QoS 需求僅適用於 Microsoft 對等項目。 在 Azure 公用對等和 Azure 私用對等上收到的網路流量的 DSCP 值將會重設為 0。 
> 
> 

下表提供 Microsoft 團隊和商務用 Skype 所使用的 DSCP 標記清單。 如需詳細資訊，請參閱 [管理適用於商務用 Skype 的 QoS](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS) 。

| **傳輸類別** | **處理方式 (DSCP 標示)** | **Microsoft 團隊和商務用 Skype 工作負載** |
| --- | --- | --- |
| **語音** |EF (46) |Skype/Microsoft 團隊/Lync voice |
| **互動式** |AF41 (34) |影片、VBSS |
| |AF21 (18) |APP 共用 | 
| **預設值** |AF11 (10) |檔案傳輸 |
| |CS0 (0) |任何其他項目 |

* 您應將工作負載分類，並標記正確的 DSCP 值。 遵循 [這裡](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) 所提供的指引，以了解如何在您的網路中設定 DSCP 標示。
* 您應在網路中設定並支援多個 QoS 佇列。 語音必須是獨立類別，並接收[RFC 3246](https://www.ietf.org/rfc/rfc3246.txt)中指定的 EF 處理。 
* 您可以決定適用於每個流量類別的佇列機制、壅塞偵測原則和頻寬配置。 但是必須保留適用於商務用 Skype 工作負載的 DSCP 標示。 如果您使用以上未列出的 DSCP 標示 (例如 AF31 (26))，就必須先將此 DSCP 值重寫為 0，才能將封包傳送給 Microsoft。 Microsoft 只會傳送使用上表所列之 DSCP 值標記的封包。 

## <a name="next-steps"></a>後續步驟
* 請參閱[路由](expressroute-routing.md)和 [NAT](expressroute-nat.md) 的需求。
* 請參閱下列連結，以設定 ExpressRoute 連線。
  
  * [建立 ExpressRoute 線路](expressroute-howto-circuit-classic.md)
  * [設定路由](expressroute-howto-routing-classic.md)
  * [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-classic.md)

