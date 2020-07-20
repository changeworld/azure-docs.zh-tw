---
title: 關於 Azure Edge 區域預覽
description: 深入瞭解 Microsoft 的 edge 計算供應專案： Azure Edge 區域。
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 07/07/2020
ms.author: ganesr
ms.openlocfilehash: be113fec596d4730d55403b6ce4dbd6d2709e273
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260073"
---
# <a name="about-azure-edge-zone-preview"></a>關於 Azure Edge 區域預覽

Azure Edge 區域是 Microsoft Azure 的供應專案系列，可讓資料處理接近使用者。 您可以將 Vm、容器和其他選取的 Azure 服務部署到邊緣區域，以解決應用程式的低延遲和高輸送量需求。

邊緣區域的一般使用案例包括：

- 機器人中的即時命令與控制。
- 透過人工智慧和機器學習，進行即時分析和推斷。
- 機器視覺。
- 混合現實和 VDI 案例的遠端呈現。
- 沉浸式多人遊戲。
- 媒體串流處理和內容傳遞。
- 監視和安全性。

Azure Edge 區域有三種類型：

- Azure 邊緣區域
- 具有貨運的 Azure 邊緣區域
- Azure 私人邊緣區域

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure 邊緣區域

![Azure 邊緣區域](./media/edge-zones-overview/edge-zones.png "Azure 邊緣區域")

Azure 邊緣區域是 Azure 的小型存放區延伸模組，放在與 Azure 區域遠距離的人口中心。 Azure 邊緣區域支援 Vm、容器和一組選取的 Azure 服務，可讓您執行接近使用者的延遲敏感和高輸送量應用程式。 Azure 邊緣區域是 Microsoft 全球網路的一部分。 它們會在靠近使用者的邊緣區域執行的應用程式之間，提供安全、可靠、高頻寬的連線能力。 而且它們會提供一組完整的 azure 服務，在 Azure 區域內執行。 Azure Edge 區域由 Microsoft 所擁有及運作。 您可以使用一組相同的 Azure 工具和相同的入口網站來管理和部署服務到邊緣區域。

典型的使用案例包括：

- 遊戲和遊戲串流。
- 媒體串流處理和內容傳遞。
- 透過人工智慧和機器學習，進行即時分析和推斷。
- 混合現實的呈現。

Azure Edge 區域將在下列 metro 區域內上市：

- 美國紐約
- 加利福尼亞州洛杉磯
- 佛羅里達州邁阿密

如需詳細資訊，[請聯絡 Edge 區域小組](https://aka.ms/EdgeZones)。

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>具有貨運的 Azure 邊緣區域

![具有貨運的邊緣區域](./media/edge-zones-overview/edge-carrier.png "具有貨運的邊緣區域")

具有電訊廠商的 azure 邊緣區域是 Azure 的小型存放區延伸模組，放在行動操作員的資料中心內。 具有承運商基礎結構的 Azure Edge 區域會放在離行動操作員的5G 網路的一個躍點。 這項放置可提供從行動裝置到應用程式少於10毫秒的延遲。

具有貨運公司的 Azure Edge 區域會部署在行動操作員的資料中心，並聯機至 Microsoft 全球網路。 它們會在接近使用者的應用程式之間，提供安全、可靠、高頻寬的連線能力。 而且它們會提供一組完整的 azure 服務，在 Azure 區域內執行。 開發人員可以使用一組相同的熟悉工具來建立服務，並將其部署到邊緣區域。

典型的使用案例包括：

- 遊戲和遊戲串流。
- 媒體串流處理和內容傳遞。
- 透過人工智慧和機器學習，進行即時分析和推斷。
- 混合現實的呈現。
- 已連接的汽車。
- Telepresence-醫學。

邊緣區域將與下列操作員合作提供：

- 在&T (亞特蘭大、達拉斯和洛杉磯)  

針對連線到5G 網路的優化且可擴充的應用程式使用的 Isv，現在可以使用 Azure 邊緣區域的新洛杉磯預覽位置，並在建立和實驗超低延遲平臺、行動和連線的案例時，于&T。 註冊早期採用者計畫，以利用安全、高頻寬的連線能力。

如需詳細資訊，[請聯絡 Edge 區域小組](https://aka.ms/EdgeZones)。

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure 私人邊緣區域

![私人邊緣區域](./media/edge-zones-overview/private-edge.png "私人邊緣區域")

Azure 私用邊緣區域是放置於內部部署的小型 Azure 擴充功能。 Azure 私用 Edge 區域是以[Azure Stack 邊緣](https://azure.microsoft.com/products/azure-stack/edge/)平臺為基礎。 它可讓您在內部部署環境中，對計算和儲存體服務進行低延遲存取。 私用邊緣區域也可讓您將來自 Isv 和虛擬化網路功能的應用程式部署 (VNFs) 作為[Azure 受控應用程式](https://azure.microsoft.com/services/managed-applications/)以及內部部署的虛擬機器和容器。 這些 VNFs 可以包括行動封包核心、路由器、防火牆和 SD WAN 設備。 Azure 私用 Edge 區域隨附雲端原生協調流程解決方案，可讓您從 Azure 入口網站管理 VNFs 和應用程式的生命週期。

Azure 私用 Edge 區域可讓您使用在 Azure 中建立及部署應用程式時所使用的相同熟悉工具，在內部部署環境開發和部署應用程式。 

它也可讓您： 

-  (私人的 LTE、私用 5G) 執行私人行動網路。
- 執行防火牆之類的安全性功能。
- 在相同的私人邊緣區域設備上使用 SD WAN 應用裝置，並從 Azure 進行管理，以跨多個分支和 Azure 延伸您的內部部署網路。

典型的使用案例包括：

- 機器人中的即時命令與控制。
- 使用人工智慧和機器學習進行即時分析和推斷。
- 機器視覺。
- 混合現實和 VDI 案例的遠端呈現。
- 監視和安全性。

我們有豐富的 VNF 廠商、Isv 和 MSP 合作夥伴生態系統，可啟用使用私人邊緣區域的端對端解決方案。 如需詳細資訊，[請洽詢私人 Edge 區域小組](https://aka.ms/EdgeZonesPartner)。

### <a name="private-edge-zone-partners"></a><a name="private-edge-partners"></a>私人 Edge 區域合作夥伴

![私人 Edge 區域合作夥伴](./media/edge-zones-overview/partners.png "私人邊緣區域合作夥伴")

#### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a> (VNFs) 的虛擬化網路功能

##### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>行動網路 (vEPC) 的虛擬化進化封包核心

- [Affirmed 網路](https://www.affirmednetworks.com/)
- [Druid 軟體](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Nokia 數位自動化雲端](https://www.dac.nokia.com/)

##### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Mobile 收音機合作夥伴

- [Commscope Ruckus](https://support.ruckuswireless.com/)

##### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN 廠商

- [NetFoundry](https://netfoundry.io/)
- [從 Nokia Nuage 網路](https://www.nuagenetworks.net/)
- [VMware SD-依 Velocloud 的 WAN](https://www.velocloud.com/)

##### <a name="router-vendors"></a><a name="router-vendors"></a>路由器廠商

- [Arista](https://www.arista.com/)

##### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>防火牆廠商

- [Palo Alto Networks](https://www.paloaltonetworks.com/)

##### <a name="managed-solutions-providers-mobile-operators-and-global-system-integrators-gsis"></a><a name="msp-mobile"></a>受控解決方案提供者：行動操作員和全域系統整合器 (GSIs) 

| GSIs 和運算子 | 電信業者 |
| --- | --- |
| Amdocs                       | Etisalat             |
| 美屬塔式               | NTT Communications   |
| CenturyLink                  | Proximus             |
| Expeto                       | Rogers               |
| 聯盟無線           | SK 電信           |
| Infosys                      | Telefonica           |
| 技術 Mahindra                | Telstra              |
|                              | Vodafone             |

如需如何成為合作夥伴的詳細資訊，[請洽詢私人 Edge 區域小組](https://aka.ms/EdgeZonesPartner)。

### <a name="private-edge-zone-solutions"></a><a name="solutions-private-edge"></a>私人 Edge 區域解決方案

#### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>私人邊緣區域上的私用行動網路

![私人邊緣區域上的私用行動網路](./media/edge-zones-overview/mobile-networks.png "私人邊緣區域上的私用行動網路")

您現在可以在私人邊緣區域上部署私人行動網路。 私人行動網路可實現非常低延遲、高容量，以及商務關鍵應用程式所需的可靠且安全的無線網路。 

私人行動網路可以啟用下列案例： 
- 自動化引導式車輛的命令與控制 (AGVs) 在倉儲中。 
- 智慧型處理站中的機器人之間的即時通訊。
- 增強的現實和虛擬實境 edge 應用程式。

虛擬化進化封包核心 (vEPC) 網路功能，是私用行動網路的大腦。 您現在可以在私人邊緣區域上部署 vEPC。 如需私人邊緣區域上可用的 vEPC 合作夥伴清單，請參閱[VEPC isv](#vEPC)。

在私人邊緣區域上部署私人行動網路解決方案，需要其他元件，例如行動存取點、SIM 卡和其他 VNFs （例如路由器）。 存取授權或未經授權的頻譜，對於設定私人行動網路而言至關重要。 而且您可能需要有 RF 規劃、實體版面配置、安裝和支援的協助。 如需合作夥伴清單，請參閱[Mobile 收音機合作夥伴](#mobile-radio)。

Microsoft 提供的合作夥伴生態系統可協助處理此程式的所有層面。 合作夥伴可以協助規劃網路、購買所需的裝置、設定硬體，以及從 Azure 管理設定。 與 Microsoft 緊密整合的一組經過驗證的合作夥伴，確保您的解決方案會可靠且容易使用。 您可以專注于您的核心案例，並依賴 Microsoft 和其合作夥伴協助進行其餘工作。

#### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-私人邊緣區域上的 WAN

![SD-私人邊緣區域上的 WAN](./media/edge-zones-overview/sd-wan.png "SD-私人邊緣區域上的 WAN")
 
SD-WAN 可讓您建立企業級的廣域網路， (具備下列優點的 Wan) ：

- 增加頻寬
- 雲端的高效能存取
- 服務插入
- 可靠性
- 原則管理
- 廣泛的網路可見度
    
SD-WAN 提供順暢的分公司連線，以較低的擁有成本，從冗余的中央控制器協調。
SD-私人邊緣區域上的 WAN 可讓您從以 capex 為中心的模型移至軟體即服務 (SaaS) 模型，以降低 IT 預算。 您可以使用您選擇的 SD-WAN 合作夥伴（orchestrator 或 controller）來啟用新服務，並立即將其傳播到整個網路中。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請洽詢下列小組：

* [Edge 區域小組](https://aka.ms/EdgeZones)
* [私人 Edge 區域小組，成為合作夥伴](https://aka.ms/EdgeZonesPartner)
