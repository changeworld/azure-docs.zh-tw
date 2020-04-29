---
title: AKS 商務持續性和嚴重損壞修復的最佳做法
description: 瞭解叢集操作員的最佳作法，為您的應用程式達到最大執行時間，提供高可用性，並在 Azure Kubernetes Service （AKS）中準備損毀修復。
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 7aa93d8ba21cafddc5511e16fa430b76942b1a6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668298"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>因應 Azure Kubernetes Service (AKS) 中商務持續性和災害復原的最佳做法

當您在管理 Azure Kubernetes Service (AKS) 中的叢集時，應用程式的執行時間會變得很重要。 根據預設，AKS 會使用[虛擬機器擴展集（VMSS）](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)中的多個節點來提供高可用性。 但是，這些多個節點不會保護您的系統免于區域失敗。 為了充分發揮您的執行時間，請事先規劃以維持商務持續性，並準備嚴重損壞修復。

本文著重于如何規劃 AKS 中的商務持續性和嚴重損壞修復。 您會了解如何：

> [!div class="checklist"]
> * 規劃多個區域中的 AKS 叢集。
> * 使用 Azure 流量管理員，將流量路由傳送到多個叢集。
> * 針對您的容器映射登錄使用異地複寫。
> * 規劃跨多個叢集的應用程式狀態。
> * 跨多個區域複寫儲存體。

## <a name="plan-for-multiregion-deployment"></a>規劃多區域部署

**最佳做法**：當您部署多個 AKS 叢集時，請選擇可使用 AKS 的區域，並使用配對的區域。

AKS 叢集會部署到單一區域。 若要保護您的系統免于區域失敗，請將您的應用程式部署到跨不同區域的多個 AKS 叢集。 當您規劃部署 AKS 叢集的位置時，請考慮：

* [**AKS 區域可用性**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability)：選擇靠近您使用者的區域。 AKS 會持續擴展到新的區域。
* [**Azure 配對區域**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)：針對您的地理區域，請選擇兩個配對的區域。 配對的區域會協調平臺更新，並在需要時優先復原復原工作。
* **服務可用性**：決定配對的區域是否應為經常性存取、經常性存取/暖性或熱/冷。 是否要同時執行這兩個區域，其中一個區域*準備好*要開始服務流量？ 或者，您想要讓一個區域有時間準備好提供流量嗎？

AKS 區域可用性和配對區域是共同考慮。 請將 AKS 叢集部署到設計用來一起管理區域災害復原的配對區域。 例如，AKS 有在「美國東部」和「美國西部」提供。 這些區域是配對的。 當您要建立 AKS BC/DR 策略時，請選擇這兩個區域。

當您部署應用程式時，請將另一個步驟新增至您的 CI/CD 管線，以部署到這些多個 AKS 叢集。 如果您未更新部署管線，應用程式可能只會部署到您的其中一個區域和 AKS 叢集。 導向至次要地區的客戶流量不會收到最新的程式碼更新。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理員來路由傳送流量

**最佳做法**： Azure 流量管理員可以將客戶導向至最接近的 AKS 叢集和應用程式實例。 為了獲得最佳效能和冗余，請將所有應用程式流量導向至 AKS 叢集之前，先透過流量管理員。

如果您在不同的區域中有多個 AKS 叢集，請使用流量管理員來控制流量如何流向在每個叢集中執行的應用程式。 [Azure 流量管理員](https://docs.microsoft.com/azure/traffic-manager/)是 DNS 型的流量負載平衡器，可以將網路流量分散到各個區域。 使用 [流量管理員] 根據叢集回應時間或根據地理位置來路由傳送使用者。

![具有流量管理員的 AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

具有單一 AKS 叢集的客戶通常會連接到指定應用程式的服務 IP 或 DNS 名稱。 在 multicluster 部署中，客戶應連線到指向每個 AKS 叢集上之服務的流量管理員 DNS 名稱。 使用流量管理員端點來定義這些服務。 每個端點都是*服務負載平衡器 IP*。 使用此設定可將某個區域中流量管理員端點的網路流量導向不同區域中的端點。

![透過流量管理員的地理路由](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

流量管理員會執行 DNS 查閱，並傳回使用者最適當的端點。 嵌套的設定檔可以設定主要位置的優先順序。 例如，使用者通常會連接到最接近的地理區域。 如果該區域發生問題，流量管理員會改為將使用者導向次要區域。 這種方法可確保客戶可以連接到應用程式實例，即使其最近的地理區域無法使用也是如此。

如需有關如何設定端點和路由的詳細資訊，請參閱[使用流量管理員設定地理流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)。

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>第7層應用程式路由與 Azure Front 服務

流量管理員使用 DNS （第3層）來塑造流量。 [Azure Front 門板服務](https://docs.microsoft.com/azure/frontdoor/front-door-overview)提供 HTTP/HTTPS （第7層）路由選項。 Azure 前門服務的其他功能包括 TLS 終止、自訂網域、web 應用程式防火牆、URL 重寫和會話親和性。 請檢閱應用程式流量的需求，以了解哪一種解決方案最合適。

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>具有全域虛擬網路對等互連的相互連接區域

如果叢集需要彼此溝通，可以透過[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)來連接兩個虛擬網路。 這項技術可將虛擬網路彼此互連，跨 Microsoft 骨幹網路提供高頻寬，甚至是在不同的地理區域。

將 AKS 叢集執行所在的虛擬網路對等互連的必要條件是，在您的 AKS 叢集中使用標準 Load Balancer，讓虛擬網路對等互連能夠連線到 Kubernetes 服務。

## <a name="enable-geo-replication-for-container-images"></a>為容器映像啟用異地複寫

**最佳做法**：將您的容器映射儲存在 Azure Container Registry，並將登錄異地複寫至每個 AKS 區域。

若要在 AKS 中部署及執行應用程式，您必須有辦法儲存和提取容器映像。 Container Registry 與 AKS 整合，因此可以安全地儲存您的容器映射或 Helm 圖表。 Container Registry 支援多宿主異地複寫，以自動將您的影像複寫至世界各地的 Azure 區域。 

若要改善效能和可用性，請使用 Container Registry 異地複寫，在您擁有 AKS 叢集的每個區域中建立登錄。 然後，每個 AKS 叢集會從相同區域中的本機容器登錄提取容器映射：

![容器映射的容器登錄異地複寫](media/operator-best-practices-bc-dr/acr-geo-replication.png)

當您使用 Container Registry 異地複寫從相同區域提取映射時，其結果如下：

* **更快**：從相同 Azure 區域內的高速、低延遲網路連線提取映射。
* **更可靠**：如果某個區域無法使用，您的 AKS 叢集就會從可用的容器登錄中提取映射。
* **成本較低**：資料中心之間沒有網路輸出費用。

異地複寫是*Premium* SKU 容器登錄的功能。 如需有關如何設定異地複寫的詳細資訊，請參閱[Container Registry 異地](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)複寫。

## <a name="remove-service-state-from-inside-containers"></a>從容器內移除服務狀態

**最佳做法**：可能的話，請勿將服務狀態儲存在容器內。 相反地，請使用支援多區域複寫的 Azure 平臺即服務（PaaS）。

*服務狀態*是指服務正常運作所需的記憶體中或磁片上的資料。 狀態包括服務會讀取及寫入的資料結構和成員變數。 根據服務的架構方式而定，狀態也可能包含檔案或儲存在磁片上的其他資源。 例如，狀態可能包括資料庫用來儲存資料和交易記錄檔的檔案。

狀態可以是外部化或與操作該狀態的程式碼共存。 通常，您會使用在網路上的不同電腦上執行的資料庫或其他資料存放區來外部化狀態，或在同一部電腦上執行不在進程中的其他資料存放區。

容器和微服務在其中執行的進程不會保留狀態時，是最具彈性的。 因為應用程式幾乎一律包含某個狀態，所以請使用 PaaS 解決方案，例如適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫或 Azure SQL Database。

若要建立可移植的應用程式，請參閱下列指導方針：

* [12因素應用程式方法](https://12factor.net/)
* [在多個 Azure 區域中執行 Web 應用程式](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>建立儲存體移轉計劃

**最佳做法**：如果您使用 Azure 儲存體，請準備並測試如何將儲存體從主要區域遷移至備份區域。

您的應用程式可能會使用 Azure 儲存體的資料。 因為您的應用程式會分散到不同區域中的多個 AKS 叢集，所以您必須將儲存體保持同步。 以下是複寫儲存體的兩個常見方式：

* 以基礎結構為基礎的非同步複寫
* 以應用程式為基礎的非同步複寫

### <a name="infrastructure-based-asynchronous-replication"></a>以基礎結構為基礎的非同步複寫

即使在 pod 刪除之後，您的應用程式也可能需要持續性儲存體。 在 Kubernetes 中，您可以使用持續性磁片區來保存資料儲存。 持續性磁片區會掛接至節點 VM，然後公開至 pod。 即使 pod 已移至相同叢集內的不同節點，持續性磁片區仍會遵循 pod。

您所使用的複寫策略取決於您的儲存體解決方案。 [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/)、 [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)、[城堡](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)和[Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps)等常見的儲存體解決方案提供自己的嚴重損壞修復和複寫指引。

一般的策略是提供一般的儲存點，讓應用程式可以在其中寫入其資料。 然後，此資料會複寫到各個區域，再於本機存取。

![以基礎結構為基礎的非同步複寫](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

如果您使用 Azure 受控磁碟，您可以選擇複寫和 DR 解決方案，如下所示：

* [Azure 上的 Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>以應用程式為基礎的非同步複寫

Kubernetes 目前不提供以應用程式為基礎之非同步複寫的原生執行。 由於容器和 Kubernetes 是鬆散結合的，因此任何傳統的應用程式或語言方法都應該可行。 一般而言，應用程式本身會複寫儲存體要求，然後再寫入每個叢集的基礎資料儲存體。

![以應用程式為基礎的非同步複寫](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>後續步驟

本文著重于 AKS 叢集的商務持續性和嚴重損壞修復考慮。 如需 AKS 中叢集作業的詳細資訊，請參閱下列有關最佳做法的文章：

* [多組織使用者和叢集隔離][aks-best-practices-cluster-isolation]
* [基本的 Kubernetes 排程器功能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
