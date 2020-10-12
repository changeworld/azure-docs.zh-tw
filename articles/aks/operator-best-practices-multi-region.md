---
title: AKS 商務持續性和嚴重損壞修復的最佳作法
description: 瞭解叢集操作員的最佳作法，以達到應用程式的最大執行時間，在 Azure Kubernetes Service (AKS) 中提供高可用性和準備進行嚴重損壞修復。
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 110a25fca0b0e764650665635dbe545de7a350cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653991"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>因應 Azure Kubernetes Service (AKS) 中商務持續性和災害復原的最佳做法

當您在管理 Azure Kubernetes Service (AKS) 中的叢集時，應用程式的執行時間會變得很重要。 根據預設，AKS 會使用 [虛擬機器擴展集中 ](../virtual-machine-scale-sets/overview.md)的多個節點， (VMSS) 來提供高可用性。 但是，這些多重節點不會保護您的系統免于區域失敗的影響。 為了充分發揮您的執行時間，請事先規劃以維護商務持續性，並為嚴重損壞修復做好準備。

本文著重于如何規劃 AKS 中的商務持續性和嚴重損壞修復。 您會了解如何：

> [!div class="checklist"]
> * 規劃多個區域中的 AKS 叢集。
> * 使用 Azure 流量管理員將流量路由傳送到多個叢集。
> * 針對您的容器映射登錄使用異地複寫。
> * 規劃跨多個叢集的應用程式狀態。
> * 跨多個區域複寫儲存體。

## <a name="plan-for-multiregion-deployment"></a>規劃多區域部署

**最佳做法**：當您部署多個 AKS 叢集時，請選擇可使用 AKS 的區域，並使用配對的區域。

AKS 叢集會部署到單一區域。 若要保護您的系統免于區域失敗，請將您的應用程式部署到跨不同區域的多個 AKS 叢集中。 當您規劃要部署 AKS 叢集的位置時，請考慮下列事項：

* [**AKS 區域可用性**](./quotas-skus-regions.md#region-availability)：選擇接近您使用者的區域。 AKS 會持續擴展到新的區域。
* [**Azure 配對的區域**](../best-practices-availability-paired-regions.md)：針對您的地理區域，請選擇兩個彼此配對的區域。 配對區域會協調平臺更新，並在需要時設定復原工作的優先順序。
* **服務可用性**：決定您配對的區域是否應該是經常性存取、經常性存取/暖或經常性/冷。 是否要同時執行這兩個區域，並 *準備好* 一個區域來開始服務流量？ 或者，您想要讓某個區域有時間準備好提供流量嗎？

AKS 區域可用性和配對區域是聯合考慮。 請將 AKS 叢集部署到設計用來一起管理區域災害復原的配對區域。 例如，AKS 有在「美國東部」和「美國西部」提供。 這些區域是成對的。 當您要建立 AKS BC/DR 策略時，請選擇這兩個區域。

當您部署應用程式時，請將另一個步驟新增至 CI/CD 管線，以部署至這些多個 AKS 叢集。 如果您未更新部署管線，應用程式可能只會部署到您的其中一個區域和 AKS 叢集。 導向至次要區域的客戶流量不會收到最新的程式碼更新。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理員來路由傳送流量

**最佳做法**： Azure 流量管理員可以將客戶導向至最接近的 AKS 叢集和應用程式實例。 為了獲得最佳效能和冗余，請先透過流量管理員將所有應用程式流量導向您的 AKS 叢集，然後再移至您的叢集。

如果您在不同區域中有多個 AKS 叢集，請使用流量管理員來控制流量流向每個叢集中執行之應用程式的方式。 [Azure 流量管理員](../traffic-manager/index.yml)是 DNS 型的流量負載平衡器，可以將網路流量分散到各個區域。 使用流量管理員根據叢集回應時間或根據地理位置來路由傳送使用者。

![使用流量管理員 AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

具有單一 AKS 叢集的客戶通常會連線至指定應用程式的服務 IP 或 DNS 名稱。 在 multicluster 部署中，客戶應連線到流量管理員 DNS 名稱，以指向每個 AKS 叢集上的服務。 使用流量管理員端點來定義這些服務。 每個端點都是 *服務負載平衡器 IP*。 使用此設定將網路流量從某個區域中的流量管理員端點導向至不同區域中的端點。

![透過流量管理員的地理路由](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

流量管理員會執行 DNS 查閱，並傳回使用者最適當的端點。 嵌套設定檔可以設定主要位置的優先順序。 例如，使用者通常應該連接到最接近的地理區域。 如果該區域發生問題，流量管理員會改為將使用者導向至次要區域。 這種方法可確保客戶即使其最接近的地理區域無法使用，也可以連接至應用程式實例。

如需有關如何設定端點和路由的詳細資訊，請參閱 [使用流量管理員設定地理流量路由方法](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)。

### <a name="application-routing-with-azure-front-door-service"></a>使用 Azure Front Door 服務的應用程式路由

[Azure Front Door 服務](../frontdoor/front-door-overview.md)使用分割的 TCP 型任意傳播通訊協定，可確保您的終端使用者能立即連接到最接近的 Front Door POP (目前狀態) 。 Azure Front Door 服務的其他功能包括 TLS 終止、自訂網域、web 應用程式防火牆、URL 重寫和會話親和性。 請檢閱應用程式流量的需求，以了解哪一種解決方案最合適。

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>具有全域虛擬網路對等互連的互連區域

如果叢集需要彼此通訊，可以透過 [虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)來連線兩個虛擬網路。 這項技術會將虛擬網路彼此互連，以提供跨 Microsoft 骨幹網路的高頻寬，甚至在不同的地理區域。

將 AKS 叢集執行所在的虛擬網路對等互連的必要條件是在您的 AKS 叢集中使用標準 Load Balancer，以便透過虛擬網路對等互連來連線到 Kubernetes 服務。

## <a name="enable-geo-replication-for-container-images"></a>為容器映像啟用異地複寫

**最佳做法**：將您的容器映射儲存在 Azure Container Registry 中，並將登錄異地複寫到每個 AKS 區域。

若要在 AKS 中部署及執行應用程式，您必須有辦法儲存和提取容器映像。 Container Registry 與 AKS 整合，因此可以安全地儲存您的容器映射或 Helm 圖表。 Container Registry 支援多宿主異地複寫，以自動將您的映射複寫至世界各地的 Azure 區域。 

若要改善效能和可用性，請使用 Container Registry 異地複寫，在您有 AKS 叢集的每個區域中建立登錄。 接著，每個 AKS 叢集都會從相同區域中的本機容器登錄提取容器映射：

![容器映射的 container Registry 異地複寫](media/operator-best-practices-bc-dr/acr-geo-replication.png)

當您使用 Container Registry 異地複寫從相同區域提取映射時，結果會是：

* **更快速**：從相同 Azure 區域內的高速、低延遲網路連線提取映射。
* **更可靠**：如果區域無法使用，您的 AKS 叢集會從可用的容器登錄中提取映射。
* **成本**低廉：資料中心之間沒有網路輸出費用。

異地複寫是 *Premium* SKU 容器登錄的功能。 如需有關如何設定異地複寫的詳細資訊，請參閱 [Container Registry 異地](../container-registry/container-registry-geo-replication.md)複寫。

## <a name="remove-service-state-from-inside-containers"></a>從容器內移除服務狀態

**最佳做法**：可能的話，請勿將服務狀態儲存在容器內。 相反地，請使用支援多區域複寫的 Azure 平臺即服務 (PaaS) 。

*服務狀態* 指的是服務運作所需的記憶體中或磁片上的資料。 狀態包括服務會讀取及寫入的資料結構和成員變數。 根據服務的架構方式而定，狀態也可能包含儲存在磁片上的檔案或其他資源。 例如，狀態可能包含資料庫用來儲存資料和交易記錄檔的檔案。

狀態可以是外部化或與操作狀態的程式碼共存。 一般來說，您會使用在網路上不同電腦上執行的資料庫或其他資料存放區來具體化狀態，或是在同一部電腦上的進程不足。

容器和微服務在其內部執行的進程不會保留狀態時，最具彈性。 因為應用程式幾乎一律會包含某些狀態，所以請使用 PaaS 解決方案，例如 Azure Cosmos DB、適用於 PostgreSQL 的 Azure 資料庫、適用於 MySQL 的 Azure 資料庫或 Azure SQL Database。

若要建立可移植的應用程式，請參閱下列指導方針：

* [12要素應用程式方法](https://12factor.net/)
* [在多個 Azure 區域中執行 Web 應用程式](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>建立儲存體移轉計劃

**最佳做法**：如果您使用 Azure 儲存體，請準備並測試如何將儲存體從主要區域遷移至備份區域。

您的應用程式可能會將 Azure 儲存體用於其資料。 因為您的應用程式會分散到不同區域中的多個 AKS 叢集，所以您需要將儲存體保持同步。 以下是複寫儲存體的兩個常見方式：

* 以基礎結構為基礎的非同步複寫
* 以應用程式為基礎的非同步複寫

### <a name="infrastructure-based-asynchronous-replication"></a>以基礎結構為基礎的非同步複寫

即使在 pod 刪除之後，您的應用程式也可能需要持續性儲存體。 在 Kubernetes 中，您可以使用持續性磁片區來保存資料存放區。 持續性磁片區會掛接到節點 VM，然後公開至 pod。 即使 pod 移至相同叢集中的不同節點，持續性磁片區仍會遵循 pod。

您使用的複寫策略取決於您的儲存體解決方案。 一般儲存體解決方案（例如 [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/)、 [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)、 [城堡](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)和 [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) ）提供有關嚴重損壞修復和複寫的指引。

典型的策略是提供可讓應用程式寫入其資料的一般儲存點。 然後，此資料會複寫到各個區域，再於本機存取。

![以基礎結構為基礎的非同步複寫](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

如果您使用 Azure 受控磁碟，則可以選擇複寫和 DR 解決方案，如下所示：

* [Azure 上的 Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure 備份](../backup/backup-overview.md)

### <a name="application-based-asynchronous-replication"></a>以應用程式為基礎的非同步複寫

Kubernetes 目前沒有針對以應用程式為基礎的非同步複寫提供原生的實作為。 因為容器和 Kubernetes 是鬆散結合的，所以任何傳統的應用程式或語言方法都應可運作。 一般而言，應用程式本身會複寫儲存體要求，然後將這些要求寫入每個叢集的基礎資料儲存體。

![以應用程式為基礎的非同步複寫](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>接下來的步驟

本文著重于 AKS 叢集的商務持續性和嚴重損壞修復考慮。 如需有關 AKS 中叢集操作的詳細資訊，請參閱下列關於最佳做法的文章：

* [多租使用者和叢集隔離][aks-best-practices-cluster-isolation]
* [基本的 Kubernetes 排程器功能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
