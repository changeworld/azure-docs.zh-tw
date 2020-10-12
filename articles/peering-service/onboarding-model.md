---
title: Azure 對等互連服務上線模型
description: 瞭解如何上架 Azure 對等互連服務
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871398"
---
# <a name="onboarding-peering-service-model"></a>上架對等互連服務模型

對等互連服務的上架程式是由下列兩個模型所組成：

 - 上架對等互連服務連線

 - 上架對等互連服務連線遙測

上述模型的動作計畫如下所述：

| **Step** | **動作**| **您會獲得的服務**|
|-----------|---------|---------|---------|
|1|客戶可從連線夥伴布建連線， (不會與 Microsoft) 互動。 |一種網際網路提供者，它已與 Microsoft 妥善連線，並符合與 Microsoft 的高效能和可靠連線的技術需求。  |
|2 (選用) |客戶會在 Azure 入口網站中註冊位置。 位置的定義方式如下： ISP/IXP 名稱、客戶網站的實體位置 (狀態層級) 、服務提供者或企業提供給位置的 IP 首碼。  |遙測：網際網路路由監視，從 Microsoft 到使用者最接近邊緣 POP 位置的流量優先順序。 |



## <a name="onboarding-peering-service-connection"></a>上架對等互連服務連線

若要將對等互連服務連線上線，請執行下列動作：

使用網際網路服務提供者 (ISP) 或網際網路 Exchange (IX) 合作夥伴取得對等互連服務，以將您的網路與 Microsoft 網路連線。

確定連線 [提供者](location-partners.md) 與 Microsoft 合作，以進行對等互連服務。 

## <a name="onboarding-peering-service-connection-telemetry"></a>上架對等互連服務連線遙測

客戶可以選擇其遙測資料（例如 BGP 路由分析），以在存取 Microsoft 網路時監視網路延遲和效能。 這可以藉由在 Azure 入口網站中註冊連接來達成。

若要將對等互連服務連線遙測上線，客戶必須在 Azure 入口網站中註冊對等互連服務連線。 請參閱 [註冊對等互連服務-Azure 入口網站](azure-portal.md) 以瞭解程式。

之後，您可以在 [這裡](measure-connection-telemetry.md)參考來測量遙測資料。

## <a name="next-steps"></a>接下來的步驟

若要深入瞭解如何註冊對等互連服務連線的逐步程式，請參閱 [註冊對等互連服務-Azure 入口網站](azure-portal.md)。

若要瞭解量值連線遙測，請參閱 [測量連接遙測](measure-connection-telemetry.md)。
