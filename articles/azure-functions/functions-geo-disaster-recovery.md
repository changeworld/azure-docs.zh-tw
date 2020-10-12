---
title: Azure Functions 異地災難復原和高可用性
description: 如何使用地理區域進行冗余以及在 Azure Functions 中容錯移轉。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 446a76b823ce3592a83d0c2f898041951361b47e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506276"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions 異地災難復原

當整個 Azure 區域或資料中心遇到停機時，計算在不同區域中繼續處理是很重要的。  本文將說明一些您可以用來部署功能以允許災難復原的策略。

## <a name="basic-concepts"></a>基本概念

Azure Functions 在特定區域中執行。  若要取得更高的可用性，您可以將相同的功能部署到多個區域。  在多個區域中，您可以讓函式在 *主動/主動* 模式或 *主動/被動* 模式中執行。  

* 主動/主動。 這兩個區域都處於作用中狀態，且 (重複或 rotationally) 來接收事件。 針對搭配 Azure Front Door 的 HTTPS 函式，建議使用主動/主動。
* 主動/被動。 一個區域處於作用中狀態，並在次要閒置時接收事件。  需要容錯移轉時，會啟動次要區域並接管處理。  這對非 HTTP 函式（例如服務匯流排和事件中樞）是建議的做法。

如需多區域部署的詳細資訊，請參閱如何 [在多個區域中執行應用程式](/azure/architecture/reference-architectures/app-service-web-app/multi-region) 。

## <a name="activeactive-for-https-functions"></a>適用于 HTTPS 函式的主動/主動

若要達到主動/主動部署的函式，它需要可協調兩個區域之間事件的部分元件。  對於 HTTPS 函式，這項協調是使用 [Azure Front Door](../frontdoor/front-door-overview.md)完成。  Azure Front Door 可以在多個區域函數之間路由傳送和迴圈配置資源 HTTPS 要求。  它也會定期檢查每個端點的健康情況。  如果區域函式停止回應健康狀態檢查，Azure Front Door 將會將其從迴圈中移出，而且只會將流量轉送到狀況良好的函式。  

![Azure Front Door 和函數的架構](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>適用于非 HTTPS 函式的主動/主動

您仍然可以針對非 HTTPS 函式達到主動/主動部署。  不過，您必須考慮兩個區域彼此互動或互相協調的方式。  如果您將相同的函式應用程式部署到兩個區域中，每個區域都會在相同的服務匯流排佇列上觸發，它們會在取消佇列的佇列時，扮演競爭取用者。  這表示每個訊息只會由其中一個實例處理，也表示單一服務匯流排上仍有單一失敗點。  如果您部署兩個服務匯流排佇列 (一個在主要區域中，一個在次要區域中) ，而兩個函式應用程式指向其區域佇列，則挑戰現在是在兩個區域之間散發佇列訊息的方式。  通常，這表示每個發行者都會嘗試將訊息發佈到 *這兩個* 區域，而每個訊息都是由作用中的函式應用程式處理。  雖然這會建立主動/主動模式，但是它會在重複計算以及資料合併的時間或方式方面創造其他挑戰。  基於這些理由，建議非 HTTPS 觸發程式使用主動/被動模式。

## <a name="activepassive-for-non-https-functions"></a>非 HTTPS 函式的主動/被動

主動/被動提供一種方法來處理每個訊息，但會提供一種機制，以在發生災難時容錯移轉至次要區域。  Azure Functions 可與 [Azure 服務匯流排異地](../service-bus-messaging/service-bus-geo-dr.md) 復原和 [Azure 事件中樞異地](../event-hubs/event-hubs-geo-dr.md)復原一起運作。

使用 Azure 事件中樞觸發程式作為範例時，主動/被動模式會涉及下列各項：

* 部署到主要和次要區域的 Azure 事件中樞。
* 啟用異地災難以配對主要和次要事件中樞。  這也會建立「別名」，您可以用來連線到「事件中樞」，並從「主要」切換為「次要」，而不需變更連接資訊。
* 部署到主要和次要區域的函式應用程式。
* 函數應用程式會在其個別事件中樞的 *直接* (非別名) 連接字串上觸發。 
* 事件中樞的發行者應發佈至別名連接字串。 

![主動-被動範例架構](media/functions-geo-dr/active-passive.png)

在容錯移轉之前，傳送至共用別名的發行者會路由傳送至主要事件中樞。  主要函式應用程式會以獨佔方式接聽主要事件中樞。  第二個函式應用程式將會是被動和閒置。  一旦啟動容錯移轉，傳送至共用別名的發行者現在將會路由傳送至次要事件中樞。  次要函式應用程式現在會變成作用中狀態，並會自動開始觸發。  有效的容錯移轉至次要區域，可完全從事件中樞驅動，而只有在個別事件中樞處於作用中狀態時，函式才會變成作用中。

深入瞭解使用 [服務匯流排](../service-bus-messaging/service-bus-geo-dr.md) 和 [事件中樞](../event-hubs/event-hubs-geo-dr.md)進行容錯移轉的資訊和考慮。

## <a name="next-steps"></a>接下來的步驟

* [建立 Azure Front Door](../frontdoor/quickstart-create-front-door.md)
* [事件中樞容錯移轉考慮](../event-hubs/event-hubs-geo-dr.md#considerations)
