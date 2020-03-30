---
title: Azure 功能地質災害恢復和高可用性
description: 如何使用地理區域進行冗余和在 Azure 函數中容錯移轉。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080227"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure 函數異地災害復原

當整個 Azure 區域或資料中心遇到停機時，計算在不同區域中繼續處理至關重要。  本文將解釋可用於部署函數以允許災害復原的一些策略。

## <a name="basic-concepts"></a>基本概念

Azure 函數在特定區域中運行。  要獲得更高的可用性，可以將相同的函數部署到多個區域。  在多個區域中時，可以使函數以*主動/主動*模式或*主動/被動*模式運行。  

* 主動/主動。 這兩個區域都是活動狀態和接收事件（重複或旋轉）。 建議將 HTTPS 功能與 Azure 前門結合使用。
* 主動/被動。 一個區域處於活動狀態並接收事件，而次要區域處於空閒狀態。  當需要容錯移轉時，次要區域將啟動並接管處理。  建議使用非 HTTP 功能（如服務匯流排和事件中心）。

有關如何[在多個區域中運行應用](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)，瞭解有關多區域部署的詳細資訊。

## <a name="activeactive-for-https-functions"></a>用於 HTTPS 功能的活動/活動

要實現函數的主動/主動部署，需要一些元件來協調兩個區域之間的事件。  對於 HTTPS 函數，此協調使用[Azure 前門](../frontdoor/front-door-overview.md)完成。  Azure 前門可以在多個區域函數之間路由和迴圈 HTTPS 請求。  它還定期檢查每個終結點的運行狀況。  如果區域函數停止回應運行狀況檢查，Azure 前門將使其退出旋轉，並且僅將流量轉發到正常運行的函數。  

![Azure 前門和函數的體系結構](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>非 HTTPS 函數的活動/活動

您仍然可以為非 HTTPS 函數實現主動/主動部署。  但是，您需要考慮這兩個區域如何相互交互或協調。  如果將同一功能應用部署到兩個區域（每個區域觸發同一服務匯流排佇列上），它們將充當取消該佇列排隊的相互競爭的消費者。  雖然這意味著每條消息僅由其中一個實例處理，但它也意味著單個服務匯流排上仍有一個故障點。  如果部署兩個服務匯流排佇列（一個在主區域，一個在次要區域），並且兩個函數應用指向其區域佇列，則現在的挑戰在於佇列消息在兩個區域之間的分配方式。  通常，這意味著每個發行者都嘗試將消息發佈到*兩個區域*，並且每條消息都由兩個活動函數應用處理。  雖然這會產生主動/主動模式，但它會在計算重複以及資料合併時間或方式上產生其他挑戰。  出於這些原因，建議非 HTTPS 觸發器使用主動/被動模式。

## <a name="activepassive-for-non-https-functions"></a>非 HTTPS 函數的主動/被動

主動/被動僅為單個函數提供處理每條消息的方法，但提供了在發生災難時容錯移轉到次要區域的機制。  Azure 函數與[Azure 服務匯流排地理恢復](../service-bus-messaging/service-bus-geo-dr.md)和[Azure 事件中心地理恢復一](../event-hubs/event-hubs-geo-dr.md)起工作。

以 Azure 事件中心觸發器為例，主動/被動模式將涉及以下內容：

* Azure 事件中心部署到主區域和次要區域。
* 啟用地理災難對主事件和輔助事件中心進行配對。  這還會創建一個"別名"，可用於連接到事件中心，並在不更改連接資訊的情況下從主集線切換到輔助中心。
* 部署到主區域和次要區域的函數應用。
* 函數應用正在其各自的事件中心*的直接*（非別名）連接字串上觸發。 
* 事件中心的發行者應發佈到別名連接字串。 

![主動-被動示例體系結構](media/functions-geo-dr/active-passive.png)

在容錯移轉之前，發送到共用別名的發行者將路由到主事件中心。  主函數應用專門偵聽主事件中心。  輔助函數應用將是被動和空閒的。  啟動容錯移轉後，發送到共用別名的發行者現在將路由到輔助事件中心。  輔助函數應用現在將變為活動狀態並開始自動觸發。  有效的容錯移轉到次要區域可以完全從事件中心驅動，功能僅在相應的事件中心處於活動狀態時變為活動狀態。

閱讀有關[使用服務匯流排](../service-bus-messaging/service-bus-geo-dr.md)和[事件集線器](../event-hubs/event-hubs-geo-dr.md)進行容錯移轉的資訊和注意事項的詳細資訊。

## <a name="next-steps"></a>後續步驟

* [創建 Azure 前門](../frontdoor/quickstart-create-front-door.md)
* [事件中心容錯移轉注意事項](../event-hubs/event-hubs-geo-dr.md#considerations)
