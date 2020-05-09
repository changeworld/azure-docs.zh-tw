---
title: Azure Front Door | Microsoft Docs
description: 本文提供 Azure Front Door 的概觀。 了解其是否為平衡應用程式使用者流量負載的最佳選擇。
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 19deb763c8e750490854892c90d0293d3e209c09
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515793"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Azure Front Door 的規則引擎是什麼？ 

規則引擎可讓您自訂在邊緣處理 HTTP 要求的方式，並且讓您更能充分掌控 Web 應用程式的行為。 Azure Front Door 的規則引擎包含數個重要功能，包括：

- 以標頭為基礎的路由 – 根據要求標頭、cookie 和查詢字串內容中的模式來路由傳送要求。
- 以參數為基礎的路由 – 利用一系列的比對條件 (包括 post 引數、查詢字串、cookie 和要求方法)，以根據 HTTP 要求參數來路由傳送要求。 
- 路由組態覆寫： 
    - 使用重新導向功能將 301/302/307/308 重新導向傳回給用戶端，以重新導向至新的主機名稱、路徑和通訊協定。 
    - 使用轉送功能來重寫要求 URL 路徑，而不需要執行傳統重新導向並將要求轉送至已設定後端集區中的適當後端。 
    - 自訂您的快取組態並根據比對條件，以動態方式將路由從轉送變更為快取。 

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="architecture"></a>架構 

規則引擎會在邊緣處理要求。 設定規則引擎後，當要求達到您的 Front Door 端點時，會先執行 WAF，然後再執行與您的前端/網域相關聯的規則引擎組態。 執行規則引擎組態時，表示父代路由規則已經相符。 是否執行規則引擎組態中每個規則內的所有動作，受制於該規則內的所有比對條件都要符合。 如果要求不符合規則引擎組態中的任何條件，則會執行預設路由規則。 

例如，在以下組態中，規則引擎會設定為附加回應標頭，以在條件符合時，變更快取控制項的最大存留期。 

![回應標頭動作](./media/front-door-rules-engine/rules-engine-architecture-3.png)

在另一個範例中，我們發現規則引擎已設定為在比對條件 (裝置類型) 為 True 時，將使用者傳送至行動版的網站。 

![路由組態覆寫](./media/front-door-rules-engine/rules-engine-architecture-1.png)

在這兩個範例中，若所有比對條件都不符合，則會執行所指定的路由規則。 

## <a name="terminology"></a>詞彙 

利用 AFD 規則引擎，您可建立一系列的規則引擎組態，而每個組態都是由一組規則所組成。 以下概述在設定規則引擎時，您將會遇到的一些實用術語。 

- *規則引擎組態*：已套用至單一路由規則的一組規則。 每個組態的限制為 5 個規則。 您最多可以建立 10 個組態。 
- *規則引擎規則*：最多由 10 個比對條件和 5 個動作所組成的規則。
- *比對條件*：有多個比對條件可用來剖析傳入要求。 一個規則最多可以包含 10 個比對條件。 比對條件會使用 **AND** 運算子進行評估。 您可以在[這裡](front-door-rules-engine-match-conditions.md)找到完整的比對條件清單。 
- *動作*：動作可決定傳入要求會發生什麼情況 - 要求 / 回應標頭動作、轉送、重新導向和重寫現在均可供使用。 一個規則最多可包含 5 個動作；不過，一個規則只能包含 1 個路由組態覆寫。  您可以在[這裡](front-door-rules-engine-actions.md)找到完整的動作清單。


## <a name="next-steps"></a>後續步驟

- 了解如何設定您的第一個[規則引擎組態](front-door-tutorial-rules-engine.md)。 
- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
