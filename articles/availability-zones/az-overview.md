---
title: Azure 中的區域和可用性區域
description: 瞭解 Azure 中的區域和可用性區域，以符合您的技術與法規需求。
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 485042c795fd94ef7daa8e00b9869190e9df61c8
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678547"
---
# <a name="regions-and-availability-zones-in-azure"></a>Azure 中的區域和可用性區域

Microsoft Azure 服務可在全球使用，以達到最佳等級來推動您的雲端作業。 您可以根據技術與法規考慮，選擇最適合您需求的區域：服務功能、資料落地、合規性需求及延遲。

## <a name="terminology"></a>術語

若要深入瞭解 Azure 中的區域和可用性區域，它有助於瞭解重要的條款或概念。

| 詞彙或概念 | 描述 |
| --- | --- |
| region | 在延遲定義的周邊內部署的一組資料中心，並透過專用的區域低延遲網路進行連線。 |
| geography | 全球的區域，其中至少包含一個 Azure 區域。 地理位置會定義可保留資料落地和合規性界限的離散市場。 不同的地理區讓具有特殊資料落地與合規性需求的客戶，可以將他們的資料與應用程式存放在彼此鄰近的區域。 每個地理位置都有容錯能力，可透過其連線到專用的高容量網路基礎結構，來承受完成區域失敗。 |
| 可用性區域 | 區域內的唯一實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 |
| 建議的區域 | 提供最廣泛服務功能的區域，其設計目的是要支援現在或未來的可用性區域。 這些是依 **建議** 在 Azure 入口網站中指定的。 |
| 替代 (其他) 區域 | 在資料落地界限內擴充 Azure 使用量的區域，其中也存在建議的區域。 替代區域有助於將延遲優化，並為嚴重損壞修復需求提供第二個區域。 雖然 Azure 會定期評估這些區域，以判斷是否應該成為建議的區域) ，但它們並不是設計來支援可用性區域 (。 這些會在 Azure 入口網站中指定為 **其他**。 |
| 基礎服務 | 當區域正式推出時，可在所有區域中使用的核心 Azure 服務。 |
| 主流服務 | 一項 Azure 服務，可在區域/服務正式運作的12個月內，或在替代區域中的需求導向可用性內的所有建議區域中使用。 |
| 特製化服務 | 一項 Azure 服務，其在自訂/特製化硬體所支援的區域之間是需求導向的可用性。 |
| 區域服務 | 部署地區的 Azure 服務，可讓客戶指定將部署服務的區域。 如需完整清單，請參閱 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=all)。 |
| 非區域服務 | 在特定 Azure 區域沒有相依性的 Azure 服務。 非區域服務會部署至兩個或多個區域，而如果發生區域失敗，則另一個區域中的服務實例會繼續為客戶提供服務。 如需完整清單，請參閱 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=all)。 |

## <a name="regions"></a>區域

區域是在延遲定義的周邊內部署的一組資料中心，並透過專用的區域低延遲網路進行連線。 Azure 可讓您彈性地部署需要的應用程式，包括跨多個區域，以提供跨區域復原。 如需詳細資訊，請參閱 [復原要件的總覽](/azure/architecture/framework/resiliency/overview)。

## <a name="availability-zones"></a>可用性區域

可用性區域是高可用性供應專案，可保護您的應用程式和資料不受資料中心失敗的影響。 「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原，所有已啟用的地區中至少要有三個不同的區域。 地區內「可用性區域」的實體區隔可保護應用程式和資料不受資料中心故障影響。 區域備援服務會將應用程式和資料複寫至所有「可用性區域」，以防出現單一失敗點。 使用「可用性區域」時，Azure 可提供業界最佳的 99.99% VM 執行時間 SLA。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 說明保證的 Azure 整體可用性。

Azure 區域中的可用性區域是由容錯網域和更新網域組成。 例如，如果您在 Azure 區域中建立橫跨三個區域的三個 (或更多) VM，您的 VM 會有效地分散到三個容錯網域和三個更新網域。 Azure 平臺會在更新網域中辨識此分佈，以確保不會將不同區域中的 Vm 同時更新。

藉由將運算、儲存體、網路及資料資源共置於某個區域內並複寫至其他區域，即可讓您的應用程式架構內建高可用性。 支援「可用性區域」的 Azure 服務分成兩個類別：

- **區域性服務** –資源釘選到特定區域 (例如虛擬機器、受控磁片、標準 IP 位址) 或
- **區域冗余服務** ：當 Azure 平臺跨區域自動複寫時 (例如，區域冗余儲存體、SQL Database) 。

若要在 Azure 上達到全面性的商務持續性，請使用「可用性區域」與 Azure 地區配對的組合來建置您的應用程式架構。 您可以使用 Azure 地區內的「可用性區域」來同步複寫應用程式和資料以提供高可用性，並以非同步方式跨 Azure 地區複寫以提供災害復原保護。
 
![地區中細分至一個區域的概念性檢視](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> 可用性區域識別碼 (上圖中的數位1、2和 3) 會以邏輯方式對應至每個訂用帳戶的實際實體區域。 這表示在指定的訂用帳戶中，可用性區域1可能會參考不同訂用帳戶中的可用性區域1以外的實體區域。 因此，建議您不要依賴跨不同訂用帳戶的可用性區域識別碼來放置虛擬機器。

## <a name="region-and-service-categories"></a>區域和服務類別

Azure 對於跨區域的 Azure 服務可用性的方法，最好是藉由表達建議區域和替代區域中提供的服務來描述。

- **建議的區域** ：提供最廣泛服務功能的區域，其設計目的是要支援現在或未來的可用性區域。 這些是依 **建議** 在 Azure 入口網站中指定的。
- **替代 (其他) 區域** -此區域會在建議的區域同時存在的資料落地界限內，擴充 Azure 的使用量。 替代區域有助於將延遲優化，並為嚴重損壞修復需求提供第二個區域。 雖然 Azure 會定期評估這些區域，以判斷是否應該成為建議的區域) ，但它們並不是設計來支援可用性區域 (。 這些會在 Azure 入口網站中指定為 **其他**。

### <a name="comparing-region-types"></a>比較區欄位型別

Azure 服務分為三種類別：基本、主流和特製化的服務。 將服務部署到任何指定區域的 Azure 一般原則主要是依區欄位型別、服務類別和客戶需求來推動：

- **基礎** –當區域正式推出，或在新基礎服務的12個月內正式推出時，適用于所有建議和替代區域。
- **主流** –在區域/服務正式運作的12個月內，所有建議的區域皆可使用;替代區域中的需求導向 (許多都已部署到) 的大量替代區域中。
- **特製** 化的目標服務供應專案，通常是以產業為主或受自訂/特製化硬體支援。 跨區域的需求導向可用性 (許多都已部署到) 的一大部分建議區域中。

若要查看特定區域中所部署的服務，以及區域中服務預覽或正式運作的未來藍圖，請參閱 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=all)。

如果特定區域無法使用服務供應專案，您可以聯絡您的 Microsoft 業務代表，以分享您的興趣。

| 區域類型 | 非區域 | 基礎 | 主流 | 特製化 | 可用性區域 | 資料存留處 |
| --- | --- | --- | --- | --- | --- | --- |
| 建議 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 需求導向 | :heavy_check_mark: | :heavy_check_mark: |
| 替代 | :heavy_check_mark: | :heavy_check_mark: | 需求導向 | 需求導向 | N/A | :heavy_check_mark: |

### <a name="services-by-category"></a>依類別分類的服務

如先前所述，Azure 會將服務分類為三種類別：基本、主流和特製化。 服務類別會在正式運作時指派。 服務通常會以特製化服務的形式啟動其生命週期，並隨著需求和使用率增加而提升為主流或基礎。 下表列出服務的「基本」、「主流」或「特製化」類別。 您應該注意有關資料表的下列資訊：

- 某些服務是非區域。 如需詳細資訊和非區域服務清單，請參閱 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。
- 未列出較舊的世代虛擬機器。 如需詳細資訊，請參閱 [上一代虛擬機器大小](../virtual-machines/sizes-previous-gen.md)的檔。

> [!div class="mx-tableFixed"]
> | 基礎 | 主流 | 特製化 |
> | --- | --- | --- |
> | 帳戶儲存 | API 管理 | 適用於 FHIR 的 Azure API |
> | 應用程式閘道 | 應用程式設定 | Azure Analysis Services |
> | Azure 備份 | App Service 方案 | Azure 區塊鏈服務 |
> | Azure Cosmos DB | 自動化 | Azure 藍圖 |
> | Azure Data Lake Storage Gen2 | Azure Active Directory Domain Services | 適用於 MariaDB 的 Azure 資料庫 |
> | Azure ExpressRoute | Azure Bastion | Azure 專用 HSM |
> | Azure SQL Database | Azure Cache for Redis | Azure Dev Spaces |
> | 雲端服務 | Azue 認知搜尋 | Azure Digital Twins |
> | 雲端服務： Av2-Series | Azure 資料總管 | Azure 實驗室服務 |
> | 雲端服務： Dv2-Series | Azure Data Share | Azure NetApp Files |
> | 雲端服務： Dv3-Series | 適用於 MySQL 的 Azure 資料庫 | Azure Quantum |
> | 雲端服務： Ev3-Series | 適用於 PostgreSQL 的 Azure 資料庫 | Azure 春季雲端服務 |
> | 雲端服務：實例層級 Ip | Azure 資料庫移轉服務 | Azure Time Series Insights |
> | 雲端服務：保留的 IP | Azure Databricks | 由 CloudSimple 提供的 Azure VMware 解決方案 |
> | 磁碟儲存體 | Azure DDoS 保護 | 雲端服務： G 系列 |
> | 事件中樞 | Azure DevTest Labs | 雲端服務： H 系列 |
> | Key Vault | Azure 防火牆 | 認知服務：自訂視覺 |
> | 負載平衡器 | Azure 防火牆管理員 | 認知服務：說話者辨識 |
> | 服務匯流排 | Azure Functions | Data Box Heavy |
> | Service Fabric | Azure HPC Cache | 資料目錄 |
> | 虛擬機器擴展集 | Azure IoT 中樞 | Data Factory： Data Factory V1 |
> | 虛擬機器 | Azure Kubernetes Service (AKS) | Data Lake Analytics |
> | 虛擬機器： Av2-Series | Azure Machine Learning | Azure Machine Learning Studio (傳統) |
> | 虛擬機器： Bs-Series | Azure Private Link | Microsoft Genomics |
> | 虛擬機器： DSv2-Series | Azure Red Hat OpenShift | 遠端轉譯 |
> | 虛擬機器： DSv3-Series | Azure SignalR 服務 | Spatial Anchors |
> | 虛擬機器： Dv2-Series | Azure Site Recovery | StorSimple |
> | 虛擬機器： Dv3-Series | Azure Stack Hub | 影片索引器 |
> | 虛擬機器： ESv3-Series | Azure 串流分析 | 虛擬機器： DASv4-Series |
> | 虛擬機器： Ev3-Series | Azure Synapse Analytics | 虛擬機器： DAv4-Series |
> | 虛擬機器： F 系列 | Batch | 虛擬機器： DCsv2 系列 |
> | 虛擬機器： FS-Series | 雲端服務： M 系列 | 虛擬機器： EASv4-Series |
> | 虛擬機器：實例層級 Ip | 認知服務 | 虛擬機器： EAv4-Series |
> | 虛擬機器：保留的 IP | 認知服務：電腦視覺 | 虛擬機器： G 系列 |
> | 虛擬網路 | 認知服務：內容仲裁 | 虛擬機器： GS-Series |
> | VPN 閘道 | 認知服務：臉部 | 虛擬機器： HBv1-Series |
> |  | 認知服務：表單辨識器 | 虛擬機器： HBv2-Series |
> |  | 認知服務： Language Understanding | 虛擬機器： HCv1-Series |
> |  | 認知服務：QnA Maker | 虛擬機器： H 系列 |
> |  | 認知服務：語音服務 | 虛擬機器： LS-Series |
> |  | 容器執行個體 | 虛擬機器： LSv2-Series |
> |  | Container Registry | 虛擬機器： Mv2-Series |
> |  | Data Factory | 虛擬機器： NC-Series |
> |  | 事件方格 | 虛擬機器： NCv2-Series |
> |  | HDInsight | 虛擬機器： NCv3-Series |
> |  | Logic Apps | 虛擬機器： NDs-Series |
> |  | 媒體服務 | 虛擬機器： NDv2-Series |
> |  | 網路監看員 | 虛擬機器： NV-Series |
> |  | 通知中樞 | 虛擬機器： NVv3-Series |
> |  | Power BI Embedded | 虛擬機器： NVv4-Series |
> |  | Premium Blob 儲存體 | 虛擬機器： Azure 上的 SAP HANA 大型執行個體 |
> |  | Premium 檔案儲存體 | Visual Studio 應用程式中心 |
> |  | 儲存體：封存儲存體 |  |
> |  | Ultra 磁碟儲存體 |  |
> |  | 虛擬機器： Ddsv4-Series |  |
> |  | 虛擬機器： Ddv4-Series |  |
> |  | 虛擬機器： Dsv4-Series |  |
> |  | 虛擬機器： Dv4-Series |  |
> |  | 虛擬機器： Edsv4-Series |  |
> |  | 虛擬機器： Edv4-Series |  |
> |  | 虛擬機器： Esv4-Series |  |
> |  | 虛擬機器： Ev4-Series |  |
> |  | 虛擬機器： Fsv2-Series |  |
> |  | 虛擬機器： M 系列 |  |
> |  | Virtual WAN |  |

###  <a name="services-resiliency"></a>服務復原

所有 Azure 管理服務的架構都能從區域層級失敗中復原。 在失敗的範圍中，區域內的一或多個可用性區域失敗會有較小的失敗半徑，相較于整個區域失敗。 Azure 可以從區域內的管理服務的區域層級失敗，或從另一個 Azure 區域復原。 Azure 會在區域內一次執行一個區域的重要維護，以防止任何失敗影響在區域內可用性區域部署的客戶資源。

### <a name="pricing-for-vms-in-availability-zones"></a>可用性區域中的 Vm 定價

針對部署在「可用性區域」中的虛擬機器並不會產生額外費用。 有兩部 (或以上) VM 部署在 Azure 區域內兩個以上的「可用性區域」中時，即可提供 99.99% VM 執行時間 SLA。 在可用性區域之間將額外產生 VM 對 VM 的資料傳輸費用。 如需詳細資訊，請檢閱[頻寬定價](https://azure.microsoft.com/pricing/details/bandwidth/)頁面。

### <a name="get-started-with-availability-zones"></a>開始使用可用性區域

- [建立虛擬機器](../virtual-machines/windows/create-portal-availability-zone.md)
- [使用 PowerShell 來新增受控磁碟](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [建立區域備援虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [使用具有區域備援前端的標準 Load Balancer 來進行跨區域的 VM 負載平衡](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [使用具有區域前端的標準 Load Balancer 來進行區域內的 VM 負載平衡](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [區域備援儲存體](../storage/common/storage-redundancy.md)
- [SQL Database 一般用途層](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [事件中樞異地災害復原](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [服務匯流排異地災害復原](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [建立區域備援虛擬網路閘道](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [新增 Azure Cosmos DB 的區域多餘區域](../cosmos-db/high-availability.md#availability-zone-support)
- [消費者入門 Azure Cache for Redis 可用性區域](https://aka.ms/redis/az/getstarted)
- [建立 Azure Active Directory Domain Services 執行個體](../active-directory-domain-services/tutorial-create-instance.md)
- [建立 Azure Kubernetes Service (AKS) 使用可用性區域的叢集](../aks/availability-zones.md)
- [在 Azure Container Registry 中啟用區域冗余](../container-registry/zone-redundancy.md)

## <a name="next-steps"></a>後續步驟

- [在 Azure 中支援可用性區域的區域](az-region.md)
- [快速入門範本](https://aka.ms/azqs)