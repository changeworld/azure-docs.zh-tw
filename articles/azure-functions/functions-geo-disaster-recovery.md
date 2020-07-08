---
title: Azure Functions 異地嚴重損壞修復和高可用性
description: 如何在 Azure Functions 中使用地理區域來進行冗余和故障切換。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79080227"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions 異地嚴重損壞修復

當整個 Azure 區域或資料中心遇到停機時間時，請務必在不同的區域中繼續處理。  本文將說明一些您可以用來部署函式的策略，以允許進行嚴重損壞修復。

## <a name="basic-concepts"></a>基本概念

Azure Functions 在特定區域中執行。  若要取得更高的可用性，您可以將相同的功能部署到多個區域。  在多個區域中，您可以讓函數以*主動/主動*模式或*主動/被動*模式執行。  

* 主動/主動。 這兩個區域都是作用中和接收事件（重複或 rotationally）。 針對 HTTPS 函式，建議使用主動/主動搭配 Azure Front 門板。
* 主動/被動。 一個區域是作用中和接收事件，而次要資料庫處於閒置狀態。  當需要容錯移轉時，次要區域會啟動並接管處理。  這對於非 HTTP 函式（如服務匯流排和事件中樞）是建議的做法。

如需多重區域部署的詳細資訊，請參閱如何[在多個區域中執行應用程式](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)。

## <a name="activeactive-for-https-functions"></a>HTTPS 函式的主動/主動

若要達成函式的主動/主動部署，它需要一些可協調這兩個區域之間事件的元件。  針對 HTTPS 函式，這項協調會使用[Azure Front 門板](../frontdoor/front-door-overview.md)來完成。  Azure Front 大門可以在多個區域功能之間路由傳送和迴圈配置資源 HTTPS 要求。  它也會定期檢查每個端點的健全狀況。  如果區域函式停止回應健康狀態檢查，Azure Front 門板會將它從輪替中取出，只將流量轉送至狀況良好的函式。  

![Azure Front 和 Function 的架構](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>非 HTTPS 函式的主動/主動

您仍然可以達到非 HTTPS 功能的主動/主動部署。  不過，您需要考慮兩個區域之間的互動或協調方式。  如果您將相同的函式應用程式部署到兩個區域中，每個都在相同的服務匯流排佇列上觸發，則會在將佇列的取消佇列中作為競爭取用者。  雖然這表示每個訊息只會由其中一個實例處理，但這也表示單一服務匯流排上仍有單一失敗點。  如果您部署兩個服務匯流排的佇列（一個在主要區域中，一個在次要區域中），而這兩個函式應用程式指向其區域佇列，則挑戰現在會出現在這兩個區域之間如何散佈佇列訊息。  通常這表示每個發行者都會嘗試將訊息發佈到*這兩個*區域，而每個訊息都是由兩個作用中函數應用程式所處理。  雖然這會建立主動/主動模式，但它會針對計算的重複程度以及資料合併的時間或方式建立其他挑戰。  基於這些理由，建議非 HTTPS 觸發程式使用主動/被動模式。

## <a name="activepassive-for-non-https-functions"></a>非 HTTPS 函式的主動/被動

主動/被動提供單一函式來處理每個訊息的方法，但會提供一種機制，以在發生損毀時故障切換到次要區域。  Azure Functions 與[Azure 服務匯流排異地](../service-bus-messaging/service-bus-geo-dr.md)復原和[Azure 事件中樞異地](../event-hubs/event-hubs-geo-dr.md)復原一起運作。

使用 Azure 事件中樞觸發程式做為範例，主動/被動模式會包含下列各項：

* 部署到主要和次要區域的 Azure 事件中樞。
* 已啟用異地災難，以配對主要和次要事件中樞。  這也會建立「別名」，您可以用來連線到事件中樞並從主要切換至次要，而不需要變更連接資訊。
* 同時部署到主要和次要區域的函數應用程式。
* 函式應用程式會針對其各自的事件中樞，在*直接*（非別名）連接字串上觸發。 
* 「事件中樞」的「發行者」應該發行至別名連接字串。 

![主動-被動範例架構](media/functions-geo-dr/active-passive.png)

在容錯移轉之前，傳送至共用別名的發行者會路由至主要事件中樞。  主要的函式應用程式只會以獨佔方式接聽主要事件中樞。  次要函式應用程式將會是被動和閒置。  一旦起始容錯移轉，傳送至共用別名的發行者現在會路由至次要事件中樞。  次要函式應用程式現在會變成作用中狀態，並自動開始觸發。  對次要區域的有效容錯移轉可以完全從事件中樞驅動，只有在個別事件中樞為作用中時，函式才會變成使用中狀態。

深入瞭解使用[服務匯流排](../service-bus-messaging/service-bus-geo-dr.md)和[事件中樞](../event-hubs/event-hubs-geo-dr.md)進行容錯移轉的相關資訊和考慮。

## <a name="next-steps"></a>後續步驟

* [建立 Azure Front](../frontdoor/quickstart-create-front-door.md)
* [事件中樞容錯移轉考慮](../event-hubs/event-hubs-geo-dr.md#considerations)
