---
title: Azure 網路架構
description: 本文提供 Microsoft Azure 基礎結構網路的一般描述。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567857"
---
# <a name="azure-network-architecture"></a>Azure 網路架構
Azure 網路架構可提供從網際網路連線至 Azure 資料中心的能力。 在 Azure 上部署 (IaaS、PaaS 和 SaaS) 的任何工作負載都是利用 Azure 資料中心網路。

## <a name="network-topology"></a>網路拓撲
Azure 資料中心的網路架構包含下列元件：

- Edge 網路
- 廣域網路
- 區域閘道網路
- 資料中心網路

![Azure 網路圖表](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>網路元件
網路元件的簡短描述。

- Edge 網路

   - Microsoft 網路和其他網路之間的分界點 (例如，網際網路、商業網路) 
   - 提供 Azure 的網際網路和 [ExpressRoute](../../expressroute/expressroute-introduction.md) 對等互連

- 廣域網路

   - 涵蓋全球的 Microsoft 智慧型骨幹網路
   - 提供[Azure 區域](https://azure.microsoft.com/global-infrastructure/geographies/)之間的連線能力

- 區域閘道

   - Azure 區域中所有資料中心的匯總點
   - 在 Azure 區域內的資料中心之間提供大規模的連線 (例如，每個資料中心有多一百 terabits) 

- 資料中心網路

   - 提供資料中心內具有低超額訂閱頻寬之伺服器之間的連線能力

上述網路元件的設計目的是要提供最大的可用性，以支援 always on、永遠可用的雲端商務。 從實體層面一直到控制通訊協定，都是在網路中設計和內建的。

## <a name="datacenter-network-resiliency"></a>資料中心網路復原
讓我們來說明使用資料中心網路的復原設計原則。

資料中心網路是 [Clos 網路](https://en.wikipedia.org/wiki/Clos_network)的修改版本，為雲端規模的流量提供高的雙層級頻寬。 使用大量商用裝置來建立網路，以降低個別硬體故障造成的影響。 這些裝置會策略性地位於不同的實體位置，具有個別的電源和冷卻領域，以降低環境事件的影響。  在控制平面上，所有網路裝置都會以 OSI 模型第3層路由模式執行，以消除流量迴圈的歷程記錄問題。 不同層之間的所有路徑都在作用中，以使用相同成本的多重路徑 (ECMP) 路由來提供高冗余和頻寬。

下圖說明資料中心網路是由不同層的網路裝置所建立。 圖中的橫條代表網路裝置群組，可提供冗余和高頻寬的連線能力。

![資料中心網路](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>後續步驟
若要深入了解 Microsoft 為協助保護 Azure 基礎結構執行了哪些動作，請參閱：

- [Azure 設施、廠房以及實體安全性](physical-security.md)
- [Azure 基礎結構可用性](infrastructure-availability.md)
- [Azure 資訊系統元件與界限](infrastructure-components.md)
- [Azure 生產網路](production-network.md)
- [Azure SQL Database 安全性功能](infrastructure-sql.md)
- [Azure 生產環境運作與管理](infrastructure-operations.md)
- [Azure 基礎結構監視](infrastructure-monitoring.md)
- [Azure 基礎結構完整性](infrastructure-integrity.md)
- [Azure 客戶資料保護](protection-customer-data.md)
