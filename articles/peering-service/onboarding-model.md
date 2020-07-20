---
title: Azure 對等服務上架模型
description: 深入瞭解如何將 Azure 對等服務上架
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871398"
---
# <a name="onboarding-peering-service-model"></a>上架對等互連服務模型

對等互連服務的上執行緒序由兩個模型組成，如下所示：

 - 登入對等互連服務連線

 - 登入對等互連服務連線遙測

上述模型的動作計畫如下所述：

| **Step** | **動作**| **您會獲得的服務**|
|-----------|---------|---------|---------|
|1|客戶布建連線協力電腦的連線能力（不與 Microsoft 互動）。 |網際網路提供者，其已妥善連線到 Microsoft，並符合 Microsoft 的高效能和可靠連接技術需求。  |
|2（選擇性）|客戶會在 Azure 入口網站中註冊位置。 位置是由下列方式定義： ISP/IXP 名稱、客戶網站的實體位置（狀態層級）、服務提供者或企業提供給位置的 IP 首碼。  |遙測：網際網路路由監視，從 Microsoft 到最接近邊緣 POP 位置的流量優先順序。 |



## <a name="onboarding-peering-service-connection"></a>登入對等互連服務連線

若要讓對等互連服務連線上線，請執行下列動作：

與網際網路服務提供者（ISP）或網際網路交換（IX）合作夥伴合作，以取得對等服務，以將您的網路連線到 Microsoft 網路。

請確定連線[提供者](location-partners.md)已與 Microsoft 合作，以進行對等互連服務。 

## <a name="onboarding-peering-service-connection-telemetry"></a>登入對等互連服務連線遙測

客戶可以選擇其遙測資料（例如 BGP 路線分析）來監視存取 Microsoft 網路時的網路延遲和效能。 這可以藉由在 Azure 入口網站中註冊連接來達成。

若要讓對等互連服務連線遙測上線，客戶必須在 Azure 入口網站中註冊對等互連服務連接。 如要瞭解此程式，請參閱[註冊對等互連服務-Azure 入口網站](azure-portal.md)。

接著，您可以在[這裡](measure-connection-telemetry.md)參考來測量遙測。

## <a name="next-steps"></a>後續步驟

若要瞭解如何註冊對等互連服務連線的逐步程式，請參閱[註冊對等互連服務-Azure 入口網站](azure-portal.md)。

若要深入瞭解量值連線遙測，請參閱[測量連接遙測](measure-connection-telemetry.md)。
