---
title: Azure Front Door-使用 Azure 的應用程式傳遞套件進行負載平衡 |Microsoft Docs
description: 本文可協助您瞭解 Azure 如何使用其應用程式傳遞套件來建議負載平衡
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 50e047325ad17710794b1640715ab1938373fe85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019458"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>使用 Azure 的應用程式傳遞套件進行負載平衡

## <a name="introduction"></a>簡介
Microsoft Azure 提供各種全域和區域服務來管理網路流量的分散和負載平衡： 

* 應用程式閘道
* Front Door 
* Load Balancer  
* 流量管理員

除了 Azure 的許多區域和區域性架構之外，一起使用這些服務可以讓您建立強大、可擴充且高效能的應用程式。

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="應用程式傳遞套件":::
 
這些服務被分類為兩個類別：
1. **全域負載平衡服務** （例如流量管理員和 Front Door）會將來自您的終端使用者的流量分散至整個區域後端、跨雲端，甚至是混合式內部部署服務。 全域負載平衡會將您的流量路由傳送至最接近的服務後端，並回應服務可靠性的變更，以維護使用者的 alwayson 可用性和高效能。 
1. **區域負載平衡服務** （例如負載平衡器和應用程式閘道）可讓您將流量分散到虛擬網路中的虛擬機器 (vm)  (vnet) 或區域內的服務端點。

當您結合這些全域和區域服務時，您的應用程式將受益于從您的終端使用者傳送至 IaaS、PaaS 或內部部署服務的可靠且安全的端對端流量。 在下一節中，我們會逐一描述這些服務。

## <a name="global-load-balancing"></a>全域負載平衡
**流量管理員** 提供全域 DNS 負載平衡。 它會查看傳入的 DNS 要求，並遵循客戶所選取的路由原則，以狀況良好的後端回應。 路由方法的選項如下︰
- * * 效能路由將要求傳送至最接近延遲的最接近後端。
- **優先順序路由** 將所有流量導向至後端，並將其他後端作為備份。
- **加權迴圈** 配置資源路由會根據指派給每個後端的加權來分配流量。
- **地理路由** 可確保來自特定地理區域的要求，會由對應于這些區域的後端來處理。 例如 (，所有來自西班牙的要求都應該導向至法國中部 Azure 區域) 
- **子網路由** 可讓您將 IP 位址範圍對應至後端，以便將那些 ip 的連入要求傳送到特定的後端。  (例如，從公司總部的 IP 位址範圍連線的任何使用者，都應該取得與一般使用者不同的 web 內容) 

用戶端直接連線至該後端。 Azure 流量管理員會在偵測到後端健康情況不良時，將用戶端重新導向至另一個健康情況良好的執行個體。 若要深入了解此服務，請參閱 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)文件。

**Azure Front Door** 提供動態網站加速 (DSA) 包括全域 HTTP 負載平衡。  它會查看針對最接近服務後端/區域的連入 HTTP 要求路由，以取得指定的主機名稱、URL 路徑與已設定的規則。  
Front Door 會於 Microsoft 網路的邊緣終止 HTTP 要求，並主動探查以偵測應用程式或基礎結構的健康情況或延遲變更。  Front Door 接著一律會將流量路由傳送到最快的可用 (健康情況良好) 的後端。 若要深入了解此服務，請參閱 Front Door 的[路由架構](front-door-routing-architecture.md)詳細資料及[流量路由方法](front-door-routing-methods.md)。

## <a name="regional-load-balancing"></a>區域負載平衡
應用程式閘道會以服務形式提供應用程式傳遞控制器 (ADC)，為您的應用程式提供各種第 7 層負載平衡功能。 它可讓客戶將 CPU 密集的 TLS 終止卸載至應用程式閘道，以優化 web 伺服陣列的生產力。 其他額外的第7層路由功能也包括迴圈配置傳入流量、以 cookie 為基礎的會話親和性、URL 路徑型路由，以及在單一應用程式閘道後方裝載多個網站的能力。 應用程式閘道可以設定為面向網際網路的端點、僅供內部使用的端點，或兩者的組合。 應用程式閘道完全由 Azure 管理，可提供您的擴充性和高可用性。 它提供一組豐富的診斷和記錄功能，很好管理。

負載平衡器是 Azure SDN 堆疊不可或缺的一部分，可為所有 UDP 和 TCP 通訊協定提供高效能、低延遲的第4層負載平衡服務。 您可以藉由定義將輸入連線對應至後端集區的規則，來設定公用或內部負載平衡的端點。 使用 TCP 或 HTTPS 的健康情況探查監視，可協助您管理服務可用性。

## <a name="choosing-a-global-load-balancer"></a>選擇全域負載平衡器
針對全域路由在流量管理員與 Azure Front Door 之間挑選全域負載平衡器時，您應該考慮這兩個服務之間的相似及相異之處。   兩個服務皆提供
- **多地理位置冗余：** 如果有一個區域服務中斷，則流量會順暢地路由傳送至最接近的區域，而不需要應用程式擁有者介入。
- **最接近的區域路由傳送**：流量會自動路由傳送到最接近的區域

</br>下表說明流量管理員與 Azure Front Door 之間的差異：</br>

| 流量管理員 | Azure Front Door |
| --------------- | ------------------------ |
|**任何通訊協定：** 由於流量管理員是在 DNS 層運作，因此您可以路由傳送任何類型的網路流量;HTTP、TCP、UDP 等等。 | **HTTP 加速：** 使用 Front Door，流量會在 Microsoft 網路的邊緣進行 proxy 處理。 HTTP/S 要求會看到延遲和輸送量改進，可減少 TLS 協商的延遲。|
|**內部部署路由：** 使用 DNS 層的路由時，流量一律會從點到點。  從您的分公司路由傳送至內部部署資料中心可以採用直接路徑;甚至是在您自己的網路上使用流量管理員。 | **獨立的擴充性：** 由於 Front Door 可搭配 HTTP 要求運作，不同 URL 路徑的要求可以根據規則及每個應用程式微服務的健康情況，路由傳送至不同的後端/區域服務集區 (微服務) 。|
|**計費格式**：以 DNS 為基礎的計費會根據您的使用者進行調整，而針對具有較多使用者的服務，能在較高使用量的情況下透過平穩狀態來減少成本。 |**內嵌的安全性**：Front Door 可提供如速率限制與 IP ACL 處理等規則，使您能在流量抵達應用程式之前保護您的後端。 

</br>我們建議客戶針對其 HTTP 工作負載使用 Front Door，因為 HTTP 適用于 Front Door 的效能、互通性和安全性優勢。 流量管理員與 Front Door 可以同時用來處理應用程式的所有流量。 

## <a name="building-with-azures-application-delivery-suite"></a>使用 Azure 的應用程式傳遞套件進行建置 
我們建議所有網站、Api、服務都是地理位置多餘的，因此可以盡可能將流量從最接近的位置傳遞給其使用者。  結合多個負載平衡服務可讓您建立地理和區域冗余，以將可靠性和效能最大化。

在下圖中，我們將說明使用所有這些服務組合來建立全域 web 服務的範例架構。 架構設計人員使用流量管理員，將流量路由傳送至全域後端，以進行檔案和物件傳遞。 使用 Front Door 時，將符合模式/store/* 的 URL 路徑全域路由至它們已遷移至 App Service 的服務。 最後，將所有其他要求路由至區域應用程式閘道。

在 IaaS 服務的每個區域中，應用程式開發人員已決定任何符合模式/images/* 的 Url 都是從專用的 Vm 集區提供。 此 Vm 集區不同于 web 伺服陣列的其餘部分。

此外，提供動態內容的預設 VM 集區需要與高可用性叢集上裝載的後端資料庫聯繫。 整個部署是透過 Azure Resource Manager 設定。

下圖顯示此案例的架構︰

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="應用程式傳遞套件詳細架構":::

> [!NOTE]
> 這個範例只是 Azure 所提供的許多可能的負載平衡服務設定的其中一個。 您可以混合搭配使用流量管理員、Front Door、應用程式閘道與負載平衡器，以滿足您的負載平衡需求。 例如，如果不需要 TLS/SSL 卸載或第7層處理，Load Balancer 可以用來取代應用程式閘道。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
