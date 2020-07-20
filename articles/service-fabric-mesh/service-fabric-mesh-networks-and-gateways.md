---
title: Azure Service Fabric 網路功能簡介
description: 深入了解 Service Fabric Mesh 中的網路、閘道及智慧型流量路由。
author: dkkapur
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: dc793e2991783cc9b7b46d92fcc8e0267feb529b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75459129"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Service Fabric Mesh 應用程式中的網路功能簡介
本文會說明不同類型的負載平衡器、閘道如何將具有您應用程式的網路連線至其他網路，以及流量如何在您應用程式的服務之間路由。

## <a name="layer-4-vs-layer-7-load-balancers"></a>第 4 層與 第 7 層負載平衡器
在用來進行網路功能的 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)中，負載平衡可在不同的層級中執行，通常是在第 4 層 (L4) 和第 7 (L7) 層中。  一般而言，負載平衡器有兩種類型：

- L4 負載平衡器會在網路傳輸層運作，該層負責處理封包傳遞，但不會理會封包內容。 負載平衡器只會檢查封包標題，因此平衡準則會限制在 IP 位址和連接埠。 例如，用戶端會對負載平衡器建立 TCP 連線。 負載平衡器會終止連線 (藉由直接回應 SYN)、選取後端，然後對後端建立新的 TCP 連線 (傳送新的 SYN)。 L4 負載平衡器通常只會在 L4 TCP/UDP 連線或工作階段的層級上運作。 因此，負載平衡會將位元組重新導向至四周圍，並確保來自相同工作階段的位元組最後會在相同的後端。 L4 負載平衡器不會知道移動位元組的任何應用程式詳細資料。 位元組可能是任何應用程式通訊協定。

- L7 負載平衡器會在應用程式層運作，該層負責處理每個封包的內容。 負載平衡器會檢查封包內容，因為它了解 HTTP、HTTPS 或 WebSockets 等通訊協定。 這會讓負載平衡器有能力執行進階的路由。 例如，用戶端會對負載平衡器建立單一 HTTP/2 TCP 連線。 然後，負載平衡器會繼續建立兩個後端連線。 當用戶端將兩個 HTTP/2 串流傳送至負載平衡器時，串流 1 會傳送至後端 1，而串流 2 會傳送到後端 2。 因此，即使有多路用戶端具有極不同的要求負載，這些用戶端也會在後端中達到有效的平衡。 

## <a name="networks-and-gateways"></a>網路和閘道
在 [Service Fabric 資源模型](service-fabric-mesh-service-fabric-resources.md)中，網路資源是個別部署的資源，與那些會參考它的應用程式或服務資源 (相依性) 無關。 這會用來為您開放給網際網路的應用程式建立網路。 不同應用程式的多個服務可以是屬於同一個網路。 此私人網路會由 Service Fabric 來建立和管理，而且不是 Azure 虛擬網路 (VNET)。 應用程式可以動態地從網路資源中新增和移除，藉以啟用和停用 VNET 連線。 

閘道會用來橋接兩個網路。 閘道資源會部署 [Envoy proxy](https://www.envoyproxy.io/)，以提供適用於任何通訊協定的 L4 路由，以及適用於進階 HTTP(S) 應用程式路由的 L7 路由。 閘道會將流量從外部網路路由至您的網路，並決定流量要路由至哪個服務。  外部網路可以是開放式網路 (基本上是公用網際網路) 或 Azure 虛擬網路，並可讓您連線至您的其他 Azure 應用程式及資源。 

![網路和閘道][Image1]

使用 `ingressConfig` 建立網路資源時，公用 IP 會指派給網路資源。 公用 IP 將會繫結至網路資源的存留期。

建立 Mesh 應用程式後，其應該參考現有網路資源。 您可以在輸入組態中新增新的公用連接埠，或移除現有的連接埠。 如果應用程式資源正在參考某個網路資源，則會無法刪除該網路資源。 刪除應用程式時，也會一併移除網路資源。

## <a name="next-steps"></a>後續步驟 
若要深入了解 Service Fabric Mesh，請閱讀下列概觀：
- [Service Fabric Mesh 概觀](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png