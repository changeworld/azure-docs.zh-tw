---
title: 支援可用性區域的 Azure 服務
description: 若要在 Azure 中建立高度可用且具有恢復功能的應用程式，可用性區域可提供可讓您用來執行資源的實體不同位置。
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: e8e62d92afb70ec9d4df40f677de05583ef472b0
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955778"
---
# <a name="azure-services-that-support-availability-zones"></a>支援可用性區域的 Azure 服務

Microsoft Azure 的全域基礎結構是在每個層級設計和建立的，可為其客戶提供最高層級的冗余和復原。 Azure 基礎結構是由地理位置、區域和可用性區域所組成，可限制失敗的群發半徑，進而限制對客戶應用程式和資料的潛在影響。 Azure 可用性區域結構的開發目的是為了提供軟體和網路解決方案，以防止資料中心失敗，並為我們的客戶提供更高的高可用性 (HA) 。

「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域都是由一或多個資料中心所組成，其具有獨立的電源、冷卻和網路功能。 區域內可用性區域的實體區隔會限制對應用程式和資料免于區域失敗的影響，例如大規模的洪水、主要風暴和 superstorms，以及其他可能會中斷網站存取、安全發行、擴充的公用程式執行時間和資源可用性的事件。 可用性區域及其相關聯的資料中心的設計，就是當某個區域遭到入侵時，該區域中的其他可用性區域可支援服務、容量和可用性。

所有 Azure 管理服務的架構都能從區域層級失敗中復原。 在失敗的範圍中，區域內的一或多個可用性區域失敗會有較小的失敗半徑，相較于整個區域失敗。 Azure 可以從區域內的管理服務區域層級失敗中復原。 Azure 會在區域內一次執行一個區域的重要維護，以防止任何失敗影響在區域內可用性區域部署的客戶資源。


![具有3個區域的 Azure 區域概念視圖](./media/az-region/azure-region-availability-zones.png)


支援可用性區域的 Azure 服務分為三種類別： **區域性**、 **區域冗余** 和 **非區域** 服務。 客戶工作負載可進行分類，以利用這些架構案例來滿足應用程式的效能和持久性。

- **區域性服務** –資源可以部署至特定的自選取可用性區域，以達成更嚴格的延遲或效能需求。  藉由將應用程式和資料複寫到區域內的一或多個區域，可自我架構復原。  資源可以釘選至特定區域。 例如，虛擬機器、受控磁片或標準 IP 位址可以釘選到特定區域，藉由將一或多個資源實例分散到多個區域，來提高復原能力。

- **區域冗余服務** -Azure 平臺會跨區域複寫資源和資料。  Microsoft 會管理高可用性的傳遞，因為 Azure 會自動複寫和散發區域內的實例。  例如，ZRS 會將資料複寫到三個區域，使區域失敗不會影響資料的 HA。 

- **非區域服務** -服務一律可從 Azure 地理位置取得，並且可在全區域中斷以及全區域中斷時復原。 


若要在 Azure 上達到全面性的商務持續性，請使用「可用性區域」與 Azure 地區配對的組合來建置您的應用程式架構。 您可以使用 Azure 地區內的「可用性區域」來同步複寫應用程式和資料以提供高可用性，並以非同步方式跨 Azure 地區複寫以提供災害復原保護。 若要深入瞭解，請參閱 [使用可用性區域來建立高可用性的解決方案](/azure/architecture/high-availability/building-solutions-for-high-availability)。 

## <a name="azure-services-supporting-availability-zones"></a>支援可用性區域的 Azure 服務

 - 未列出較舊的世代虛擬機器。 如需詳細資訊，請參閱[前幾代的虛擬機器大小](../virtual-machines/sizes-previous-gen.md)。
 - 如同 [Azure 中的區域和可用性區域](az-overview.md)所述，某些服務是非區域。 這些服務沒有特定 Azure 區域的相依性，因為這種方式可復原整個區域的中斷，以及全區域中斷。  您可以在 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)中找到非區域服務的清單。


## <a name="azure-regions-with-availability-zones"></a>具有可用性區域的 Azure 區域


| 美洲           | 歐洲         | 德國              | 非洲              | 亞太地區   |
|--------------------|----------------|----------------------|---------------------|----------------|
|                    |                |                      |                     |                |
| 加拿大中部     | 法國中部 | 德國中西部 | 南非北部 * | 日本東部     |
| 美國中部         | 北歐   |                      |                     | 東南亞 |
| 美國東部            | 英國南部       |                      |                     | 澳大利亞東部 |
| 美國東部 2          | 西歐    |                      |                     |                |
| 美國中南部 |                |                      |                     |                |
| US Gov 維吉尼亞州 * |                |                      |                     |                |
| 美國西部 2        |                |                      |                     |                |


\* 若要深入瞭解這些區域中的可用性區域和可用服務支援，請洽詢您的 Microsoft 銷售或客戶代表。 針對即將支援可用性區域的即將推出區域，請參閱 [Azure 地理](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)位置。


## <a name="azure-services-supporting-availability-zones"></a>支援可用性區域的 Azure 服務

- 較舊的世代虛擬機器未列于下方。 如需詳細資訊，請參閱 [上一代的虛擬機器大小](../virtual-machines/sizes-previous-gen.md)。

- 某些服務不是區域，請參閱 [Azure 中的區域和可用性區域](az-overview.md) ，以取得詳細資訊。 這些服務沒有特定 Azure 區域的相依性，因此可在全區域中斷和全區域中斷時復原。  您可以在 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)中找到非區域服務的清單。


### <a name="zone-resilient-services"></a>區域復原服務 

： globe_with_meridians：非區域服務-服務一律可從 Azure 地理位置取得，並且可在整個區域中斷及全區域中斷時復原。

： large_blue_diamond：對全區域中斷的復原能力 

**基礎服務**

|     產品                                                    | 災害復原             |
|-----------------------------------------------------------------|:----------------------------:|
|     儲存體帳戶                                           | ： large_blue_diamond：  |
|     應用程式閘道 (V2)                                   | ： large_blue_diamond：  |
|     Azure 備份                                                | ： large_blue_diamond：  |
|     Azure Cosmos DB                                           | ： large_blue_diamond：  |
|     Azure Data Lake Storage Gen 2                             | ： large_blue_diamond：  |
|     Azure Express Route                                       | ： large_blue_diamond：  |
|     Azure 公用 IP                                           | ： large_blue_diamond：  |
|     Azure SQL Database (一般用途層)                  | ： large_blue_diamond：  |
|     Azure SQL Database (Premium & 業務關鍵層)      | ： large_blue_diamond：  |
|     磁碟儲存體                                                | ： large_blue_diamond：  |
|     事件中樞                                                  | ： large_blue_diamond：  |
|     Key Vault                                                   | ： large_blue_diamond：  |
|     Load Balancer                                               | ： large_blue_diamond：  |
|     服務匯流排                                                 | ： large_blue_diamond：  |
|     Service Fabric                                            | ： large_blue_diamond：  |
|     儲存體：經常性存取/非經常性存取 Blob 儲存層                      | ： large_blue_diamond：  |
|     儲存體：受控磁碟                                    | ： large_blue_diamond：  |
|     虛擬機器擴展集                               | ： large_blue_diamond：  |
|     虛擬機器                                          | ： large_blue_diamond：  |
|     虛擬機器： Av2-Series                              | ： large_blue_diamond：  |
|     虛擬機器： Bs-Series                               | ： large_blue_diamond：  |
|     虛擬機器： DSv2-Series                             | ： large_blue_diamond：  |
|     虛擬機器： DSv3-Series                             | ： large_blue_diamond：  |
|     虛擬機器： Dv2-Series                              | ： large_blue_diamond：  |
|     虛擬機器： Dv3-Series                              | ： large_blue_diamond：  |
|     虛擬機器： ESv3-Series                             | ： large_blue_diamond：  |
|     虛擬機器： Ev3-Series                              | ： large_blue_diamond：  |
|     虛擬網路                                           | ： large_blue_diamond：  |
|     VPN 閘道                                                 | ： large_blue_diamond：  |


**主流服務**

| 產品                                        | 災害復原 |
|-------------------------------------------------|:------------:|
| App Service 環境                        |      ： large_blue_diamond：  |
| Azure Active Directory Domain Services          |      ： large_blue_diamond：  |
| Azure Bastion                                   |      ： large_blue_diamond：  |
| Azure Cache for Redis                           |      ： large_blue_diamond：  |
| Azure 認知服務：文字分析        |      ： large_blue_diamond：  |
| Azure 資料總管                             |      ： large_blue_diamond：  |
| 適用於 MySQL 的 Azure 資料庫–有彈性的伺服器      |      ： large_blue_diamond：  |
| 適用於 PostgreSQL 的 Azure 資料庫–有彈性的伺服器 |      ： large_blue_diamond：  |
| Azure DDoS 保護                           |      ： large_blue_diamond：  |
| Azure 防火牆                                  |      ： large_blue_diamond：  |
| Azure 防火牆管理員                          |      ： large_blue_diamond：  |
| Azure Kubernetes Service (AKS)                  |      ： large_blue_diamond：  |
| Azure Private Link                              |      ： large_blue_diamond：  |
| Azure Red Hat OpenShift                         |      ： large_blue_diamond：  |
| Azure Site Recovery                             |      ： large_blue_diamond：  |
| Container Registry                              |      ： large_blue_diamond：  |
| 事件方格                                      |      ： large_blue_diamond：  |
| 網路監看員                                 |      ： large_blue_diamond：  |
| Power BI Embedded                               |      ： large_blue_diamond：  |
| Premium Blob 儲存體                            |      ： large_blue_diamond：  |
| 虛擬機器： Ddsv4-Series                  |      ： large_blue_diamond：  |
| 虛擬機器： Ddv4-Series                   |      ： large_blue_diamond：  |
| 虛擬機器： Dsv4-Series                   |      ： large_blue_diamond：  |
| 虛擬機器： Dv4-Series                    |      ： large_blue_diamond：  |
| 虛擬機器： Edsv4-Series                  |      ： large_blue_diamond：  |
| 虛擬機器： Edv4-Series                   |      ： large_blue_diamond：  |
| 虛擬機器： Esv4-Series                   |      ： large_blue_diamond：  |
| 虛擬機器： Ev4-Series                    |      ： large_blue_diamond：  |
| 虛擬機器： Fsv2-Series                   |      ： large_blue_diamond：  |
| 虛擬機器： M 系列                      |      ： large_blue_diamond：  |
| 虛擬 WAN                                     |      ： large_blue_diamond：  |


**非區域**

|     產品                                  |     災害復原    |
|-----------------------------------------------|:-------------------:|
|     Azure DNS                                 |     ： globe_with_meridians：             |
|     Azure Active Directory                  |     ： globe_with_meridians：             |
|     Azure Advisor                             |     ： globe_with_meridians：             |
|     Azure Bot 服務                        |     ： globe_with_meridians：             |
|     適用于 IoT 的 Azure Defender                  |     ： globe_with_meridians：             |
|     Azure 資訊保護            |     ： globe_with_meridians：             |
|     Azure Lighthouse                        |     ： globe_with_meridians：             |
|     Azure 受控應用程式              |     ： globe_with_meridians：             |
|     Azure 地圖服務                                |     ： globe_with_meridians：             |
|     Azure 原則                              |     ： globe_with_meridians：             |
|     Azure Resource Graph                    |     ： globe_with_meridians：             |
|     Azure Stack                               |     ： globe_with_meridians：             |
|     Azure Stack Edge                        |     ： globe_with_meridians：             |
|     Cloud Shell                               |     ： globe_with_meridians：             |
|     Microsoft Azure 的客戶加密箱    |     ： globe_with_meridians：             |
|     Microsoft Azure 對等互連服務         |     ： globe_with_meridians：             |
|     Microsoft Azure 入口網站                  |     ： globe_with_meridians：             |
|     安全性中心                         |     ： globe_with_meridians：             |
|     流量管理員                         |     ： globe_with_meridians：             |


## <a name="pricing-for-vms-in-availability-zones"></a>可用性區域中的 Vm 定價

針對部署在「可用性區域」中的虛擬機器並不會產生額外費用。 如需詳細資訊，請參閱 [頻寬定價頁面](https://azure.microsoft.com/pricing/details/bandwidth/)。


## <a name="get-started-with-availability-zones"></a>開始使用可用性區域

- [建立虛擬機器](../virtual-machines/windows/create-portal-availability-zone.md)
- [使用 PowerShell 來新增受控磁碟](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [建立區域備援虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [使用具有區域備援前端的標準 Load Balancer 來進行跨區域的 VM 負載平衡](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [使用具有區域前端的標準 Load Balancer 來進行區域內的 VM 負載平衡](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [區域備援儲存體](../storage/common/storage-redundancy.md)
- [SQL Database 一般用途層](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [事件中樞異地災害復原](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [服務匯流排異地災害復原](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [建立區域備援虛擬網路閘道](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [新增 Azure Cosmos DB 的區域多餘區域](../cosmos-db/high-availability.md#availability-zone-support)
- [開始使用 Azure Cache for Redis 可用性區域](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [建立 Azure Active Directory Domain Services 執行個體](../active-directory-domain-services/tutorial-create-instance.md)
- [建立 Azure Kubernetes Service (AKS) 使用可用性區域的叢集](../aks/availability-zones.md)


## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [Azure 中的區域和可用性區域](az-overview.md) \(部分機器翻譯\)
