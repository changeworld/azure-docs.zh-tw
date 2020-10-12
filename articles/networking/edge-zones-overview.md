---
title: 關於 Azure Edge 區域預覽
description: 深入瞭解 Microsoft 的 edge 計算供應專案： Azure Edge 區域。
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 07/07/2020
ms.author: ganesr
ms.openlocfilehash: 68aa3342ab09be73a82f4f896ffdff99d15a5350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329562"
---
# <a name="about-azure-edge-zone-preview"></a>關於 Azure Edge 區域預覽

Azure Edge 區域是 Microsoft Azure 的一系列產品，可讓使用者接近使用者的資料處理。 您可以將 Vm、容器和其他選取的 Azure 服務部署到邊緣區域，以解決應用程式的低延遲和高輸送量需求。

Edge 區域的一般使用案例包括：

- 機器人中的即時命令和控制。
- 透過人工智慧和機器學習的即時分析和推斷。
- 機器視覺。
- 混合現實和 VDI 案例的遠端呈現。
- 沉浸式多人遊戲。
- 媒體串流處理和內容傳遞。
- 監視和安全性。

有三種類型的 Azure Edge 區域：

- Azure Edge Zones
- 具有電訊廠商的 Azure Edge 區域
- Azure 私人 Edge 區域

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zones

![Azure Edge Zones](./media/edge-zones-overview/edge-zones.png "Azure Edge Zones")

Azure Edge 區域是 Azure 的小型使用量延伸模組，放在與 Azure 區域遠相距的擴展中心內。 Azure Edge 區域支援 Vm、容器，以及一組選取的 Azure 服務，可讓您執行接近使用者的延遲和高輸送量應用程式。 Azure Edge 區域是 Microsoft 全球網路的一部分。 它們可在邊緣區域（接近使用者）執行的應用程式之間，提供安全、可靠、高頻寬的連線能力。 並提供在 Azure 區域內執行的一組完整的 Azure 服務。 Azure Edge 區域是由 Microsoft 所擁有及運作。 您可以使用一組相同的 Azure 工具和相同的入口網站來管理及部署服務到邊緣區域。

一般使用案例包括：

- 遊戲和遊戲串流。
- 媒體串流處理和內容傳遞。
- 透過人工智慧和機器學習的即時分析和推斷。
- 混合現實的呈現。

Azure Edge 區域將可在下列 metro 區域中使用：

- 美國紐約
- 加利福尼亞州洛杉磯
- 佛羅里達州邁阿密

如需詳細資訊，[請洽詢 Edge 區域小組](https://aka.ms/EdgeZones)。

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>具有電訊廠商的 Azure Edge 區域

![具有電訊廠商的 Edge 區域](./media/edge-zones-overview/edge-carrier.png "具有電訊廠商的 Edge 區域")

具有電訊廠商的 azure Edge 區域是 Azure 的小型使用量擴充功能，這些擴充功能放在擴展中心的行動操作員的資料中心內。 具有貨運公司基礎結構的 Azure Edge 區域會從行動操作員的5G 網路下一個躍點。 這項放置可提供小於10毫秒的延遲給行動裝置的應用程式。

具有電訊廠商的 Azure Edge 區域會部署在行動操作員的資料中心，並聯機到 Microsoft 全球網路。 它們在執行接近使用者的應用程式之間提供安全、可靠、高頻寬的連線能力。 並提供在 Azure 區域內執行的一組完整的 Azure 服務。 開發人員可以使用同一組熟悉的工具來建立服務，並將其部署到邊緣區域中。

一般使用案例包括：

- 遊戲和遊戲串流。
- 媒體串流處理和內容傳遞。
- 透過人工智慧和機器學習的即時分析和推斷。
- 混合現實的呈現。
- 連接的汽車。
- Telepresence-醫學。

Edge 區域會與下列操作員合作提供：

- 在&T (亞特蘭大、達拉斯和洛杉磯) 

針對連線到5G 網路的優化且可擴充的應用程式進行開發的 Isv，現在可以在建立和實驗超低延遲平臺、行動裝置和連線案例時，使用 Azure Edge 區域的新洛杉磯預覽位置，以及&T。 報名早期採用者計畫，以利用安全、高頻寬的連線能力。

如需詳細資訊，[請洽詢 Edge 區域小組](https://aka.ms/EdgeZones)。

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure 私人 Edge 區域

![私人 Edge 區域](./media/edge-zones-overview/private-edge.png "私人 Edge 區域")

Azure 私人 Edge 區域是放在內部部署環境的小型 Azure 擴充功能。 Azure 私人 Edge 區域是以 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) 平臺為基礎。 它可讓您低延遲存取內部部署的計算和儲存體服務。 私人 Edge 區域也可讓您將應用程式從 Isv 和虛擬化網路功能部署 (VNFs) 為 [Azure 受控應用程式](https://azure.microsoft.com/services/managed-applications/) ，以及內部部署的虛擬機器和容器。 這些 VNFs 可以包括行動包核心、路由器、防火牆和 SD WAN 設備。 Azure 私用 Edge 區域隨附雲端原生協調流程解決方案，可讓您從 Azure 入口網站管理 VNFs 和應用程式的生命週期。

Azure 私人 Edge 區域可讓您使用您在 Azure 中建立及部署應用程式時所使用的相同熟悉工具，在內部部署開發和部署應用程式。

它也可讓您：

-  (私人 LTE、私用 5G) 執行私人行動網路。
- 執行防火牆等安全性功能。
- 使用相同私人 Edge 區域設備上的 SD WAN 設備，在多個分支和 Azure 之間擴充您的內部部署網路，並從 Azure 管理它們。

一般使用案例包括：

- 機器人中的即時命令和控制。
- 使用人工智慧和機器學習的即時分析和推斷。
- 機器視覺。
- 混合現實和 VDI 案例的遠端呈現。
- 監視和安全性。

我們有一 VNF 廠商、Isv 和 MSP 合作夥伴的豐富生態系統，可啟用使用私人 Edge 區域的端對端解決方案。 如需詳細資訊，[請洽詢私用 Edge 區域小組](https://aka.ms/EdgeZonesPartner)。

### <a name="private-edge-zone-partners"></a><a name="private-edge-partners"></a>私人 Edge 區域合作夥伴

![私人 Edge 區域合作夥伴](./media/edge-zones-overview/partners.png "私人 Edge 區域合作夥伴")

#### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>虛擬化網路功能 (VNFs) 

##### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>行動網路的虛擬化演進套件核心 (vEPC) 

- [Affirmed 網路](https://www.affirmednetworks.com/)
- [Celona](https://www.celona.io/azure-edge)
- [Druid 軟體](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Nokia 數位自動化雲端](https://www.dac.nokia.com/)

##### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>行動電話廣播合作夥伴

- [Celona](https://www.celona.io/azure-edge)
- [Commscope Ruckus](https://support.ruckuswireless.com/)

##### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN 廠商

- [128 Technology](https://www.128technology.com/)
- [NetFoundry](https://netfoundry.io/)
- [從 Nokia Nuage 網路](https://www.nuagenetworks.net/)
- [反向網路](https://www.versa-networks.com/)
- [VMware SD-WAN （依 Velocloud）](https://www.velocloud.com/)

##### <a name="router-vendors"></a><a name="router-vendors"></a>路由器廠商

- [Arista](https://www.arista.com/)

##### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>防火牆廠商

- [Palo Alto Networks](https://www.paloaltonetworks.com/)

##### <a name="managed-solutions-providers-mobile-operators-and-global-system-integrators-gsis"></a><a name="msp-mobile"></a>受控解決方案提供者：行動操作員和全球系統整合者 (GSIs) 

| GSIs 和運算子 | 電信業者 |
| --- | --- |
| Amdocs                       | Etisalat             |
| 美國塔式               | NTT Communications   |
| CenturyLink                  | Proximus             |
| Expeto                       | Rogers               |
| 同盟無線           | SK 電信           |
| Infosys                      | Telefonica           |
| 技術 Mahindra                | Telstra              |
|                              | Vodafone             |

[請聯絡私用 Edge 區域小組](https://aka.ms/EdgeZonesPartner) ，以取得如何成為合作夥伴的相關資訊。

### <a name="private-edge-zone-solutions"></a><a name="solutions-private-edge"></a>私人 Edge 區域解決方案

#### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>私人 Edge 區域上的私人行動網路

![私人 Edge 區域上的私人行動網路](./media/edge-zones-overview/mobile-networks.png "私人 Edge 區域上的私人行動網路")

您現在可以在私人 Edge 區域上部署私人行動網路。 私用行動網路可提供高度低延遲、高容量，以及商務關鍵應用程式所需的可靠且安全的無線網路。

私人行動網路可以啟用如下的案例：
- 自動引導式車輛的命令和控制 (AGVs) 在倉儲中。
- 智慧型工廠中機器人之間的即時通訊。
- 增強的現實和虛擬實境 edge 應用程式。

虛擬化演進的封包核心 (vEPC) network function 是私人行動網路的大腦。 您現在可以在私人 Edge 區域上部署 vEPC。 如需私人 Edge 區域中可用的 vEPC 合作夥伴清單，請參閱 [VEPC isv](#vEPC)。

在私人 Edge 區域上部署私人行動網路解決方案需要其他元件，例如行動存取點、SIM 卡，以及路由器等其他 VNFs。 存取授權或未授權的頻譜對於設定私人行動網路而言非常重要。 而且您可能需要協助進行 RF 規劃、實體配置、安裝及支援。 如需合作夥伴清單，請參閱行動 [裝置廣播合作夥伴](#mobile-radio)。

Microsoft 提供的合作夥伴生態系統，可協助處理此程式的所有層面。 合作夥伴可以協助規劃網路、購買所需的裝置、設定硬體，以及管理 Azure 的設定。 與 Microsoft 緊密整合的一組經過驗證的夥伴，可確保您的解決方案可靠且容易使用。 您可以專注于您的核心案例，並依賴 Microsoft 及其合作夥伴來協助其他人。

#### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-私人 Edge 區域上的 WAN

![SD-私人 Edge 區域上的 WAN](./media/edge-zones-overview/sd-wan.png "SD-私人 Edge 區域上的 WAN")

SD-WAN 可讓您建立具備下列優點的企業級廣域網路 (Wan) ：

- 增加的頻寬
- 高效能存取雲端
- 服務插入
- 可靠性
- 原則管理
- 廣泛的網路可見度

SD-WAN 提供以較低的擁有成本從冗余中央控制器協調的無縫分公司連線能力。
SD-私用 Edge 區域上的 WAN 可讓您從以 capex 為中心的模型移至軟體即服務 (SaaS) 模型，以降低 IT 預算。 您可以使用您選擇的 SD-WAN 夥伴、協調器或控制器來啟用新的服務，並立即將其傳播到整個網路中。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請洽詢下列小組：

* [Edge 區域小組](https://aka.ms/EdgeZones)
* [私用 Edge 區域小組，成為合作夥伴](https://aka.ms/EdgeZonesPartner)
