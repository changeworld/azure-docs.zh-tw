---
title: Azure Front 門板-使用 Azure 的應用程式傳遞套件進行負載平衡 |Microsoft Docs
description: 本文可協助您瞭解 Azure 如何使用其應用程式傳遞套件來建議負載平衡
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 44af14a01e7b045b7abb6a84db89a67f3dd22445
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80875277"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>使用 Azure 的應用程式傳遞套件進行負載平衡

## <a name="introduction"></a>簡介
Microsoft Azure 提供多個全域和區域服務，以管理您的網路流量分散和負載平衡的方式：流量管理員、前門、應用程式閘道和 Load Balancer。  除了 Azure 的眾多區域 (region) 與區域 (zonal) 架構之外，搭配使用這些服務也能讓您建置強健且可調整的高效能應用程式。

![應用程式傳遞套件 ][1]
 
這些服務被分類為兩個類別：
1. **全域負載平衡服務**（例如流量管理員和 Front）會在跨雲端或甚至是混合式內部部署服務的區域後端中，散發您的終端使用者流量。 全域負載平衡會將您的流量路由傳送到距離您最接近的服務後端，並對服務可靠性或效能的變更做出反應，以為您的使用者維持永遠可用的最大化效能。 
2. **區域負載平衡服務**（例如 Standard Load Balancer 或應用程式閘道）可讓您將虛擬網路（vnet）內的流量分散到您的虛擬機器（vm）或區域內的區域性服務端點。

將應用程式中的全域和區域服務結合，可提供端對端可靠、高效能且安全的方式，將流量傳送至您的 IaaS、PaaS 或內部部署服務，並將其流向您的使用者。 在下一節中，我們會逐一描述這些服務。

## <a name="global-load-balancing"></a>全域負載平衡
**流量管理員**提供全域 DNS 負載平衡。 它會查看連入 DNS 要求並根據客戶所選取的路由原則，以健康情況良好的後端做出回應。 路由方法的選項如下︰
- 效能路由傳送，以根據延遲將要求者傳送到最接近的後端。
- 優先順序路由，可將所有流量導向後端，並以其他後端做為備份。
- 加權循環配置資源路由傳送，它會根據指派給各後端的加權散發流量。
- 地理路由傳送，以確保位於特定地理區域的要求者會被導向對應至那些區域的後端 (例如，所有來自西班牙的要求者都應該被導向法國中部 Azure 區域)
- 子網路路由傳送，能允許您將 IP 位址範圍對應至後端，使來自那些範圍的要求會被傳送至指定的後端 (例如，與一般的使用者相比，所有從您企業總部的 IP 位址範圍連線的使用者都應該會取得不同的 Web 內容)

用戶端直接連線至該後端。 Azure 流量管理員會在偵測到後端健康情況不良時，將用戶端重新導向至另一個健康情況良好的執行個體。 若要深入了解此服務，請參閱 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)文件。

**Azure Front 門板**提供動態網站加速（DSA），包括全域 HTTP 負載平衡。  它會查看針對最接近服務後端/區域的連入 HTTP 要求路由，以取得指定的主機名稱、URL 路徑與已設定的規則。  
Front Door 會於 Microsoft 網路的邊緣終止 HTTP 要求，並主動探查以偵測應用程式或基礎結構的健康情況或延遲變更。  Front Door 接著一律會將流量路由傳送到最快的可用 (健康情況良好) 的後端。 若要深入了解此服務，請參閱 Front Door 的[路由架構](front-door-routing-architecture.md)詳細資料及[流量路由方法](front-door-routing-methods.md)。

## <a name="regional-load-balancing"></a>區域負載平衡
應用程式閘道會以服務形式提供應用程式傳遞控制器 (ADC)，為您的應用程式提供各種第 7 層負載平衡功能。 它可讓客戶將 CPU 密集的 TLS 終止卸載至應用程式閘道，以優化 web 伺服陣列的生產力。 其他第 7 層路由功能包括循環配置連入流量、以 Cookie 為基礎的工作階段同質、URL 路徑型路由，以及在單一應用程式閘道背後代管多個網站的能力。 應用程式閘道可以設定為連結網際網路的閘道、內部專用閘道或兩者混合。 應用程式閘道完全由 Azure 管理、可調整且可用性極高。 它提供一組豐富的診斷和記錄功能，很好管理。
負載平衡器是 Azure SDN 堆疊不可或缺的一部分，針對所有 UDP 與 TCP 通訊協定提供高效能、低延遲的第 4 層負載平衡服務。 它會管理輸入及輸出連線。 您可以設定公用和內部負載平衡端點，並使用 TCP 和 HTTP 健全狀況探查選項定義規則，將輸入連線對應至後端集區目的地，以管理服務可用性。


## <a name="choosing-a-global-load-balancer"></a>選擇全域負載平衡器
針對全域路由在流量管理員與 Azure Front Door 之間挑選全域負載平衡器時，您應該考慮這兩個服務之間的相似及相異之處。   兩個服務皆提供
- **多重地理區域備援**：如果某個區域故障，服務會將流量順暢地路由傳送到最近的區域，而不需要應用程式擁有者介入。
- **最接近的區域路由傳送**：流量會自動路由傳送到最接近的區域

</br>下表說明流量管理員與 Azure Front 門之間的差異：</br>

| 流量管理員 | Azure Front Door |
| --------------- | ------------------------ |
|**任何通訊協定**：由於流量管理員是在 DNS 層運作，因此您可以路由傳送任何類型的網路流量 (HTTP、TCP、UDP 等)。 | **HTTP 加速：** 具有 Front 門板的流量會在 Microsoft 網路的邊緣進行 proxy 處理。  因此，HTTP （S）要求會看到延遲和輸送量的改善，以減少 TLS 協調的延遲，並使用從 AFD 到您應用程式的熱連接。|
|**內部部署路由：** 使用 DNS 層的路由時，流量一律會從點對點。  從您的分公司到內部部署資料中心的路由可以採用直接路徑;即使在您自己的網路上使用流量管理員。 | **獨立的可調整姓**：由於 Front Door 能搭配 HTTP 要求運作，因此可以根據規則及每個應用程式微服務的健康情況，將針對不同 URL 路徑的要求路由傳送到不同的後端/區域服務集區 (微服務)。|
|**計費格式**：以 DNS 為基礎的計費會根據您的使用者進行調整，而針對具有較多使用者的服務，能在較高使用量的情況下透過平穩狀態來減少成本。 |**內嵌的安全性**：Front Door 可提供如速率限制與 IP ACL 處理等規則，使您能在流量抵達應用程式之前保護您的後端。 

</br>基於 Front Door 針對 HTTP 工作負載所提供的效能、可操作性與安全性的優點，我們建議客戶針對其 HTTP 工作負載使用 Front Door。    流量管理員與 Front Door 可以同時用來處理應用程式的所有流量。 

## <a name="building-with-azures-application-delivery-suite"></a>使用 Azure 的應用程式傳遞套件進行建置 
我們建議所有網站、API 與服務皆應進行異地備援處理，並從距離它們最接近 (延遲最低) 的位置將流量傳遞給其使用者。  結合來自流量管理員、Front 門、應用程式閘道和 Load Balancer 的服務，可讓您建立地理位置和 zonally 冗余，以最大化可靠性、規模及效能。

在下表中，我們會描述結合使用所有上述服以建置全域 Web 服務的範例服務。   在此範例中，架構設計人員使用流量管理員來使檔案與物件的傳遞能路由傳送到全域後端，並使用 Front Door 來將符合 /store/* 模式的 URL 路徑全域路由傳送到已移轉至 App Service 的服務，同時將所有其他要求路由傳送到區域應用程式閘道。

在該區域中，針對其 IaaS 服務，應用程式開發人員決定任何符合 /images/* 模式的 URL，都是由不同於其餘 Web 伺服陣列的專用 VM 集區所提供。

此外，提供動態內容的預設 VM 集區需要與高可用性叢集上裝載的後端資料庫聯繫。 整個部署是透過 Azure Resource Manager 來設定。

下圖顯示此案例的架構︰

![應用程式傳遞套件詳細架構][2] 

> [!NOTE]
> 這個範例只是 Azure 所提供的許多可能的負載平衡服務設定的其中一個。 您可以混合搭配使用流量管理員、Front Door、應用程式閘道與負載平衡器，以滿足您的負載平衡需求。 例如，如果不需要 TLS/SSL 卸載或第7層處理，您可以使用 Load Balancer 來取代應用程式閘道。


## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
