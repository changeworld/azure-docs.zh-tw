---
title: Azure 對等互連服務常見問題
description: 瞭解 Microsoft Azure 對等互連服務常見問題
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 55c5e6c5b718dc2de295b9b4418ddc8607a69f8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871358"
---
# <a name="peering-service-faq"></a>對等互連服務常見問題

本文說明 Azure 對等互連服務連線的最常見問題。


**問。誰是目標客戶？**

A. 目標客戶是使用網際網路作為傳輸來連線到 Microsoft cloud 的企業。

**問。客戶是否可以註冊多個提供者的對等互連服務？** 

A. 是的，客戶可以使用相同區域或不同區域中的多個提供者來註冊對等互連服務，但不能使用相同的前置詞。

**問。客戶是否可以為每個地理區域的網站選取唯一的 ISP？**

A. 是的，客戶可以這樣做。 選取符合您商務和操作需求的每個區域合作夥伴 ISP。

**問。什麼是 Microsoft Edge PoP？**

A. 它是 Microsoft 與其他網路互連的實體位置。 在 Microsoft Edge 的 PoP 位置中，會託管 Azure Front Door 和 Azure CDN 等服務。 如需詳細資訊，請參閱 [Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-features)。

## <a name="peering-service-unique-characteristics"></a>對等互連服務：唯一的特性

**問。對等互連服務與一般網際網路存取有何不同？**

A. 已向 Microsoft 對等互連服務註冊的合作夥伴，與 Microsoft 合作，為 Microsoft 服務提供優化的路由和可靠的連線能力。  

**問。對等互連服務與 ExpressRoute 有何不同？**

A. Azure ExpressRoute 是一或多個客戶位置的私人專用連線。 當對等互連服務提供優化的公用連線能力，且不支援任何私用連線時，它也會為本機網際網路突破提供優化的連線能力。

## <a name="next-steps"></a>接下來的步驟

- 若要深入瞭解對等互連服務，請參閱對 [等互連服務的總覽](about.md)。
- 若要尋找服務提供者，請參閱對 [等互連服務夥伴和位置](location-partners.md)。
- 若要讓對等互連服務連線上線，請參閱上 [架對等互連服務](onboarding-model.md)。
- 若要註冊對等互連服務連線，請參閱 [註冊對等服務連接-Azure 入口網站](azure-portal.md)。
- 若要測量遙測，請參閱[測量連線遙測](measure-connection-telemetry.md)。