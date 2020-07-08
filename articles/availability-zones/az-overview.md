---
title: Azure 中的區域和可用性區域
description: 瞭解 Azure 中的區域和可用性區域，以符合您的技術和法規需求。
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 04/28/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 78f50abf68412d2edcb7a6504c8e5c1b788e5901
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85413156"
---
# <a name="regions-and-availability-zones-in-azure"></a>Azure 中的區域和可用性區域

Microsoft Azure 服務可全域使用，以最佳層級來驅動您的雲端作業。 您可以根據技術和法規考慮，選擇最適合您需求的區域：服務功能、資料存放區、合規性需求和延遲。

## <a name="terminology"></a>詞彙

為了進一步瞭解 Azure 中的區域和可用性區域，它有助於瞭解主要詞彙或概念。

| 詞彙或概念 | 描述 |
| --- | --- |
| region | 在延遲定義的周邊內部署的一組資料中心，並透過專用的區域低延遲網路連線。 |
| geography | 全球的一個區域，其中至少包含一個 Azure 區域。 地理位置會定義可保留資料存放區和合規性界限的離散市場。 不同的地理區讓具有特殊資料落地與合規性需求的客戶，可以將他們的資料與應用程式存放在彼此鄰近的區域。 地理位置具有容錯能力，可透過其與我們專用的高容量網路基礎結構的連線，承受完成區域失敗。 |
| 可用性區域 | 區域內的唯一實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 |
| 建議的區域 | 提供最廣泛服務功能的區域，其設計目的是要支援現在或未來的可用性區域。 **建議您**在 Azure 入口網站中指定這些設定。 |
| 替代（其他）區域 | 一個區域，可在建議區域也存在的資料常駐界限內，延伸 Azure 的使用量。 替代區域可協助將延遲優化，並為嚴重損壞修復需求提供第二個區域。 它們不是設計來支援可用性區域（雖然 Azure 會定期評估這些區域，以判斷它們是否應成為建議的區域）。 這些會在 Azure 入口網站中指定為**其他**。 |
| 基本服務 | 當區域正式推出時，可在所有區域中使用的核心 Azure 服務。 |
| 主流服務 | Azure 服務，可在區域/服務的12個月內，于替代區域的一般可用性或需求驅動的可用性中取得。 |
| 專用服務 | 一項 Azure 服務，其為由自訂/特製化硬體支援的區域之間的需求驅動可用性。 |
| 區域服務 | 已區域內部署的 Azure 服務，可讓客戶指定將部署服務的區域。 如需完整清單，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=all)。 |
| 非區域服務 | Azure 服務，其不會相依于特定的 Azure 區域。 非區域服務會部署到兩個或多個區域，如果發生區域性失敗，則另一個區域中的服務實例會繼續服務客戶。 如需完整清單，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=all)。 |

## <a name="regions"></a>區域

區域是在延遲定義的周邊內部署的一組資料中心，並透過專用的區域低延遲網路進行連線。 Azure 可讓您彈性地部署您需要的應用程式，包括跨多個區域，以提供跨區域復原。 如需詳細資訊，請參閱[復原要件的總覽](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)。

## <a name="availability-zones"></a>可用性區域

可用性區域是高可用性供應專案，可保護您的應用程式和資料不受資料中心失敗的影響。 「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原，所有已啟用的地區中至少要有三個不同的區域。 地區內「可用性區域」的實體區隔可保護應用程式和資料不受資料中心故障影響。 區域備援服務會將應用程式和資料複寫至所有「可用性區域」，以防出現單一失敗點。 使用「可用性區域」時，Azure 可提供業界最佳的 99.99% VM 執行時間 SLA。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 說明保證的 Azure 整體可用性。

Azure 區域中的可用性區域是由容錯網域和更新網域組成。 例如，如果您在 Azure 區域中建立橫跨三個區域的三個 (或更多) VM，您的 VM 會有效地分散到三個容錯網域和三個更新網域。 Azure 平臺會在更新網域中辨識此發佈，以確保不同區域中的 Vm 不會被排程同時更新。

藉由將運算、儲存體、網路及資料資源共置於某個區域內並複寫至其他區域，即可讓您的應用程式架構內建高可用性。 支援「可用性區域」的 Azure 服務分成兩個類別：

- 區域**服務**–資源會釘選到特定區域（例如虛擬機器、受控磁片、標準 IP 位址），或
- **區域冗余服務**– Azure 平臺會在區域間自動複寫時（例如，區域冗余儲存體、SQL Database）。

若要在 Azure 上達到全面性的商務持續性，請使用「可用性區域」與 Azure 地區配對的組合來建置您的應用程式架構。 您可以使用 Azure 地區內的「可用性區域」來同步複寫應用程式和資料以提供高可用性，並以非同步方式跨 Azure 地區複寫以提供災害復原保護。
 
![地區中細分至一個區域的概念性檢視](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> 可用性區域識別碼（上圖中的數位1、2和3）在邏輯上會分別對應至每個訂用帳戶的實際實體區域。 這表示指定訂用帳戶中的可用性區域1可能會參照不同訂用帳戶中可用性區域1以外的不同實體區域。 因此，建議您不要依賴不同訂用帳戶的可用性區域識別碼來放置虛擬機器。

## <a name="region-and-service-categories"></a>區域和服務類別

Azure 對於跨區域的 Azure 服務可用性的方法，最好是藉由表示在建議區域和替代區域中提供的服務來進行說明。

- **建議的區域**-提供最廣泛服務功能的區域，其設計是為了支援現在或未來的可用性區域。 **建議您**在 Azure 入口網站中指定這些設定。
- **替代（其他）區域**-一種區域，可在建議的區域也存在的資料存放區界限內，延伸 Azure 的使用量。 替代區域可協助將延遲優化，並為嚴重損壞修復需求提供第二個區域。 它們不是設計來支援可用性區域（雖然 Azure 會定期評估這些區域，以判斷它們是否應成為建議的區域）。 這些會在 Azure 入口網站中指定為**其他**。

### <a name="comparing-region-types"></a>比較區欄位型別

Azure 服務分成三種類別：基本、主流和特製化服務。 Azure 將服務部署到任何指定區域的一般原則主要是依區欄位型別、服務類別和客戶需求來驅動：

- **基本**–當區域正式推出，或在新的基本服務的12個月內正式推出時，所有建議和替代區域皆可使用。
- **主流**–適用于區域/服務公開上市12個月內的所有建議區域。替代區域中的需求驅動（許多已部署到較大的替代區域子集）。
- **專門**目標的服務供應專案，通常是以產業為主或由自訂/特殊化硬體支援。 跨區域的需求導向可用性（許多都已經部署到較大的建議區域子集）。

若要查看特定區域中所部署的服務，以及在區域中預覽或正式推出服務的未來藍圖，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=all)。

如果特定地區沒有提供服務供應專案，您可以聯絡 Microsoft 業務代表來分享您的興趣。

| 區域類型 | 非區域 | 基礎 | 主流 | 特製化 | 可用性區域 | 資料存留處 |
| --- | --- | --- | --- | --- | --- | --- |
| 建議 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 需求導向 | :heavy_check_mark: | :heavy_check_mark: |
| 替代 | :heavy_check_mark: | :heavy_check_mark: | 需求導向 | 需求導向 | N/A | :heavy_check_mark: |

### <a name="services-by-category"></a>依類別分類的服務

如先前所述，Azure 會將服務分類成三個類別：基本、主流和特製化。 服務類別會在正式推出時指派。 服務通常會以特製化服務的形式啟動其生命週期，而隨著需求和使用率的增加，可能會提升為主流或基礎。 下表列出服務的類別為 [基本]、[主流] 或 [特製化]。 您應該注意下列關於資料表的事項：

- 某些服務為非區域。 如需相關資訊和非區域服務的清單，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。
- 未列出較舊的世代虛擬機器。 如需詳細資訊，請參閱[前幾代虛擬機器大小](../virtual-machines/sizes-previous-gen.md)的檔。

> [!div class="mx-tableFixed"]
> | 基礎 | 主流 | 特製化 |
> | --- | --- | --- |
> | 帳戶儲存體 | API 管理 | 適用於 FHIR 的 Azure API |
> | 應用程式閘道 | 應用程式設定 | Azure 區塊鏈服務 |
> | Azure 備份 | App Service | Azure 藍圖 |
> | Azure Cosmos DB | 自動化 | 適用於 MariaDB 的 Azure 資料庫 |
> | Azure Data Lake Storage Gen2 | Azure Active Directory Domain Services | Azure 專用 HSM |
> | Azure ExpressRoute | Azure Analysis Services | Azure Dev Spaces |
> | Azure SQL Database | Azure Bastion | Azure Digital Twins |
> | 雲端服務 | Azure Cache for Redis | Azure 實驗室服務 |
> | 雲端服務： Av2 系列 | Azue 認知搜尋 | Azure NetApp Files |
> | 雲端服務： Dv2 系列 | Azure 資料總管 | Azure 量子 |
> | 雲端服務： Dv3 系列 | Azure Data Share | Azure Time Series Insights |
> | 雲端服務： Ev3 系列 | 適用於 MySQL 的 Azure 資料庫 | 由 CloudSimple 提供的 Azure VMware 解決方案 |
> | 雲端服務：實例層級 Ip | 適用於 PostgreSQL 的 Azure 資料庫 | 雲端服務： A8-A11 （需要大量計算） |
> | 雲端服務：保留的 IP | Azure 資料庫移轉服務 | 雲端服務： G 系列 |
> | 磁碟儲存體 | Azure Databricks | 雲端服務： H 系列 |
> | 事件中樞 | Azure DDoS 保護 | 認知服務：異常偵測器 |
> | Key Vault | Azure DevTest Labs | 認知服務：自訂視覺 |
> | 負載平衡器 | Azure 防火牆管理員 | 認知服務：說話者辨識 |
> | 服務匯流排 | Azure 防火牆 | Data Box Heavy |
> | Service Fabric | Azure Functions | 資料目錄 |
> | 虛擬機器擴展集 | Azure HPC Cache | Data Factory： Data Factory V1 |
> | 虛擬機器 | Azure IoT 中樞 | Data Lake Analytics |
> | 虛擬機器： Av2 系列 | Azure Kubernetes Service (AKS) | Machine Learning Studio |
> | 虛擬機器： Bs 系列 | Azure Machine Learning | Microsoft Genomics |
> | 虛擬機器： DSv2 系列 | Azure Private Link | 遠端轉譯 |
> | 虛擬機器： DSv3 系列 | Azure Red Hat OpenShift | Spatial Anchors |
> | 虛擬機器： Dv2 系列 | Azure Site Recovery | StorSimple |
> | 虛擬機器： Dv3 系列 | Azure 春季雲端服務 | 影片索引器 |
> | 虛擬機器： ESv3 系列 | Azure Stack Hub | 虛擬機器： A8-A11 （需要大量計算） |
> | 虛擬機器： Ev3 系列 | Azure 串流分析 | 虛擬機器： DASv4 系列 |
> | 虛擬機器： F 系列 | Azure Synapse Analytics | 虛擬機器： DAv4 系列 |
> | 虛擬機器： FS 系列 | Azure SignalR 服務 | 虛擬機器： DCsv2 系列 |
> | 虛擬機器：實例層級 Ip | Batch | 虛擬機器： EASv4 系列 |
> | 虛擬機器：保留的 IP | 雲端服務： M 系列 | 虛擬機器： EAv4 系列 |
> | 虛擬網路 | 認知服務 | 虛擬機器： G 系列 |
> | VPN 閘道 | 認知服務：電腦視覺 | 虛擬機器： GS 系列 |
> |  | 認知服務：內容仲裁 | 虛擬機器： HBv1 系列 |
> |  | 認知服務：臉部 | 虛擬機器： HBv2 系列 |
> |  | 認知服務： Language Understanding | 虛擬機器： HCv1 系列 |
> |  | 認知服務：語音服務 | 虛擬機器： H 系列 |
> |  | 認知服務：QnA Maker | 虛擬機器： LS 系列 |
> |  | 容器執行個體 | 虛擬機器： LSv2 系列 |
> |  | Container Registry | 虛擬機器： Mv2 系列 |
> |  | Data Factory | 虛擬機器： NC 系列 |
> |  | 事件方格 | 虛擬機器： NCv2 系列 |
> |  | HDInsight | 虛擬機器： NCv3 系列 |
> |  | Logic Apps | 虛擬機器： NDs 系列 |
> |  | 媒體服務 | 虛擬機器： NDv2 系列 |
> |  | 網路監看員 | 虛擬機器： NV 系列 |
> |  | 通知中樞 | 虛擬機器： NVv3 系列 |
> |  | Power BI Embedded | 虛擬機器： NVv4 系列 |
> |  | Premium Blob 儲存體 | 虛擬機器： Azure 上的 SAP HANA 大型執行個體 |
> |  | Premium 檔案儲存體 | Visual Studio App Center |
> |  | 儲存體：封存儲存體 |  |
> |  | Ultra 磁碟儲存體 |  |
> |  | 虛擬機器： Fsv2 系列 |  |
> |  | 虛擬機器： M 系列 |  |
> |  | Virtual WAN |  |

###  <a name="services-resiliency"></a>服務復原

所有 Azure 管理服務的架構都可以從區域層級失敗中復原。 在失敗的範圍中，區域內的一個或多個可用性區域失敗，與整個區域失敗相比，其失敗半徑較小。 Azure 可以從區域內或從另一個 Azure 區域中的管理服務區域層級失敗中復原。 Azure 會在區域內一次執行一個區域的重要維護，以避免影響在某個區域內可用性區域部署之客戶資源的任何失敗。

### <a name="pricing-for-vms-in-availability-zones"></a>可用性區域中的 Vm 定價

針對部署在「可用性區域」中的虛擬機器並不會產生額外費用。 有兩部 (或以上) VM 部署在 Azure 區域內兩個以上的「可用性區域」中時，即可提供 99.99% VM 執行時間 SLA。 在可用性區域之間將額外產生 VM 對 VM 的資料傳輸費用。 如需詳細資訊，請檢閱[頻寬定價](https://azure.microsoft.com/pricing/details/bandwidth/)頁面。

### <a name="get-started-with-availability-zones"></a>開始使用可用性區域

- [建立虛擬機器](../virtual-machines/windows/create-portal-availability-zone.md)
- [使用 PowerShell 來新增受控磁碟](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [建立區域備援虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [使用具有區域備援前端的標準 Load Balancer 來進行跨區域的 VM 負載平衡](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [使用具有區域前端的標準 Load Balancer 來進行區域內的 VM 負載平衡](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [區域備援儲存體](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../azure-sql/database/high-availability-sla.md#zone-redundant-configuration)
- [事件中樞異地災害復原](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [服務匯流排異地災害復原](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [建立區域備援虛擬網路閘道](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [為 Azure Cosmos DB 新增區域多餘區域](../cosmos-db/high-availability.md#availability-zone-support)
- [消費者入門 Azure Cache for Redis 可用性區域](https://aka.ms/redis/az/getstarted)
- [建立 Azure Active Directory Domain Services 執行個體](../active-directory-domain-services/tutorial-create-instance.md)
- [建立使用可用性區域的 Azure Kubernetes Service （AKS）叢集](../aks/availability-zones.md)

## <a name="next-steps"></a>後續步驟

- [在 Azure 中支援可用性區域的區域](az-region.md)
- [快速入門範本](https://aka.ms/azqs)
