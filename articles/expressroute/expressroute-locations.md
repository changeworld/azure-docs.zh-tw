---
title: 連線提供者和位置︰Azure ExpressRoute |Microsoft Docs
description: 本文提供提供服務所在位置以及如何連線到 Azure 區域的詳細概觀。 依連線提供者排序。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/15/2020
ms.author: cherylmc
ms.openlocfilehash: 4d4e47808355833ff7ecfb66db13ee86dc4a6402
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251034"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作夥伴和對等互連位置

> [!div class="op_single_selector"]
> * [依提供者的位置](expressroute-locations.md)
> * [依位置的提供者](expressroute-locations-providers.md)


本文中的表格提供 ExpressRoute 地理涵蓋範圍和位置、ExpressRoute 連線提供者，以及 ExpressRoute 系統整合者（SIs）的相關資訊。

> [!Note]
> Azure 區域和 ExpressRoute 位置是兩個不同的概念，瞭解兩者之間的差異對於探索 Azure 混合式網路連線非常重要。 
>
>

## <a name="azure-regions"></a>Azure 區域
Azure 區域是「Azure 計算」、「網路」和「儲存體」資源所在的全球資料中心。 建立 Azure 資源時，客戶需要選取資源位置。 資源位置會決定資源建立所在的 Azure 資料中心（或可用性區域）。

## <a name="expressroute-locations"></a>ExpressRoute 位置
ExpressRoute 位置（有時稱為「對等位置」或「符合我的位置」）是 Microsoft Enterprise Edge （MSEE）裝置所在的共同位置設施。 ExpressRoute 位置是 Microsoft 網路的進入點，而且會全域散發，讓客戶有機會連接到全球各地的 Microsoft 網路。 這些位置是 ExpressRoute 合作夥伴和 ExpressRoute Direct 客戶向 Microsoft 網路發出交叉連線的地方。 一般而言，ExpressRoute 位置不需要符合 Azure 區域。 例如，客戶可以在*西雅圖*對等互連位置中，建立資源位置為*美國東部*的 ExpressRoute 線路。

如果您至少與地緣政治區域內的一個 ExpressRoute 位置連線，您將有權存取地緣政治區域內所有區域中的 Azure 服務。

## <a name="locations"></a>地緣政治區域內 ExpressRoute 位置的 Azure 區域。
下表提供地緣政治區域內 ExpressRoute 位置的 Azure 區域對應。

| **地緣政治區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **澳洲政府** |澳大利亞中部、澳大利亞中部 2 |坎培拉、坎培拉 2 |
| **歐洲** | 法國中部、法國南部、德國北部、德國中西部、北歐、挪威東部、挪威西部、瑞士北部、瑞士西部、英國西部、英國南部、西歐 |阿姆斯特丹、阿姆斯特丹2、哥本哈根、都柏林、法蘭克福、Geneva、倫敦、倫敦2、馬賽、米蘭、慕尼克、紐波特（威爾士）、Oslo、巴黎、Stavanger、斯德哥爾摩、蘇黎世 |
| **北美洲** |美國東部、美國西部、美國東部 2、美國西部 2、美國中部、美國中南部、美國中北部、美國中西部、加拿大中部、加拿大東部 |亞特蘭大、芝加哥、達拉斯、丹佛、內華達州、洛杉磯、邁阿密、紐約、San Antonio、西雅圖、矽谷、矽 Valley2、華盛頓特區、華盛頓特區、蒙特利爾、魁北克市、多倫多 |
| **亞洲** | 東亞、東南亞 | 曼谷，香港特別行政區，香港 Kong2，雅加達，吉隆坡，新加坡，新加坡2，臺北 |
| **印度** | 印度西部、印度中部、印度南部 |辰內、辰內 2、孟買、孟買 2 |
| **日本** | 日本西部、日本東部 |大阪，東京，Tokyo2 |
| **大洋洲** | 澳大利亞東南部、澳大利亞東部 |奧克蘭，墨爾本，Sydney2，悉尼， |
| **南韓** | 南韓中部、南韓南部 |釜山、首爾|
| **阿拉伯聯合大公國** | 阿拉伯聯合大公國中部，阿拉伯聯合大公國北部 | 杜拜、Dubai2 |
| **南非** | 南非西部，南非北部 |開普敦、約翰尼斯堡 |
| **南美洲** | 巴西南部 |聖保羅 |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>國家雲端的區域和地理政治界限
下表提供國家雲端的區域和地理政治界限等資訊。

| **地緣政治區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **US Gov 雲端** |US Gov 亞利桑那州、US Gov 愛荷華州、US Gov 德克薩斯州、US Gov 維吉尼亞州、US DoD 中部、US DoD 東部  |亞特蘭大、芝加哥、達拉斯、紐約、Phoenix、San Antonio、西雅圖、矽谷、華盛頓特區 |
| **中國東部** |中國東部、中國東部 2 |上海、上海 2 |
| **中國北部** |中國北部、中國北部 2 |北京、北京 2 |
| **德國** |德國中部、德國東部 |柏林、法蘭克福 |

標準 ExpressRoute SKU 不支援跨地緣政治區域的連線。 您必須啟用 ExpressRoute 進階附加元件，以支援全球連線。 不支援連線至國家雲端環境。 如果有需要的話，您可以聯絡您的連線提供者。

## <a name="partners"></a>ExpressRoute 連線提供者

下表顯示服務提供者的位置。 如果您想依位置檢視可用的提供者，請參閱[位置服務提供者](expressroute-locations-providers.md)。


### <a name="global-commercial-azure"></a>全球商業 Azure

| **服務提供者** | **Microsoft Azure** | **Office 365**  | **位置** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |支援 |支援 |墨爾本、雪梨 |
| **[Airtel](https://www.airtel.in/business/#/)** | 支援 | 支援 | 辰內 2、孟買 2 |
| **[AIS](http://business.ais.co.th/solution/microsoft-azure.html?category=cloud)** | 支援 | 支援 | 曼谷 |
| **[Aryaka Networks](https://www.aryaka.com/)** |支援 |支援 |阿姆斯特丹、芝加哥、達拉斯、香港特別行政區、聖保羅、西雅圖、矽谷、新加坡、東京、華盛頓特區 |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |支援 |支援 |聖保羅 |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |支援 |支援 |阿姆斯特丹、芝加哥、達拉斯、倫敦、矽谷、新加坡、雪梨、東京、多倫多、華盛頓特區 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | 支援 | 支援 | 東京 |
| **[BCX](https://www.bcx.co.za/solutions)** |支援 |支援 |維德角|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |支援 |支援 |蒙特婁、多倫多、魁北克市 |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |支援 |支援 |阿姆斯特丹、香港特別行政區、約翰尼斯堡、倫敦、紐波特（威爾士）、聖保羅、矽谷、新加坡、悉尼、東京、華盛頓特區 |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |支援 |支援 |邁阿密 |
| **CDC** | 支援 | 支援 | 坎培拉、坎培拉 2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |支援 |支援 |阿姆斯特丹2、芝加哥、香港、內華達州、倫敦2、紐約、巴黎、San Antonio、矽谷、東京、多倫多、華盛頓特區、華盛頓特區 |
| **[首席電信](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |支援 |支援 |香港特別行政區，臺北 |
| **中國 Mobile 國際** |支援 |支援 | 新加坡 |
| **China Telecom Global** |支援 |支援 |香港特別行政區 |
| **中國 Unicom Global** |支援 |支援 | 新加坡2 |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |支援 |支援 |芝加哥、達拉斯、蒙特婁、多倫多、華盛頓特區 |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |支援 |支援 |阿姆斯特丹、阿姆斯特丹2、芝加哥、都柏林、法蘭克福、倫敦、倫敦2、紐波特、紐約、大阪、巴黎、矽谷、矽 Valley2、新加坡2、東京、華盛頓特區 |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |支援 |支援 |芝加哥、矽谷、華盛頓特區 |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |支援 |支援 |芝加哥、丹佛、洛杉磯、紐約、矽谷、矽 Valley2、華盛頓特區、華盛頓特區 |
| **[取消 CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | 支援 |支援 |阿姆斯特丹2、法蘭克福、馬賽|
| **[Devoli](https://devoli.com/expressroute)** | 支援 |支援 | 奧克蘭，墨爾本，悉尼 |
| **du datamena** |支援 |支援 | Dubai2 |
| **eir** |支援 |支援 |都柏林|
| **[Epsilon 全球通訊](https://www.epsilontel.com/solutions/direct-cloud-connect)** |支援 |支援 |新加坡、新加坡 2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |支援 |支援 |阿姆斯特丹、亞特蘭大、芝加哥、達拉斯、都柏林、法蘭克福、Geneva、香港特別行政區、倫敦、倫敦2、洛杉磯、墨爾本、邁阿密、紐約、大阪、巴黎、聖保羅、西雅圖、矽谷、新加坡、斯德哥爾摩、悉尼、東京、多倫多、華盛頓特區 |
| **Etisalat 阿拉伯聯合大公國** |支援 |支援 |杜拜|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |支援 |支援 |阿姆斯特丹、阿姆斯特丹2、都柏林、倫敦 |
| **FarEasTone** |支援 |支援 |台北|
| **GÉANT** |支援 |支援 |阿姆斯特丹、法蘭克福、馬賽 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | 支援| 支援 | 辰內，孟買 |
| **Intelsat** | 支援 | 支援 | 華盛頓特區 |
| **[InterCloud](https://www.intercloud.com/)** |支援 |支援 |阿姆斯特丹、芝加哥、香港、倫敦、紐約、巴黎、矽谷、新加坡、華盛頓特區、蘇黎世 |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |支援 |支援 |芝加哥、達拉斯、矽谷、華盛頓特區 |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |支援 |支援 |大阪、東京 |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |支援 |支援 |開普敦、約翰尼斯堡、倫敦 |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |支援 |支援 |阿姆斯特丹、阿姆斯特丹2、哥本哈根、都柏林、法蘭克福、倫敦、馬賽、巴黎、蘇黎世 |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|支援 |支援 | 阿姆斯特丹、倫敦2、矽谷、多倫多、華盛頓特區 |
| **Jaguar 網路** |支援 |支援 |馬賽|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |支援 |支援 |倫敦 |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |支援 |支援 |首爾 |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | 支援 |支援 |奧克蘭，悉尼 |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | 支援 | 支援 | 阿姆斯特丹 |
| **[KT](https://cloud.kt.com/portal/ktcloudportal.epc.productintro.partnershipcloud_ConnectHub.html)** | 支援 | 支援 | 首爾 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |支援 |支援 |阿姆斯特丹、芝加哥、達拉斯、倫敦、新港 (威爾斯)、聖保羅、西雅圖、矽谷、新加坡、華盛頓特區 |
| **LG CNS** |支援 |支援 |釜山、首爾 |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |支援 |支援 |開普敦、約翰尼斯堡 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |支援 |支援 |阿姆斯特丹、亞特蘭大、奧克蘭、芝加哥、達拉斯、丹佛、Dubai2、都柏林、法蘭克福、Geneva、香港特別行政區、內華達州、倫敦、倫敦2、洛杉磯、墨爾本、邁阿密、蒙特利爾、紐約、Oslo、達拉斯、魁北克市、San Antonio、西雅圖、矽谷、新加坡、新加坡2、悉尼、Sydney2、東京、多倫多、華盛頓特區、蘇黎世 |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |支援 |支援 |倫敦 |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |支援 |支援 |達拉斯、洛杉磯、邁阿密、聖保羅、華盛頓特區 |
| **[新一代資料](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |支援 |支援 |Newport(Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |支援 |支援 |墨爾本、通過、悉尼、Sydney2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |支援 |支援 |阿姆斯特丹、香港特別行政區、倫敦、洛杉磯、大阪、新加坡、悉尼、東京、華盛頓特區 |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |支援 |支援 |東京 |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |支援 |支援 |大阪 |
| **[Optus](https://www.optus.com.au/enterprise/)** |支援 |支援 |墨爾本、雪梨 |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |支援 |支援 |阿姆斯特丹、阿姆斯特丹2、Dubai2、法蘭克福、香港特別行政區、約翰尼斯堡、倫敦、巴黎、聖保羅、矽谷、新加坡、悉尼、東京、華盛頓特區 |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | 支援 | 支援 | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |支援 |支援 |芝加哥、矽谷、華盛頓特區 |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |支援 |支援 |芝加哥，香港特別行政區，倫敦 |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |支援 |支援 |首爾 |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | 支援 |支援 | 華盛頓 |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |支援 |支援 |辰內、孟買 2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |支援 |支援 |新加坡、新加坡 2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |支援 |支援 |大阪、東京 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |支援 |支援 |奧克蘭，悉尼 |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |支援 |支援 |芝加哥、矽谷、華盛頓特區 |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | 支援 | 支援 | 蘇黎世 |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |支援 |支援 |阿姆斯特丹、辰內、香港特別行政區、倫敦、孟買、聖保羅、矽谷、新加坡、華盛頓特區 |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |支援 |支援 |阿姆斯特丹、聖保羅 |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |支援 |支援 |倫敦、倫敦2 |
| **Telenor** |支援 |支援 |阿姆斯特丹、倫敦、Oslo |
| **[Telia Carrier](https://www.teliacarrier.com/)** | 支援 | 支援 |阿姆斯特丹、芝加哥、達拉斯、法蘭克福、香港、倫敦、Oslo、巴黎、矽谷、斯德哥爾摩、華盛頓特區 |
| **Telmex Uninet**| 支援 | 支援 | 達拉斯 |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |支援 |支援 |墨爾本、新加坡、雪梨 |
| **[Telus](https://www.telus.com)** |支援 |支援 |蒙特利爾、西雅圖、多倫多 |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |支援 |支援 |開普敦、約翰尼斯堡 |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | 支援 | 支援 | 吉隆坡 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |支援 |支援 |達拉斯、洛杉磯|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |支援 |支援 |聖保羅 |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |支援 |支援 |阿姆斯特丹、芝加哥、達拉斯、香港特別行政區、倫敦、孟買、矽谷、新加坡、悉尼、東京、多倫多、華盛頓特區 |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | 支援 | 支援 | 華盛頓特區 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | 支援 | 支援 | 奧克蘭，悉尼 |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |支援 |支援 |阿姆斯特丹2、倫敦、新加坡 |
| **[Vodafone 概念](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | 支援 | 支援 | 孟買、孟買 2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |支援 |支援 |阿姆斯特丹、芝加哥、達拉斯、丹佛、倫敦、洛杉磯、蒙特利爾、紐約、巴黎、西雅圖、矽谷、多倫多、華盛頓特區、華盛頓特區 |

 **+** 表示即將推出

### <a name="national-cloud-environment"></a>國家雲端環境

Azure 國家/地區雲端彼此隔離，而不是全球商業 Azure。 一個 Azure 雲端的 ExpressRoute 無法連線到其他人的 Azure 區域。 

### <a name="us-government-cloud"></a>美國政府雲端

| **服務提供者** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |支援 |支援 |芝加哥、Phoenix、矽谷、華盛頓特區 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |支援 |支援 |紐約、Phoenix、San Antonio、華盛頓特區 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |支援 |支援 |亞特蘭大、芝加哥、達拉斯、紐約、西雅圖、矽谷、華盛頓特區 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |支援 |支援 |芝加哥、矽谷、華盛頓特區 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |支援 | 支援 | 芝加哥、達拉斯、San Antonio、西雅圖、華盛頓特區 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |支援 |支援 |芝加哥、達拉斯、紐約、矽谷、華盛頓特區 |

### <a name="china"></a>中國

| **服務提供者** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **China Telecom** |支援 |不支援 |北京，北京2，上海，上海2 |
| **中國 Unicom** | 支援 | 不支援 | 北京2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |支援 |不支援 |北京 2、上海 2 |

若要深入了解，請參閱 [ExpressRoute (中國)](http://www.windowsazure.cn/home/features/expressroute/)。

### <a name="germany"></a>Germany

| **服務提供者** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |支援 |不支援 |法蘭克福 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |支援 |不支援 |法蘭克福 |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |支援 |不支援 |柏林 |
| **Interxion** |支援 |不支援 |法蘭克福 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |支援  | 不支援 | 柏林 |
| **T-Systems** |支援 |不支援 |柏林 |

## <a name="connectivity-through-exchange-providers"></a>透過 Exchange 提供者的連線能力

如果上一節中未列出您的連線提供者，您仍然可以建立連線。

* 請洽詢您的連線提供者，以了解他們是否連線到上方表格中列出的任何 Exchange 提供者。 您可以檢查下列連結，以收集 Exchange 提供者所提供之服務的相關詳細資訊。 已有數個連線提供者連線到乙太網路 Exchange。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* 請您的連線提供者將您的網路延伸到選擇的對等互連位置。
  * 請確保您的連線提供者以高可用性的方式延伸您的連線，因此不會有單一失敗點。
* 排序一個 ExpressRoute 循環，將 Exchange 視為連線至 Microsoft 的連線提供者。
  * 依照 [建立 ExpressRoute 循環](expressroute-howto-circuit-classic.md) 中的步驟來設定連線。

## <a name="connectivity-through-satellite-operators"></a>透過附屬運算子的連線能力
如果您是遠端且沒有光纖連線，或者您想要探索其他連線選項，可以檢查下列附屬操作員。 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>透過其他服務提供者的連線能力

| **連線提供者** | **Exchange** | **位置** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |新加坡 |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix、Cologix | 多倫多、蒙特婁 |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |紐約、華盛頓特區 |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |東京 |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |達拉斯|
| **[Beanfield Metroconnect](https://www.beanfield.com/cloud-exchange/)** |Megaport |多倫多|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | 倫敦 |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | 阿姆斯特丹、法蘭克福、倫敦、新加坡、華盛頓特區 |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | 東京 |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix、Megaport | 達拉斯 |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | 香港特別行政區 |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix、Megaport | 法蘭克福、漢堡 |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | 阿姆斯特丹 | 
| **[CMC 電信](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | 新加坡 | 
| **[Aptum 技術](https://aptum.com/services/cloud/managed-azure/)**| Equinix | 蒙特婁、多倫多 |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | 倫敦 |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | 達拉斯、矽谷、華盛頓特區 |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | 亞特蘭大、芝加哥、達拉斯、洛杉磯、紐約、華盛頓特區 |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | 達拉斯 |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | 倫敦、新加坡、華盛頓特區 |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | 阿姆斯特丹 |
| **[指數 E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | 倫敦 |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | 阿姆斯特丹 |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | 魁北克市 |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | 華盛頓 |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | 阿姆斯特丹 |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | 倫敦、斯勞 |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | 多倫多 |
| **[Kaalam 電信巴林 b. C](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| 層級3通訊 |阿姆斯特丹 |
| **LGA Telecom** |Equinix |新加坡|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |香港特別行政區 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | 雪梨 |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | 阿姆斯特丹 |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | 華盛頓 |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | 開普敦、約翰尼斯堡 |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | 倫敦 |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | 阿姆斯特丹、法蘭克福 |
| **[電信後盧森堡](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | 阿姆斯特丹 |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | 阿姆斯特丹、都柏林、倫敦、巴黎 |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | 法蘭克福 |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | 阿姆斯特丹 |
| **Rogers** | Cologix、Equinix | 蒙特婁、多倫多 |
| **[頻譜企業](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | 芝加哥、達拉斯、洛杉磯、紐約、矽谷 | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | 倫敦 | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | 阿姆斯特丹 | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | 阿姆斯特丹 |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | 阿姆斯特丹、法蘭克福 |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | 阿姆斯特丹 |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | 多倫多 | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | 新加坡 |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | 聖保羅 |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | 紐約 |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | 芝加哥、矽谷、華盛頓特區 |
| **[X2nsat Inc。](https://www.x2nsat.com/expressroute/)** |Coresite |矽谷、矽谷2|
| **Zain** |Equinix |倫敦|
| **[Zertia](https://www.zertia.es)**| Level 3 | 馬德里 |
| **[Zirro](https://zirro.com/services/)**| Cologix、Equinix | 蒙特婁、多倫多 |

## <a name="connectivity-through-datacenter-providers"></a>透過資料中心提供者的連線能力

| **提供者** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport、PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport、PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX 觸及，Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport、PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX 觸及、Megaport、PacketFabric |
| **[QTS 資料中心](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport、PacketFabric |
| **[串流資料中心]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX 觸及、Megaport、PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX 觸及、Megaport |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>透過國家/地區研究與教育網路（NREN）的連線能力

| **提供者**|
| --- |
| **AARNET**| 
| **DeIC，透過 GÉANT**|
| **GARR，透過 GÉANT**|
| **GÉANT**|
| **HEAnet，透過 GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS，透過 GÉANT**|
| **SINET**|
| **Surfnet，透過 GÉANT**|

* 如果連線提供者未列於此處，請查看他們是否已連線到上列的任何 ExpressRoute Exchange 提供者。

## <a name="expressroute-system-integrators"></a>ExpressRoute 系統整合者
根據您的網路規模，為符合您的需求而啟用私人連線可能有一定的難度。 您可以使用下表所列出的任何系統整合者來協助您開始使用 ExpressRoute。

| **系統整合者** | **Continent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | 歐洲 |
| **[Avanade Inc.](https://www.avanade.com/)** | 亞洲、歐洲、北美洲、南美洲 |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | 歐洲
| **[Ensyst](https://www.ensyst.com.au)** | Asia
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | 北美洲 |
| **[FlexManage](https://www.flexmanage.com/cloud)** | 北美洲 |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | 北美洲 |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | 澳洲 |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | 澳洲 |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | 歐洲 (德國) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | 歐洲 |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | 歐洲 |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | 歐洲 |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 北美洲 |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | 北美洲 |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | 歐洲 |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | 南美洲 |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | 澳洲 |

## <a name="next-steps"></a>後續步驟
* 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置圖"
