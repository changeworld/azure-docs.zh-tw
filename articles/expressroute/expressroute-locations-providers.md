---
title: 位置和連線提供者：Azure ExpressRoute | Microsoft Docs
description: 本文提供提供服務所在位置以及如何連線到 Azure 區域的詳細概觀。 依位置排序。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: cherylmc
ms.openlocfilehash: a8d73255fdd4b6a7c7a6cc4be96a73d4fb56a613
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460162"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作夥伴和對等互連位置

> [!div class="op_single_selector"]
> * [依提供者的位置](expressroute-locations.md)
> * [依位置的提供者](expressroute-locations-providers.md)


本文中的資料表會提供有關 ExpressRoute 地理涵蓋範圍和位置、ExpressRoute 連線提供者，以及 ExpressRoute 系統整合者 (SI) 的資訊。

> [!Note]
> Azure 區域和 ExpressRoute 位置是兩種不同的概念，了解兩者之間的差異對於探索 Azure 混合式網路連線能力至關重要。 
>
>

## <a name="azure-regions"></a>Azure 區域
Azure 區域是 Azure 計算、網路和儲存體資源所在的全球資料中心。 建立 Azure 資源時，客戶需要選取資源位置。 資源位置決定資源建立所在的 Azure 資料中心 (或可用性區域)。

## <a name="expressroute-locations"></a>ExpressRoute 位置
ExpressRoute 位置 (有時稱為對等互連位置或 meet-me-locations) 是 Microsoft Enterprise Edge (MSEE) 裝置所在的共同位置設施。 ExpressRoute 位置是 Microsoft 網路的進入點，且分佈於全球，讓客戶有機會連線到全世界各地的 Microsoft 網路。 這些位置是 ExpressRoute 合作夥伴和 ExpressRoute Direct 客戶向 Microsoft 網路發出交叉連線的位置。 一般而言，ExpressRoute 位置不需要符合 Azure 區域。 例如，客戶可以在「西雅圖」對等互連位置中，建立具有資源位置「美國東部」的 ExpressRoute 線路。

如果您至少與地緣政治區域內的一個 ExpressRoute 位置連線，您將有權存取地緣政治區域內所有區域中的 Azure 服務。 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>地緣政治區域內 ExpressRoute 位置的 Azure 區域
下表提供地緣政治區域內 ExpressRoute 位置的 Azure 區域對應。

| **地緣政治區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **澳洲政府** | 澳大利亞中部、澳大利亞中部 2 |坎培拉、坎培拉 2 |
| **歐洲** | 法國中部、法國南部、德國北部、德國中西部、北歐、挪威東部、挪威西部、瑞士北部、瑞士西部、英國西部、英國南部、西歐 |阿姆斯特丹、阿姆斯特丹 2、柏林、哥本哈根、都柏林、法蘭克福、日內瓦、倫敦、倫敦 2、馬賽、米蘭、幕尼黑、新港 (威爾士)、奧斯陸、巴黎、斯塔萬格、斯德哥爾摩、蘇黎世 |
| **北美洲** | 美國東部、美國西部、美國東部 2、美國西部 2、美國中部、美國中南部、美國中北部、美國中西部、加拿大中部、加拿大東部 |亞特蘭大、芝加哥、達拉斯、丹佛、內華達州、洛杉磯、邁阿密、Minneapolis、紐約、奎（墨西哥）、San Antonio、西雅圖、矽谷、矽谷 Valley2、華盛頓特區、華盛頓特區、蒙特利爾、魁北克市、多倫多、范 |
| **亞洲** | 東亞、東南亞 | 曼谷、香港特別行政區、香港特別行政區 2、雅加達、吉隆坡、新加坡、新加坡 2、台北 |
| **印度** | 印度西部、印度中部、印度南部 |辰內、辰內 2、孟買、孟買 2 |
| **日本** | 日本西部、日本東部 |大阪、東京、東京 2 |
| **大洋洲** | 澳大利亞東南部、澳大利亞東部 |奧克蘭、墨爾本、伯斯、雪梨、雪梨 2 | 
| **南韓** | 南韓中部、南韓南部 |釜山、首爾|
| **阿拉伯聯合大公國** | 阿拉伯聯合大公國中部、阿拉伯聯合大公國北部 | 杜拜、杜拜 2 |
| **南非** | 南非西部、南非北部 |開普敦、約翰尼斯堡 |
| **南美洲** | 巴西南部 |聖保羅 |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>國家雲端的 Azure 區域和地理政治界限
下表提供國家雲端的區域和地理政治界限等資訊。

| **地緣政治區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **US Gov 雲端** |US Gov 亞利桑那州、US Gov 愛荷華州、US Gov 德克薩斯州、US Gov 維吉尼亞州、US DoD 中部、US DoD 東部  |亞特蘭大、芝加哥、達拉斯、紐約、鳳凰城、聖安東尼奧、西雅圖、矽谷、華盛頓特區 |
| **中國東部** |中國東部、中國東部 2 |上海、上海 2 |
| **中國北部** |中國北部、中國北部 2 |北京、北京 2 |
| **德國** |德國中部、德國東部 |柏林、法蘭克福 |

標準 ExpressRoute SKU 不支援跨地緣政治區域的連線。 您必須啟用 ExpressRoute 進階附加元件，以支援全球連線。 不支援連線至國家雲端環境。 如果有需要的話，您可以聯絡您的連線提供者。

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute 連線提供者

下表顯示連線的位置以及每個位置的服務提供者。 如果您想檢視服務提供者及其可提供服的各個位置，請參閱[服務提供者位置](expressroute-locations.md)。

* **本地 Azure 區域** 是位於每個對等互連位置的 [ExpressRoute Local](expressroute-faqs.md) 可以存取的區域。 **n/a** 表示該對等互連位置無法使用 ExpressRoute Local。

* **區域**指的是[定價](https://azure.microsoft.com/pricing/details/expressroute/)。


### <a name="global-commercial-azure"></a>全球商業 Azure
| **位置** | **位址** | **區域** | **本地 Azure 區域** | **ER Direct** | **服務提供者** |
| --- | --- | --- | --- | --- | --- |
| **阿姆斯特丹** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | 西歐 | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT、InterCloud、Interxion、KPN、 IX Reach、Level 3 Communications、Megaport、NTT Communications、Orange、Tata Communications、Telefonica、Telenor、Telia Carrier、Verizon、Zayo |
| **阿姆斯特丹2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | 西歐 | 10G、100G | CenturyLink Cloud Connect、Colt、CIX、euNetworks、GÉANT、Interxion、NOS、橙色、Vodafone |
| **亞特蘭大** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/a | 10G、100G | Equinix、Megaport |
| **奧克蘭** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/a | 10G | Devoli、Kordia、Megaport、Spark NZ、Vocus Group NZ |
| **曼谷** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | n/a | 10G | AIS、UIH |
| **柏林** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | 德國北部 | 10G | NTT Global DataCenters EMEA|
| **斧山** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | 南韓南部 | n/a | LG CNS |
| **坎培拉** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 澳大利亞中部 | 10G、100G | CDC |
| **坎培拉2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 澳大利亞中部 2| 10G、100G | CDC |
| **開普敦** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | 南非西部 | 10G | BCX、Internet Solutions - Cloud Connect、Liquid Telecom、Teraco |
| **辰內** | Tata Communications | 2 | 印度南部 | 10G | Global CloudXchange (GCX)、SIFY、Tata Communications |
| **辰內2** | Airtel | 2 | 印度南部 | n/a | Airtel |
| **芝加哥** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | 美國中北部 | 10G、100G | Aryaka Networks、AT&T NetBond、CenturyLink Cloud ConnectCologix、Comcast、ColtCoresite、Equinix、InterCloud、Internet2、Level 3 Communications、Megaport、PacketFabric、PCCW Global Limited、Sprint、Telia Carrier、Verizon、Zayo |
| **哥本哈根** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/a | 10G | Interxion |
| **達拉斯** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/a | 10G、100G | Aryaka Networks、AT&T NetBond、Cologix、Equinix、Internet2、Level 3 Communications、Megaport、Neutrona Networks、Telmex Uninet、Telia Carrier、Transtelco、Verizon、Zayo|
| **丹佛** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | 美國中西部 | n/a | CoreSite、Megaport、Zayo |
| **杜拜** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | 阿拉伯聯合大公國北部 | n/a | Etisalat UAE |
| **杜拜 2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | 阿拉伯聯合大公國北部 | n/a | CIX、du datamena、Megaport、橙色、Orixcom |
| **都柏林** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | 北歐 | 10G、100G | Colt、eir、Equinix、GEANT、euNetworks、Interxion、Megaport |
| **法蘭克福** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | 德國中西部 | 10G、100G | 在&T NetBond、CenturyLink Cloud Connect、Colt、DE CIX、Equinix、euNetworks、GEANT、Interxion、Megaport、橙色、Telia 電訊廠商 |
| **日內瓦** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | 瑞士西部 | 10G、100G | Equinix、Megaport |
| **香港** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | 東亞 | n/a | Aryaka Networks、British Telecom、CenturyLink Cloud Connect、Chief Telecom、China Telecom Global、Equinix、InterCloud、Megaport、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telia Carrier、Verizon |
| **香港 2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | n/a | 10G | 中國的 Mobile 國際、中國電信全球、PCCW GLOBAL LIMITED 全球有限 |
| **雅加達** | Telkom Indonesia | 4 | n/a | 10G | |
| **約翰尼斯堡** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | 南非北部 | 10G | BCX、British Telecom、Internet Solutions - Cloud Connect、Liquid Telecom、Orange、Teraco |
| **吉隆坡** | [TIME dotCom Menara AIMS](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | n/a | n/a | TIME dotCom |
| **拉斯維加斯** | [Switch LV](https://www.switch.com/las-vegas) | 1 | n/a | n/a | CenturyLink Cloud Connect、Megaport |
| **倫敦** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | 英國南部 | 10G、100G | AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc、Level 3 Communications、Megaport、MTN、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telehouse - KDDI、Telenor、Telia Carrier、Verizon、Vodafone、Zayo |
| **London2** | [Telehouse North Two](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | 英國南部 | 10G、100G | CenturyLink Cloud Connect、Colt、GTT、IX 觸及、Equinix、Megaport、Telehouse-KDDI |
| **洛杉磯** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/a | 10G、100G | CoreSite、Equinix、Megaport、Neutrona Networks、NTT、Zayo |
| **馬賽** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | 法國南部 | n/a | DE-CIX、GEANT、Interxion、Jaguar Network、Ooredoo Cloud Connect |
| **墨爾本** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | 澳大利亞東南部 | 10G、100G | AARNet、Devoli、Equinix、Megaport、NEXTDC、Optus、Telstra Corporation、TPG Telecom |
| **邁阿密** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/a | 10G、100G | Claro、C3ntro、Equinix、Megaport、Neutrona Networks |
| **米蘭** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n/a | 10G | Equinix、Retelit |
| **Minneapolis** | [Cologix MIN1](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | n/a | 10G、100G | Cologix |
| **蒙特婁** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/a | 10G、100G | Bell Canada、Cologix、Megaport、Telus、Zayo |
| **孟買** | Tata Communications | 2 | 印度西部 | 10G | CIX，Global CloudXchange （GCX），依賴 Jio，Sify，Tata 通訊，Verizon |
| **孟買2** | Airtel | 2 | 印度西部 | n/a | Airtel、Sify、Vodafone Idea |
| **幕尼黑** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | n/a | 10G | DE-CIX |
| **紐約** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n/a | 10G、100G | CenturyLink Cloud Connect、Colt、Coresite、DE CIX、Equinix、InterCloud、Megaport、Packet、Zayo |
| **Newport(Wales)** | [新一代資料](https://www.nextgenerationdata.co.uk) | 1 | 英國西部 | n/a | British Telecom、Colt、Level 3 Communications、Next Generation Data |
| **大阪** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | 日本西部 | 10G、100G | Colt，Equinix，網際網路方案日本 Inc.-IIJ，Megaport，NTT 通訊，NTT SmartConnect，Softbank |
| **奧斯陸** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | 挪威東部 | 10G、100G | Global Connect、Megaport、Telenor、Telia 電訊廠商 |
| **巴黎** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | 法國中部 | 10G、100G | CenturyLink Cloud Connect、Colt、Equinix、Intercloud、Interxion、Orange、Telia Carrier、Zayo |
| **伯斯** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/a | 10G | Megaport、NextDC |
| **Phoenix** | [EdgeConnext PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | n/a | 10G | |
| **魁北克市** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | 加拿大東部 | n/a | Bell Canada、Megaport |
| **奎（墨西哥）** | [KIO 網路 QR01](https://www.kionetworks.com/es-mx/) | 4 | n/a | 10G | Transtelco|
| **昆西** | [Sabey Datacenter-建立](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | 美國西部 2 | 10G、100G | | 
| **聖安東尼奧** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | 美國中南部 | 10G、100G | CenturyLink Cloud Connect、Megaport |
| **聖保羅** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | 巴西南部 | n/a | Aryaka Networks、Ascenty Data Centers、British Telecom、Equinix、Level 3 Communications、Neutrona Networks、Orange、Tata Communications、Telefonica、UOLDIVEOW |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | 美國西部 2 | 10G、100G | Aryaka Networks、Equinix、Level 3 Communications、Megaport、Telus、Zayo |
| **首爾** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | 南韓中部 | 10G、100G | KINX、KT、LG CNS、Sejong Telecom |
| **矽谷** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | 美國西部 | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、Comcast、Coresite、Equinix、InterCloud、Internet2、IX Reach、Packet、PacketFabric、Level 3 Communications、Megaport、Orange、Sprint、Tata Communications、Telia Carrier、Verizon、Zayo |
| **矽谷 2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | 美國西部 | 10G、100G | Colt、Coresite | 
| **新加坡** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | 東南亞 | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、China Mobile International、Epsilon Global Communications、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、SingTel、Tata Communications、Telstra Corporation、Verizon、Vodafone |
| **新加坡 2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | 東南亞 | 10G、100G | China Unicom Global、Colt、Epsilon Global Communications、Megaport、PCCW Global Limited、SingTel |
| **斯塔萬格** | [Green Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | 挪威西部 | 10G、100G |Global Connect、Megaport |
| **斯德哥爾摩** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/a | 10G | Equinix、Telia Carrier |
| **雪梨** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | 澳大利亞東部 | 10G、100G | AARNet、AT&T NetBond、British Telecom、Devoli、Equinix、Kordia、Megaport、NEXTDC、NTT Communications、Optus、Orange、Spark NZ、Telstra Corporation、TPG Telecom、Verizon、Vocus Group NZ |
| **雪梨 2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | 澳大利亞東部 | 10G、100G | Megaport、NextDC |
| **台北** | Chief Telecom | 2 | n/a | 10G | Chief Telecom、Chunghwa Telecom、FarEasTone |
| **東京** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | 日本東部 | 10G、100G | Aryaka Networks、AT&T NetBond、BBIX、British Telecom、CenturyLink Cloud Connect、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、Megaport、NTT Communications、NTT EAST、Orange、Softbank、Verizon |
| **東京 2** | [AT TOKYO](https://www.attokyo.com/) | 2 | 日本東部 | 10G、100G | AT TOKYO |
| **多倫多** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | 加拿大中部 | 10G、100G | AT&T NetBond、Bell Canada、CenturyLink Cloud Connect、Cologix、Equinix、IX Reach Megaport、Telus、Verizon、Zayo |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | n/a | 10G | Cologix |
| **華盛頓** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | 美國東部、美國東部 2 | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Cologix、Colt、Comcast、Coresite、Equinix、Internet2、InterCloud、IX Reach、Level 3 Communications、Megaport、Neutrona Networks、NTT Communications、Orange、PacketFabric、SES、Sprint、Tata Communications、Telia Carrier、Verizon、Zayo |
| **華盛頓特區2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | 美國東部、美國東部 2 | 10G、100G | CenturyLink Cloud Connect、Coresite、Intelsat、Megaport、Viasat、Zayo | 
| **蘇黎士** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | 瑞士北部 | 10G、100G | Intercloud、Interxion、Megaport、Swisscom |

 **+** 表示即將推出

### <a name="national-cloud-environments"></a>國家雲端環境

Azure 國家雲端彼此隔離，也與全球商業 Azure 隔離。 某個 Azure 雲端的 ExpressRoute 無法連線到其他 Azure 雲端的 Azure 區域。

### <a name="us-government-cloud"></a>美國政府雲端
| **位置** | **位址** | **本地 Azure 區域**| **ER Direct** | **服務提供者** |
| --- | --- | --- | --- | --- |
| **亞特蘭大** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n/a | 10G、100G | Equinix |
| **芝加哥** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/a | 10G、100G | AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **達拉斯** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/a | 10G、100G | Equinix、Megaport、Verizon |
| **紐約** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/a | 10G、100G | Equinix、CenturyLink Cloud Connect、Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov 亞利桑那州 | n/a | AT&T NetBond、CenturyLink Cloud Connect、Megaport |
| **聖安東尼奧** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov 德克薩斯州 | n/a | CenturyLink Cloud Connect、Megaport |
| **矽谷** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/a | 10G、100G | AT&T、Equinix、LLevel 3 Communications、Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/a | n/a | Equinix、Megaport |
| **華盛頓** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD 東部、US Gov 維吉尼亞州 | 10G、100G | AT&T NetBond、CenturyLink Cloud Connect、Equinix、Level 3 Communications、Megaport、Verizon |

### <a name="china"></a>中國
| **位置** | **服務提供者** |
| --- | --- |
| **北京** |China Telecom |
| **北京 2** | China Telecom、China Unicom、GDS |
| **上海** |China Telecom |
| **上海 2** | China Telecom、China Unicom、GDS |

若要深入了解，請參閱 [ExpressRoute (中國)](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>德國
| **位置** | **服務提供者** |
| --- | --- |
| **柏林** |e-shelter、Megaport+、T-Systems |
| **法蘭克福** |Colt、Equinix、Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>透過 Exchange 提供者連線
如果上一節中未列出您的連線提供者，您仍然可以建立連線。

* 請洽詢您的連線提供者，以了解他們是否連線到上方表格中列出的任何 Exchange 提供者。 您可以檢查下列連結，以收集 Exchange 提供者所提供之服務的相關詳細資訊。 已有數個連線提供者連線到乙太網路 Exchange。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* 請您的連線提供者將您的網路延伸到選擇的對等互連位置。
  * 請確保您的連線提供者以高可用性的方式延伸您的連線，因此不會有單一失敗點。
* 排序一個 ExpressRoute 循環，將 Exchange 視為連線至 Microsoft 的連線提供者。
  * 依照 [建立 ExpressRoute 循環](expressroute-howto-circuit-classic.md) 中的步驟來設定連線。

## <a name="connectivity-through-satellite-operators"></a>透過衛星操作員連線
如果您在遠端且沒有光纖連線，或想要探索其他連線選項，則可檢查下列衛星操作員。 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>透過其他服務提供者連線
| **位置** | **Exchange** | **連線提供者** |
| --- | --- | --- |
| **阿姆斯特丹** | Equinix、Interxion、Level 3 Communications | BICS、CloudXpress、Eurofiber、Fastweb S.p.A、Gulf Bridge International、Kalaam Telecom Bahrain B.S.C、MainOne、Nianet、POST Telecom Luxembourg、Proximus、RETN、TDC Erhverv、Telecom Italia Sparkle、Telekom Deutschland GmbH、Telia |
| **亞特蘭大** | Equinix| Crown Castle
| **開普敦** | Teraco | MTN |
| **芝加哥** | Equinix| Crown Castle、Spectrum Enterprise、Windstream |
| **達拉斯** | Equinix、Megaport | Axtel、C3ntro Telecom、Cox Business、Crown Castle、Data Foundry、Spectrum Enterprise、Transtelco |
| **法蘭克福** | Interxion | BICS、Cinia、Equinix、Nianet、QSC AG、Telekom Deutschland GmbH |
| **漢堡** | Equinix | Cinia |
| **香港特別行政區** | Equinix | Chief、Macroview Telecom |
| **約翰尼斯堡** | Teraco | MTN |
| **倫敦** | BICS、Equinix、euNetworks| Bezeq International Ltd.、CoreAzure、Epsilon Telecommunications Limited、Exponential E、HSO、NexGen Networks、Proximus、Tamares Telecom、Zain |
| **洛杉磯** | Equinix |Crown Castle、Spectrum Enterprise、Transtelco |
| **馬德里** | Level3 | Zertia |
| **蒙特婁** | Cologix| Airgate Technologies, Inc.Aptum Technologies、Rogers、Zirro |
| **紐約** |Equinix、Megaport | Altice Business、Crown Castle、Spectrum Enterprise、Webair |
| **巴黎** | Equinix | Proximus |
| **魁北克市** | Megaport | Fibrenoire |
| **聖保羅** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **矽谷** |Coresite、Equinix | Cox Business、Spectrum Enterprise、Windstream、X2nsat Inc. |
| **新加坡** |Equinix |1CLOUDSTAR、BICS、CMC Telecom、Epsilon Telecommunications Limited、LGA Telecom、United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **雪梨** | Megaport | Macquarie Telecom Group|
| **東京** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **多倫多** | Equinix、Megaport | Airgate Technologies Inc.、Beanfield Metroconnect、Aptum Technologies、IVedha Inc、Rogers、Thinktel、Zirro|
| **華盛頓** |Equinix | Altice Business、BICS、Cox Business、Crown Castle、Gtt Communications Inc、Epsilon Telecommunications Limited、Masergy、Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute 系統整合者
根據您的網路規模，為符合您的需求而啟用私人連線可能有一定的難度。 您可以使用下表所列出的任何系統整合者來協助您開始使用 ExpressRoute。

| **Continent** | **系統整合者** |
| --- | --- |
| **亞洲** |Avanade Inc.、OneAs1a |
| **澳大利亞** | Ensyst、IT Consultancy、MOQdigital、Vigilant.IT |
| **歐洲** |Avanade Inc.、Altogee、Bright Skies GmbH、Inframon、MSG Services、New Signature、Nelite、Orange Networks、sol-tec |
| **北美洲** |Avanade Inc.、Equinix Professional Services、FlexManage、Lightstream、Perficient、Presidio |
| **南美洲** |Avanade Inc.、Venha Pra Nuvem |
## <a name="next-steps"></a>後續步驟
* 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置圖"
