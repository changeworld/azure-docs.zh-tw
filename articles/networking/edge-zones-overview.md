---
title: 關於 Azure 邊緣區域 - 預覽
description: 瞭解微軟提供的邊緣計算產品。
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 04/02/2020
ms.author: ganesr
ms.openlocfilehash: aaa849633591bfd34a9fca026c820ec2f9137844
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410845"
---
# <a name="about-azure-edge-zones---preview"></a>關於 Azure 邊緣區域 - 預覽

Azure 邊緣區域是Microsoft Azure 提供的一系列產品,支援靠近用戶的數據處理。 您可以將 VM、容器和其他選定的 Azure 服務部署到邊緣區域,以滿足應用程式的低延遲和高輸送量要求。

邊緣區域的典型用例方案包括:

- 機器人即時指揮與控制
- 使用人工智慧和機器學習進行即時分析和推斷
- 機器視覺
- 混合實式實境和 VDI 機制的遠端
- 沉浸式多人遊戲
- 媒體串流和內容提供
- 監視和安全

Azure 邊緣區域有三個離散產品:

- Azure 邊緣區域
- 帶載波的 Azure 邊緣區域
- Azure 專用邊緣區域

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure 邊緣區域

![邊緣區域](./media/edge-zones-overview/edge-zones.png "邊緣區域")

Azure 邊緣區域是放置在遠離 Azure 區域的人口中心中的 Azure 小範圍擴展。 Azure 邊緣區域支援 VM、容器和一組選定的 Azure 服務,這些服務允許您在接近最終使用者時運行延遲敏感和輸送量密集型應用程式。 Azure 邊緣區域是Microsoft全域網路的一部分,在靠近使用者的邊緣區域運行的應用程式與在Azure區域內運行的完整Azure服務集之間提供安全、可靠和高頻寬連接。 Azure 邊緣區域由 Microsoft 擁有和營運,允許您使用同一組 Azure 工具和門戶來管理服務並將其部署到邊緣區域。

典型用例包括:

- 遊戲與遊戲串流
- 媒體串流和內容提供
- 使用人工智慧和機器學習進行即時分析和推斷
- 混合實境的渲染

Azure 邊緣區域將在以下城市中可用:

- 美國紐約
- 加利福尼亞州洛杉磯
- 佛羅里達州邁阿密

[有關詳細資訊,與邊緣區域團隊](https://aka.ms/EdgeZones)聯繫。

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>帶載波的 Azure 邊緣區域

![帶載波的邊緣區域](./media/edge-zones-overview/edge-carrier.png "帶載波的邊緣區域")

具有載波的 Azure 邊緣區域是放置在行動營運商資料中心中填充中心的 Azure 小佔用空間擴展。 具有營運商基礎結構的 Azure 邊緣區域距離行動營運商的 5G 網路一躍而過,為行動裝置的應用程式提供 10 毫秒以下的延遲。 具有營運商的 Azure 邊緣區域部署在行動營運商的資料中心中,並連接到 Microsoft 全球網路。 它們在使用者附近運行的應用程式和在 Azure 區域內運行的完整 Azure 服務集之間提供安全、可靠和高頻寬的連接。 開發人員可以使用同一組熟悉的工具在邊緣區域中構建和部署服務。

典型用例包括:

- 遊戲與遊戲串流
- 媒體串流和內容提供
- 使用人工智慧和機器學習進行即時分析和推斷
- 混合實境的渲染
- 互聯汽車
- 遠端

邊緣區域將與以下營運商合作提供:

- AT&T(亞特蘭大、達拉斯和洛杉磯)

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure 專用邊緣區域

![專用邊緣區域](./media/edge-zones-overview/private-edge.png "專用邊緣區域")

Azure 專用邊緣區域是放置在本地的 Azure 的較小佔用空間擴展。 Azure 專用邊緣區域基於[Azure 堆疊邊緣](https://azure.microsoft.com/products/azure-stack/edge/)平台,並且允許對本地部署的計算和儲存服務進行低延遲訪問。 專用邊緣區域還允許您將虛擬化網路功能 (VNF)(如行動資料套件核心、路由器、防火牆和 SD-WAN 裝置)以及 ISVS 的應用程式並行部署,以及本地虛擬機器和容器作為[Azure 託管應用程式](https://azure.microsoft.com/services/managed-applications/)。 Azure 專用邊緣區域附帶了雲原生業務流程解決方案,允許您從 Azure 門戶管理虛擬化網路功能 (VNF) 和應用程式的生命週期。

Azure 專用邊緣區域允許您使用用於在 Azure 中生成和部署應用程式的相同熟悉工具在本地開發和部署應用程式。 您還可以運行專用行動網路(專用 LTE、專用 5G)、安全功能(如防火牆),並使用同一專用邊緣區域設備上的 SD-WAN 設備跨多個分支和 Azure 擴展本地網路,並從 Azure 管理它們。

典型用例包括:

- 機器人即時指揮與控制
- 使用人工智慧和機器學習進行即時分析和推斷
- 機器視覺
- 混合實式實境和 VDI 機制的遠端
- 監視和安全

我們擁有由 VNF 供應商、ISV 和 MSP 合作夥伴組成的豐富生態系統,能夠使用專用邊緣區域實現端到端解決方案。 [有關詳細資訊,連絡專用邊緣區團隊](https://aka.ms/EdgeZonesPartner)。

## <a name="private-edge-zones---partners"></a><a name="private-edge-partners"></a>私人邊緣區域 - 合作夥伴

![專用邊緣區域合作夥伴](./media/edge-zones-overview/partners.png "專用邊緣區域合作夥伴")

### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>虛擬化網路功能

#### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>移動網路的虛擬化進化資料套件核心 (vEPC)

- [確認網路](https://www.affirmednetworks.com/)
- [德魯伊軟體](https://www.druidsoftware.com/)
- [埃佩托](https://www.expeto.io/)
- [馬韋尼爾](https://mavenir.com/)
- [元開關](https://www.metaswitch.com/)
- [諾基亞數位自動化雲](https://www.dac.nokia.com/)

#### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>行動無線電合作夥伴

- [通訊魯斯](https://support.ruckuswireless.com/)

#### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN 供應商

- [NetFoundry](https://netfoundry.io/)
- [來自諾基亞的 Nuage 網路](https://www.nuagenetworks.net/)
- [VMware SD-WAN 由 Velocloud](https://www.velocloud.com/)

#### <a name="router-vendors"></a><a name="router-vendors"></a>路由器供應商

- [Arista](https://www.arista.com/)

[聯繫專用邊緣區域團隊](https://aka.ms/EdgeZonesPartner),瞭解有關如何成為合作夥伴的更多資訊。

#### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>防火牆供應商

- Palo Alto Networks

### <a name="managed-solutions-providers---mobile-operators-and-global-system-integrators"></a><a name="msp-mobile"></a>託管解決方案提供者 - 行動營運商和全球系統整合者

| 全球 SI 和營運商 | 行動營運商 |
| --- | --- |
| 阿姆多克斯                       | 埃泰薩爾特             |
| 美國塔               | NTT Communications   |
| 世紀連結                 | Proximus             |
| 埃佩托                       | Rogers               |
| 聯合無線           | SK電信           |
| Infosys                      | Telefonica           |
| 科技馬辛德拉                | Telstra              |
|        *                     | Vodafone             |

[聯繫專用邊緣區域團隊](https://aka.ms/EdgeZonesPartner),瞭解有關如何成為合作夥伴的更多資訊。

## <a name="private-edge-zones---solutions"></a><a name="solutions-private-edge"></a>私人邊緣區域 - 解決方案

### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>私人邊緣區域上的專用行動網路

![私人邊緣區域上的專用行動網路](./media/edge-zones-overview/mobile-networks.png "私人邊緣區域上的專用行動網路")

您現在可以在專用邊緣區域部署專用移動網路。 專用行動網路可實現超低延遲、高容量以及業務關鍵型應用所需的可靠和安全無線網路。 專用行動網路可以實現諸如倉庫中自動引導車輛 (AGV) 的指揮和控制、智慧工廠中的機器人與增強型房地產之間的即時通信以及虛擬實境邊緣應用等場景。

虛擬化進化的數據包核心(vEPC)網路功能構成了專用移動網路的大腦。 您現在可以在專用邊緣區域部署 vEPC。 有關在專用邊緣區域可用的 vEPC 合作夥伴的清單,請參閱[vEPC ISV](#vEPC)。

在專用邊緣區域部署專用行動網路解決方案需要其他元件,如行動存取點、SIM 卡和其他 VNF(如路由器)。 訪問許可或未經許可的頻譜對於設置專用移動網路至關重要。 此外,您可能需要關於射頻規劃、物理佈局、安裝和支援的説明。 有關合作夥伴清單,請參閱[行動無線電合作夥伴](#mobile-radio)。

Microsoft 提供了一個合作夥伴生態系統,可説明處理此過程的所有方面 - 從規劃網路、購買所需設備、設置硬體,到從 Azure 管理配置。 通過一組與 Microsoft 緊密整合的經過驗證的合作夥伴,您可以放心,解決方案將可靠且易於使用。 您可以專注於核心方案,同時依靠 Microsoft 及其合作夥伴幫助解決其餘方案。

### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>私有邊緣區域上的 SD-WAN

![私有邊緣區域上的 SD-WAN](./media/edge-zones-overview/sd-wan.png "私有邊緣區域上的 SD-WAN")
 
SD-WAN 作為一項技術,可讓您創建企業級廣域網路 (WAN),具有更高的頻寬、對雲的高性能訪問、服務插入、可靠性、策略管理和廣泛的網路可見性。 SD-WAN 提供由冗餘中央控制器精心編排的無縫分支機構連接,擁有成本更低。
私有邊緣區域的 SD-WAN 允許您從以資本支出為中心的模型轉向軟體即服務 (SaaS) 模型,以減少 IT 預算。 您可以使用您選擇的 SD-WAN 合作夥伴協調器或控制器來啟用新服務並立即在整個網路中傳播它們。

## <a name="next-steps"></a>後續步驟

有關詳細資訊,可聯繫以下團隊:

* [邊緣區域團隊](https://aka.ms/EdgeZones)
* [私人邊緣區域團隊 - 成為合作夥伴](https://aka.ms/EdgeZonesPartner)
