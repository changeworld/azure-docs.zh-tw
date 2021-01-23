---
title: 支援可用性區域的 Azure 服務
description: 若要在 Azure 中建立高度可用且具有恢復功能的應用程式，可用性區域可提供可讓您用來執行資源的實體不同位置。
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: c63ea4f9cdb961ca492d5dcf22a89627864236cd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733195"
---
# <a name="azure-services-that-support-availability-zones"></a>支援可用性區域的 Azure 服務

可用性區域是高可用性供應專案，可保護您的應用程式和資料不受資料中心失敗的影響。 如需支援可用性區域的現有和即將推出區域清單，請參閱 [Azure 中的區域和可用性區域](az-overview.md)。  

本節列出支援可用性區域的 Azure 服務。 

每個區域中提供的服務，以及即將推出的可用性藍圖，都可在各 [區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)中找到。

所有 Azure 管理服務的架構都能從區域層級失敗中復原。 在失敗的範圍中，區域內的一或多個可用性區域失敗會有較小的失敗半徑，相較于整個區域失敗。 Azure 可以從區域內的管理服務區域層級失敗中復原。 Azure 會在區域內一次執行一個區域的重要維護，以防止任何失敗影響在區域內可用性區域部署的客戶資源。


![具有3個區域的 Azure 區域概念視圖](./media/az-region/azure-region-availability-zones.png)


支援可用性區域的 Azure 服務分為三種類別： **區域性**、 **區域冗余** 和 **非區域** 服務。 客戶工作負載可進行分類，以利用這些架構案例來滿足應用程式的效能和持久性。

- **區域性服務** –資源可以部署至特定的自選取可用性區域，以達成更嚴格的延遲或效能需求。  藉由將應用程式和資料複寫到區域內的一或多個區域，可自我架構復原。  資源可以釘選至特定區域。 例如，虛擬機器、受控磁片或標準 IP 位址可以釘選到特定區域，藉由將一或多個資源實例分散到多個區域，來提高復原能力。

- **區域冗余服務** -Azure 平臺會跨區域複寫資源和資料。  Microsoft 會管理高可用性的傳遞，因為 Azure 會自動複寫和散發區域內的實例。  例如，ZRS 會將資料複寫到三個區域，使區域失敗不會影響資料的 HA。 

- **非區域服務** -不具有特定 Azure 區域相依性的服務，讓他們能夠復原整個區域的中斷，以及全區域中斷。


若要在 Azure 上達到全面性的商務持續性，請使用「可用性區域」與 Azure 地區配對的組合來建置您的應用程式架構。 您可以使用 Azure 地區內的「可用性區域」來同步複寫應用程式和資料以提供高可用性，並以非同步方式跨 Azure 地區複寫以提供災害復原保護。 若要深入瞭解，請參閱 [使用可用性區域來建立高可用性的解決方案](/azure/architecture/high-availability/building-solutions-for-high-availability)。 


### <a name="azure-services-supporting-availability-zones"></a>支援可用性區域的 Azure 服務

- 較舊的世代虛擬機器未列于下方。 如需詳細資訊，請參閱 [上一代的虛擬機器大小](../virtual-machines/sizes-previous-gen.md)。

- 某些服務不是區域，請參閱 [Azure 中的區域和可用性區域](az-overview.md) ，以取得詳細資訊。 這些服務沒有特定 Azure 區域的相依性，因此可在全區域中斷和全區域中斷時復原。  您可以在 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)中找到非區域服務的清單。



## <a name="americas"></a>美洲

| **產品** | **Central US** | 美國東部 | **美國東部 2** | **美國西部 2** | **加拿大中部** |
|--|--|--|--|--|--|
| **Compute** |  |  |  |  |  |
| [App Service 環境 (ILB) ](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬機器](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 
| **容器** |  |  |  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Container Registry](../container-registry/zone-redundancy.md) |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  | 
| **儲存體** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal) \(部分機器翻譯\)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium 檔案儲存體](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob 儲存體](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [受控磁碟](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **網路功能** |  |  |  |  |  |
| [應用程式閘道 V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 防火牆](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [標準 IP 位址](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [負載平衡器](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬網路](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬網路 NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN 閘道](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **資料庫** |  |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 資料總管](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [適用於 MySQL 的 Azure 資料庫彈性的伺服器](../mysql/flexible-server/concepts-high-availability.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器](../postgresql/flexible-server/overview.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Azure SQL Database (一般用途層) ](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | ： heavy_check_mark： (Preview)  | ： heavy_check_mark： (Preview)  | ： heavy_check_mark： (Preview)  | :x: |
| [Azure SQL Database (Premium & 業務關鍵層) ](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |  |  |
| [事件中樞](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **整合** |  |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [服務匯流排](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **管理和治理** |  |  |  |  |  |
| [網路監看員](../network-watcher/frequently-asked-questions.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| **安全性** |  |  |  |  |  |
| [Azure Active Directory 網域服務](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>歐洲

| **產品** | **法國中部** | **北歐** | **英國南部** | 歐洲西部 |
|--|--|--|--|--|
| **Compute** |  |  |  |  |
| [App Service 環境 (ILB) ](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬機器](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **儲存體** |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal) \(部分機器翻譯\)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium 檔案儲存體](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Blob 儲存體](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [受控磁碟](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **網路功能** |  |  |  |  |
| [應用程式閘道 V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 防火牆](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [標準 IP 位址](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [負載平衡器](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬網路](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬網路 NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN 閘道](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **資料庫** |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 資料總管](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [適用於 MySQL 的 Azure 資料庫彈性的伺服器](../mysql/flexible-server/concepts-high-availability.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器](../postgresql/flexible-server/overview.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (一般用途層) ](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | ： heavy_check_mark： (Preview)  | ： heavy_check_mark： (Preview)  | :x: | ： heavy_check_mark： (Preview)  |
| [Azure SQL Database (Premium & 業務關鍵層) ](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |  |
| [事件中樞](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **整合** |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [服務匯流排](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **管理和治理** |  |  |  |  |
| [網路監看員](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: |
| **安全性** |  |  |  |  |
| [Azure Active Directory 網域服務](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>亞太地區



| **產品** | **日本東部** | **東南亞** | **澳大利亞東部** |
|--|--|--|--|
| **Compute** |  |  |  |
| [App Service 環境 (ILB) ](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬機器](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **儲存體** |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal) \(部分機器翻譯\)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium 檔案儲存體](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob 儲存體](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [受控磁碟](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **網路功能** |  |  |  |
| [應用程式閘道 V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 防火牆](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [標準 IP 位址](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [負載平衡器](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬網路](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虛擬網路 NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN 閘道](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **資料庫** |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 資料總管](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [適用於 MySQL 的 Azure 資料庫彈性的伺服器](../mysql/flexible-server/concepts-high-availability.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器](../postgresql/flexible-server/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (一般用途層) ](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | ： heavy_check_mark： (Preview)  | ： heavy_check_mark： (Preview)  | ： heavy_check_mark： (Preview)  |
| [Azure SQL Database (Premium & 業務關鍵層) ](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |
| [事件中樞](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **整合** |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [服務匯流排](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **管理和治理** |  |  |  |
| [網路監看員](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :x: | :x: |
| **安全性** |  |  |  |
| [Azure Active Directory 網域服務](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="upcoming-availability-zones"></a>即將推出的可用性區域 

Azure 提供下欄區域可用性區域支援：
- US Gov 維吉尼亞州
- 南非北部
- 美國中南部
- 德國中西部

您可以在 [這裡](https://azure.microsoft.com/global-infrastructure/geographies/)找到支援可用性區域的現有和即將推出之區域的清單。    

若要深入瞭解這些區域中的可用性區域支援，請洽詢您的 Microsoft 銷售或客戶代表。


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


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure 中的區域和可用性區域](az-overview.md) \(部分機器翻譯\)