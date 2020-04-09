---
title: 位置和連線提供者︰Azure ExpressRoute | Microsoft Docs
description: 本文提供提供服務所在位置以及如何連線到 Azure 區域的詳細概觀。 依位置排序。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0b8c8a0baba5a80c6a383e6f8f14e29b482dbc82
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878607"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作夥伴和對等互連位置

> [!div class="op_single_selector"]
> * [依提供者進行的位置](expressroute-locations.md)
> * [提供者(按位置)](expressroute-locations-providers.md)


本文中的表提供有關 ExpressRoute 地理覆蓋範圍和位置、快速路由連接提供者和快速路由系統集成者 (SIs) 的資訊。

> [!Note]
> Azure 區域和 ExpressRoute 位置是兩個不同概念,瞭解兩者之間的區別對於探索 Azure 混合網路連接至關重要。 
>
>

## <a name="azure-regions"></a>Azure 區域
Azure 區域是 Azure 計算、網路和存儲資源所在的全域資料中心。 創建 Azure 資源時,客戶需要選擇資源位置。 資源位置確定在其中創建資源的 Azure 資料中心(或可用性區域)。

## <a name="expressroute-locations"></a>快速路線位置
ExpressRoute 位置(有時稱為對等位置或 Meet-me 位置)是 Microsoft 企業邊緣 (MSEE) 設備所在的同地位置設施。 ExpressRoute 位置是Microsoft網路的入口點,並且在全球範圍內進行分發,為客戶提供連接到Microsoft全球網路的機會。 這些位置是 ExpressRoute 合作夥伴和 ExpressRoute Direct 客戶向 Microsoft 網路發出交叉連接的位置。 通常,ExpressRoute 位置不需要與 Azure 區域匹配。 例如,客戶可以在*西雅圖*對等位置創建具有資源位置 *「美國東部*」的 ExpressRoute 電路。

如果您至少與地緣政治區域內的一個 ExpressRoute 位置連線，您將有權存取地緣政治區域內所有區域中的 Azure 服務。 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Azure 區域到地緣政治區域內的 ExpressRoute 路由位置
下表提供地緣政治區域內 ExpressRoute 位置的 Azure 區域對應。

| **地緣政治區域** | **Azure 區域** | **快速路線位置** |
| --- | --- | --- |
| **澳洲政府** | 澳大利亞中部、澳大利亞中部 2 |坎培拉、坎培拉 2 |
| **歐洲** | 法國中部、法國南部、德國北部、德國中西部、北歐、挪威東部、挪威西部、瑞士北部、瑞士西部、英國西部、英國南部、西歐 |阿姆斯特丹、阿姆斯特丹2、哥本哈根、都柏林、法蘭克福、日內瓦、倫敦、倫敦2、馬賽、米蘭、慕尼黑、紐波特(威爾士)、奧斯陸、巴黎、斯塔萬格、斯德哥爾摩、蘇黎世、慕尼克 |
| **北美洲** | 美國東部、美國西部、美國東部 2、美國西部 2、美國中部、美國中南部、美國中北部、美國中西部、加拿大中部、加拿大東部 |亞特蘭大、芝加哥、達拉斯、丹佛、拉斯維加斯、洛杉磯、邁阿密、紐約、聖安東尼奧、西雅圖、矽谷、矽谷2、華盛頓特區、華盛頓特區、蒙特利爾、魁北克城、多倫多、溫哥華 |
| **Asia** | 東亞、東南亞 | 曼谷, 香港, 香港2, 雅加達, 吉隆坡, 新加坡, 新加坡2, 臺北 |
| **印度** | 印度西部、印度中部、印度南部 |辰內、辰內 2、孟買、孟買 2 |
| **日本** | 日本西部、日本東部 |大阪, 東京, 東京2 |
| **大洋洲** | 澳大利亞東南部、澳大利亞東部 |奧克蘭、 墨爾本、 珀斯、 悉尼、 悉尼2 | 
| **南韓** | 南韓中部、南韓南部 |釜山、首爾|
| **阿拉伯聯合大公國** | 阿聯酋中部, 阿聯酋北部 | 迪拜, 迪拜2 |
| **南非** | 南非西部,南非北部 |開普敦、約翰尼斯堡 |
| **南美洲** | 巴西南部 |聖保羅 |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>國家雲的 Azure 區域和地緣政治邊界
下表提供國家雲端的區域和地理政治界限等資訊。

| **地緣政治區域** | **Azure 區域** | **快速路線位置** |
| --- | --- | --- |
| **US Gov 雲端** |US Gov 亞利桑那州、US Gov 愛荷華州、US Gov 德克薩斯州、US Gov 維吉尼亞州、US DoD 中部、US DoD 東部  |亞特蘭大、芝加哥、達拉斯、紐約、鳳凰城、聖安東尼奧、西雅圖、矽谷、華盛頓特區 |
| **中國東部** |中國東部、中國東部 2 |上海、上海 2 |
| **中國北部** |中國北部、中國北部 2 |北京、北京 2 |
| **德國** |德國中部、德國東部 |柏林、法蘭克福 |

標準 ExpressRoute SKU 不支援跨地緣政治區域的連線。 您必須啟用 ExpressRoute 進階附加元件，以支援全球連線。 不支援連線至國家雲端環境。 如果有需要的話，您可以聯絡您的連線提供者。

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute 連線提供者

下表顯示連線的位置以及每個位置的服務提供者。 如果您想檢視服務提供者及其可提供服的各個位置，請參閱[服務提供者位置](expressroute-locations.md)。

* **本地 Azure 區域**是在每個對等位置[快速路由本地](expressroute-faqs.md)區域可以訪問的區域。 **n/a**表示 ExpressRoute Local 在該對等位置不可用。

* **區域**是指[定價](https://azure.microsoft.com/pricing/details/expressroute/)。


### <a name="global-commercial-azure"></a>全球商業 Azure
| **位置** | **位址** | **區** | **本地端 Azure 區域** | **ER 直接** | **服務提供者** |
| --- | --- | --- | --- | --- | --- |
| **阿姆斯特丹** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | 西歐 | 10G, 100G | 阿裡亞卡網路、美國電話電報公司&amp;T NetBond、英國電信、科爾特、Equinix、euNetworks、G&ANT、InterCloud、Interxion、KPN、IX 覆蓋、3 級通信、兆埠、NTT 通信、奧蘭治、塔塔通信、電信、電信、泰利亞運營商、Verizon、Zayo |
| **阿姆斯特丹2** | [跨號 AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | 西歐 | 10G, 100G | 世紀鏈接雲連線, 科爾特, DE-CIX, 歐網, 互連, 橙色, 沃達豐 |
| **亞特蘭大** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/a | n/a | Equinix、Megaport |
| **奧克蘭** | [沃庫斯集團紐西蘭奧爾巴尼](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/a | 10G | 德沃利,科爾迪亞,兆港,火花紐西蘭,Vocus集團紐西蘭 |
| **曼谷** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | n/a | 10G | AIS |
| **斧山** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | 南韓南部 | n/a | LG CNS |
| **坎培拉** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 澳大利亞中部 | 10G, 100G | CDC |
| **坎培拉2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 澳大利亞中部 2| 10G, 100G | CDC |
| **開普敦** | [特拉科 CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | 南非西部 | 10G | BCX, 網際網路解決方案 - 雲端連線, 液體電信, 特拉科 |
| **辰內** | Tata Communications | 2 | 印度南部 | 10G | Global CloudXchange (GCX)、SIFY、Tata Communications |
| **辰內2** | Airtel | 2 | 印度南部 | n/a | Airtel |
| **芝加哥** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | 美國中北部 | 10G, 100G | 阿裡亞卡網路, AT&T NetBond, 世紀連結雲連接, 科洛尼克斯, 科爾特, 康卡斯特, 核心網站, Equinix, InterCloud, Internet2, 3 級通信, 兆波特, 包布,電訊盈科全球有限公司, 衝刺, 泰利亞運營商, Verizon, Zayo |
| **哥本哈根** | [錫龍 CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/a | 10G | Interxion |
| **達拉斯** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/a | 10G, 100G | 阿裡亞卡網路,美國電話&T NetBond,科洛尼克斯,Equinix,Internet2,3級通信,兆波特,中子網络,Telmex Uninet,Telia運營商,Transtelco,Verizon,Zayo|
| **丹佛** | [核心網站 DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | 美國中西部 | n/a | 核心網站, 兆港, 紮約 |
| **迪拜** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | 阿拉伯聯合大公國北部 | n/a | 埃泰薩爾特 阿聯酋 |
| **迪拜2** | [杜德梅納](http://datamena.com/solutions/data-centre) | 3 | 阿拉伯聯合大公國北部 | n/a | 杜戈梅納, 兆港, 奧蘭治, 奧裡克斯康 |
| **都柏林** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | 北歐 | 10G, 100G | 科爾特, eir, Equinix, euNetworks, 互通, 兆連接埠 |
| **法蘭克福** | [錫龍 FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | 德國中西部 | 10G, 100G | 世紀鏈接雲連線, 科爾特, DE-CIX, Equinix, GEANT, 互連, 兆港, 橙色, 泰利亞運營商 |
| **日內瓦** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | 瑞士西部 | 10G, 100G | Equinix、Megaport |
| **香港** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | 東亞 | n/a | 亞里卡網路、英國電信、世紀通雲連接、首席電信、中國電信全球、Equinix、InterCloud、Megaport、NTT通信、奧蘭治、電訊盈科全球有限公司、塔塔通信、泰利亞運營商、Verizon |
| **香港2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | n/a | 10G | |
| **雅加達** | 印尼特爾科姆 | 4 | n/a | 10G | |
| **約翰尼斯堡** | [特拉科JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | 南非北部 | 10G | 英國電信, 網際網路解決方案- 雲端連線, 液體電信, 橙色, 特拉科 |
| **吉隆坡** | [時代點梅納拉AIMS](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | n/a | n/a | TIME dotCom |
| **拉斯維加斯** | [開關 LV](https://www.switch.com/las-vegas) | 1 | n/a | n/a | CenturyLink Cloud Connect、Megaport |
| **倫敦** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | 英國南部 | 10G, 100G | 美國電話電報公司&T NetBond、 英國電信、 科爾特、 Equinix、 euNetworks、 InterCloud、 互聯網解決方案 - 雲連接、Interxion、Jisc、3 級通信、兆波特、MTN、NTT 通信、奧蘭治、電訊盈科全球有限公司、塔塔通信、電信總部 - KDDI、Telenor、Telia 運營商、Verizon、沃達豐、Zayo |
| **London2** | [電信大樓北二號](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | 英國南部 | 10G, 100G | 世紀連結雲連線, 科爾特, 九到達, Equinix, 兆埠, 電房 - KDDI |
| **Los Angeles** | [核心網站 LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/a | n/a | 核心網站, Equinix, Megaport, 中子網路, NTT, Transtelco, Zayo |
| **馬賽** |[插曲 MRS1](https://www.interxion.com/Locations/marseille/) | 1 | 法國南部 | n/a | DE-CIX, GEANT, Interxion, 捷豹網路, 奧雷多雲連接 |
| **墨爾本** | [下一個DC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | 澳大利亞東南部 | 10G, 100G | AARNet, 德沃利, Equinix, 兆港、 NEXTDC, Optus, Telstra 公司, TPG 電信 |
| **邁阿密** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/a | 10G | C3ntro, Equinix, 兆港, 中子網路 |
| **Milan** | [伊裡多斯](https://irideos.it/en/data-centers/) | 1 | n/a | 10G | |
| **蒙特婁** | [科洛尼克斯 MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/a | 10G, 100G | 貝爾加拿大, 科洛威斯, 兆港, 泰魯斯, 扎約 |
| **孟買** | Tata Communications | 2 | 印度西部 | n/a | 全球雲X變更 (GCX)、信實、Sify、塔塔通信、Verizon |
| **孟買2** | Airtel | 2 | 印度西部 | n/a | Airtel、Sify、Vodafone Idea |
| **慕尼克** | [邊緣康奈克斯](https://www.edgeconnex.com/locations/europe/) | 1 | n/a | 10G, 100G | |
| **紐約** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n/a | n/a | 世紀連結雲連線, 科爾特, 核心網站, Equinix, 雲端, 兆埠, 包, Zayo |
| **紐波特(威爾士)** | [下一代資料](https://www.nextgenerationdata.co.uk) | 1 | 英國西部 | n/a | 英國電信, 科爾特, 3 級通信, 下一代數據 |
| **大阪** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | 日本西部 | 10G, 100G | Colt, Equinix, 網際網路倡議日本公司 - IIJ, 兆港, NTT 通訊, NTT 智慧連線, 軟銀 |
| **Oslo** | [迪吉普·烏爾文](https://www.digiplex.com/locations/oslo-datacentre) | 1 | 挪威東部 | 10G, 100G | 特萊諾兆港,泰利亞航空公司 |
| **巴黎** | [跨號 PAR5](https://www.interxion.com/Locations/paris/) | 1 | 法國中部 | 10G, 100G | 世紀連結雲連接, 科爾特, Equinix, 雲間, 插曲, 橙色, 泰利亞運營商, 扎約 |
| **伯斯** | [下一個DC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/a | 10G | 兆埠,下一個DC |
| **魁北克市** | [優勢](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | 加拿大東部 | n/a | Bell Canada、Megaport |
| **San Antonio** | [賽勒斯內 SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | 美國中南部 | 10G, 100G | CenturyLink Cloud Connect、Megaport |
| **聖保羅** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | 巴西南部 | n/a | Aryaka Networks、Ascenty Data Centers、British Telecom、Equinix、Level 3 Communications、Neutrona Networks、Orange、Tata Communications、Telefonica、UOLDIVEOW |
| **西雅圖** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | 美國西部 2 | 10G, 100G | 阿裡亞卡網路, Equinix, 3 級通信, 兆港, 泰魯斯, 扎約 |
| **首爾** | [KINX 加桑IDC](https://www.kinx.net/support/location/?lang=en) | 2 | 南韓中部 | 10G, 100G | KINX, KT, LG CNS, 世宗電信 |
| **矽谷** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | 美國西部 | 10G, 100G | Aryaka網路, AT&T NetBond, 英國電信, 世紀連結雲連接, 科爾特, 康卡斯特, 核心網站, Equinix, InterCloud, 互聯網2, IX 覆蓋, 數據包, 資料包Fabric, 3級通信, 兆埠, 橙色, 衝刺, 塔塔通信, 泰利亞運營商, Verizon, Zayo |
| **矽谷2** | [核心網站 SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | 美國西部 | 10G, 100G | 科爾特, 核心網站 | 
| **新加坡** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | 東南亞 | 10G, 100G | 阿裡亞卡網路、美國電話電報公司&T NetBond、英國電信、中國移動國際、愛普西隆全球通信、Equinix、InterCloud、3級通信、兆豐、NTT通信、奧蘭治、新加坡電信、塔塔通信、電信公司、Verizon、沃達豐 |
| **新加坡 2** | [全球開關泰森](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | 東南亞 | 10G, 100G | 中國聯通全球、科爾特、愛普西隆全球通信、兆港、新加坡電信 |
| **斯塔萬格** | [綠山DC1](https://greenmountain.no/dc1-stavanger/) | 1 | 挪威西部 | 10G, 100G | |
| **斯德哥爾摩** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/a | 10G | 埃奎尼克斯, 泰利亞航空公司 |
| **雪梨** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | 澳大利亞東部 | 10G, 100G | AARNet、 AT&T NetBond, 英國電信、 德沃利、 Equinix、 科爾迪亞、 兆港、 NEXTDC, NTT 通訊、 Optus、 奧蘭治、 Spark NZ、 Telstra 公司, TPG 電信、 Verizon, Vocus Group NZ |
| **悉尼2** | [下一個DC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | 澳大利亞東部 | 10G, 100G | 兆埠,下一個DC |
| **台北** | Chief Telecom | 2 | n/a | 10G | 法里亞斯通首席電信 |
| **東京** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | 日本東部 | 10G, 100G | Aryaka網络, AT&T NetBond, BBIX, 英國電信, 世紀連結雲連接, 科爾特, Equinix, 互聯網倡議日本公司 - IIJ, 兆港, NTT通信, NTT EAST, 奧蘭治, 軟銀, Verizon |
| **東京2** | [在東京](https://www.attokyo.com/) | 2 | 日本東部 | 10G, 100G | 在東京 |
| **多倫多** | [科洛尼克斯TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | 加拿大中部 | 10G, 100G | AT&T NetBond、貝爾加拿大、世紀鏈接雲連接、科洛尼克斯、Equinix、IX 到達兆港、泰魯斯、威瑞森、扎約 |
| **溫哥華** | [科洛尼克斯范1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | n/a | 10G, 100G | |
| **華盛頓** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | 美國東部,美國東部 2 | 10G, 100G | 阿裡亞卡網路, AT&T NetBond, 英國電信, 世紀鏈接雲連接, 科洛尼克斯, 科爾特, 康卡斯特, 核心網站, Equinix, Internet2, InterCloud, IX 覆蓋, 3 級通信, 兆端口, 中子網路, NTT通信, 橙色, 包布,SES, 衝刺, 塔塔通信, 泰利亞運營商, Verizon, Zayo |
| **華盛頓特區2** | [核心網站雷斯頓](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | 美國東部,美國東部 2 | 10G, 100G | 世紀連結雲連線, 核心網站, Intelsat, 維亞盛, 扎約 | 
| **蘇黎世** | [聯交 ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | 瑞士北部 | 10G, 100G | 雲間, 國際, 兆港, 瑞士 |

 **+** 表示即將推出

### <a name="national-cloud-environments"></a>國家雲端環境

Azure 國家雲彼此隔離,與全域商業 Azure 隔離。 一個 Azure 雲的 ExpressRoute 無法連接到其他中的 Azure 區域。

### <a name="us-government-cloud"></a>美國政府雲端
| **位置** | **位址** | **本地端 Azure 區域**| **ER 直接** | **服務提供者** |
| --- | --- | --- | --- | --- |
| **亞特蘭大** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n/a | 10G, 100G | Equinix |
| **芝加哥** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/a | 10G, 100G | AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **達拉斯** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/a | 10G, 100G | Equinix、Megaport、Verizon |
| **紐約** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/a | 10G, 100G | Equinix、CenturyLink Cloud Connect、Verizon |
| **Phoenix** | [賽勒斯·內·錢德勒](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov 亞利桑那州 | n/a | AT&T NetBond, 世紀連結雲端連線, 兆埠 |
| **San Antonio** | [賽勒斯內 SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov 德克薩斯州 | n/a | CenturyLink Cloud Connect、Megaport |
| **矽谷** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/a | 10G, 100G | AT&T, Equinix, 3 級通訊, Verizon |
| **西雅圖** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/a | n/a | Equinix、Megaport |
| **華盛頓** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 美國國防部東,美國弗吉尼亞州州長 | 10G, 100G | AT&T NetBond、世紀連結雲連接、Equinix、3 級通信、兆埠、Verizon |

### <a name="china"></a>中國
| **位置** | **服務提供者** |
| --- | --- |
| **北京** |China Telecom |
| **北京 2** | 中國電信、中國聯通、GDS |
| **上海** |China Telecom |
| **上海 2** | 中國電信,GDS |

要瞭解更多資訊,請參閱[中國速遞路線](http://www.windowsazure.cn/home/features/expressroute/)

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
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [國際](https://www.interxion.com/)
  * [下一個DC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* 請您的連線提供者將您的網路延伸到選擇的對等互連位置。
  * 請確保您的連線提供者以高可用性的方式延伸您的連線，因此不會有單一失敗點。
* 排序一個 ExpressRoute 循環，將 Exchange 視為連線至 Microsoft 的連線提供者。
  * 依照 [建立 ExpressRoute 循環](expressroute-howto-circuit-classic.md) 中的步驟來設定連線。

## <a name="connectivity-through-satellite-operators"></a>透過衛星營運商連接
如果您是遠端的,並且沒有光纖連接,或者想要探索其他連接選項,可以檢查以下衛星運營商。 

* 英特爾
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [維亞薩特](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>透過其他服務提供者連線
| **位置** | **裝置** | **連線供應商** |
| --- | --- | --- |
| **阿姆斯特丹** | Equinix, 互通, 3 級通訊 | BICS, CloudXpress, 歐洲光纖、 快速網路 S.p.A、 海灣橋國際、 巴林卡拉姆電信 B.S.C、 美因One、 Nianet, POST 電信盧森堡、 普羅西穆斯、 RETN、 TDC Erhverv、 義大利電信火花、 電信德國電信有限公司、 泰利亞 |
| **亞特蘭大** | Equinix| 皇冠城堡
| **開普敦** | Teraco | MTN |
| **芝加哥** | Equinix| 皇冠城堡, 頻譜企業, 風流 |
| **達拉斯** | Equinix、Megaport | 安克斯特, C3ntro電信, 考克斯業務, 皇冠城堡, 數據鑄造廠, 頻譜企業, Transtelco |
| **法蘭克福** | Interxion | BICS, 西尼亞, Equinix, 尼亞尼特, QSC AG, 德國電信有限公司 |
| **漢堡** | Equinix | Cinia |
| **香港特別行政區** | Equinix | Chief、Macroview Telecom |
| **約翰尼斯堡** | Teraco | MTN |
| **倫敦** | BICS, Equinix, 歐網| 貝澤克國際有限公司, CoreAzure, Epsilon電信有限公司, 指數E, HSO, NexGen網路, Proximus, 塔馬拉斯電信, 扎因 |
| **Los Angeles** | Equinix |皇冠城堡, 頻譜企業, Transtelco |
| **馬德里** | Level3 | Zertia |
| **蒙特婁** | Cologix| Airgate技術公司阿普圖姆技術公司,羅傑斯,齊羅 |
| **紐約** |Equinix、Megaport | 阿爾蒂斯商業, 皇冠城堡, 頻譜企業, 網路 |
| **巴黎** | Equinix | Proximus |
| **魁北克市** | Megaport | Fibrenoire |
| **聖保羅** | Equinix | Venha Pra Nuvem |
| **西雅圖** |Equinix | Alaska Communications |
| **矽谷** |核心網站, Equinix | 考克斯業務, 頻譜企業, 風流, X2nsat公司. |
| **新加坡** |Equinix |1CLOUDSTAR, BICS, CMC電信, Epsilon電信有限公司, LGA電信, 聯合資訊高速公路 (UIH) |
| **Slough** | Equinix | HSO|
| **雪梨** | Megaport | Macquarie Telecom Group|
| **東京** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **多倫多** | Equinix、Megaport | Airgate技術公司,比斯菲爾德地鐵公司,阿普圖姆技術公司,IVedha公司,羅傑斯,Thinktel,齊羅|
| **華盛頓** |Equinix | Altice 商業, BICS, 考克斯業務, 皇冠城堡, Gtt通信公司, Epsilon電信有限公司, 馬森,風流 |

## <a name="expressroute-system-integrators"></a>ExpressRoute 系統整合者
根據您的網路規模，為符合您的需求而啟用私人連線可能有一定的難度。 您可以使用下表所列出的任何系統整合者來協助您開始使用 ExpressRoute。

| **Continent** | **系統整合者** |
| --- | --- |
| **Asia** |Avanade Inc.、OneAs1a |
| **澳大利亞** | Ensyst、IT Consultancy、MOQdigital、Vigilant.IT |
| **歐洲** |Avanade Inc.、Altogee、Bright Skies GmbH、Inframon、MSG Services、New Signature、Nelite、Orange Networks、sol-tec |
| **北美洲** |Avanade Inc.、Equinix Professional Services、FlexManage、Lightstream、Perficient、Presidio |
| **南美洲** |Avanade Inc.、Venha Pra Nuvem |
## <a name="next-steps"></a>後續步驟
* 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置圖"
