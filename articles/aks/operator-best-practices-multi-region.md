---
title: Azure 庫伯奈斯服務 (AKS) 的高可用性和災難恢復
description: 瞭解群集操作員的最佳做法,以實現應用程式的最大發佈時間,在 Azure Kubernetes 服務 (AKS) 中提供高可用性併為災難恢復做好準備。
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 6aff60cbc4a4cab557e6e202ea1181d5b20bbd20
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655875"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>因應 Azure Kubernetes Service (AKS) 中商務持續性和災害復原的最佳做法

當您在管理 Azure Kubernetes Service (AKS) 中的叢集時，應用程式的執行時間會變得很重要。 默認情況下,AKS 在[虛擬機器規模集(VMSS)](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)中使用多個節點來提供高可用性。 但是,這些多個節點無法保護您的系統免受區域故障的影響。 為了最大限度地延長您的停機時間,請提前計劃以保持業務連續性,併為災難恢復做好準備。

本文重點介紹如何在 AKS 中規劃業務連續性和災難恢復。 您會了解如何：

> [!div class="checklist"]
> * 規劃多個區域中的 AKS 群集。
> * 使用 Azure 流量管理器跨多個群集路由流量。
> * 對容器映射註冊表使用異地複製。
> * 規劃跨多個群集的應用程序狀態。
> * 跨多個區域複製存儲。

## <a name="plan-for-multiregion-deployment"></a>規劃多區域部署

**最佳實務**:部署多個 AKS 群集時,選擇 AKS 可用的區域,並使用配對區域。

AKS 叢集會部署到單一區域。 為了保護系統免受區域故障的影響,請將應用程式部署到跨不同區域的多個 AKS 群集中。 規劃部署 AKS 叢集的位置時,請考慮:

* [**AKS 區域可用性**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability):選擇靠近用戶的區域。 AKS 不斷擴展到新區域。
* [**Azure 配對區域**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions):對於地理區域,選擇彼此配對的兩個區域。 配對區域協調平臺更新,並根據需要確定恢復工作的優先順序。
* **服務可用性**:決定您的配對區域是熱/熱、熱/熱還是熱/冷。 是否要同時運行這兩個區域,同時*準備*運行一個區域,以開始為流量提供服務? 或者,您希望一個地區有時間為交通服務做好準備?

AKS 區域可用性和配對區域是共同考慮的問題。 請將 AKS 叢集部署到設計用來一起管理區域災害復原的配對區域。 例如，AKS 有在「美國東部」和「美國西部」提供。 這些區域是配對的。 創建 AKS BC/DR 策略時,請選擇這兩個區域。

部署應用程式時,請向 CI/CD 管道添加另一個步驟以部署到這些多個 AKS 群集。 如果不更新部署管道,應用程式可能只部署到其中一個區域和 AKS 群集中。 定向到輔助區域的客戶流量將接收最新的代碼更新。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理員來路由傳送流量

**最佳實務**:Azure 流量管理員可以將客戶定向到其最近的 AKS 群集和應用程式實例。 為了獲得最佳性能和冗餘,請在所有應用程式流量通過流量管理器在它進入 AKS 群集之前進行引導。

如果不同地區有多個 AKS 群集,請使用流量管理器控制流量如何流向每個群集中運行的應用程式。 [Azure 流量管理員](https://docs.microsoft.com/azure/traffic-manager/)是 DNS 型的流量負載平衡器，可以將網路流量分散到各個區域。 使用流量管理器根據群集回應時間或地理位置對用戶進行路由。

![帶流量管理員的 AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

具有單個 AKS 群集的客戶通常連接到給定應用程式的服務 IP 或 DNS 名稱。 在多群集部署中,客戶應連接到指向每個 AKS 群集上的服務的流量管理器 DNS 名稱。 使用流量管理器終結點定義這些服務。 每個終結點都是*服務負載均衡器 IP。* 使用此配置可以將網路流量從一個區域中的流量管理器終結點定向到其他區域中的終結點。

![使用流量管理員的地理路由](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

流量管理器執行 DNS 查找並返回使用者最合適的終結點。 嵌套配置檔可以確定主位置的優先順序。 例如,使用者通常應連接到其最近的地理區域。 如果該區域有問題,流量管理器將使用者定向到輔助區域。 此方法可確保客戶可以連接到應用程式實例,即使其最近的地理區域不可用。

有關如何設定終結點和路由的資訊,請參閱[使用流量管理器設定地理流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)。

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>第 7 層應用程式路由與 Azure 前門服務

流量管理器使用 DNS(第 3 層)來塑造流量。 [Azure 前門服務](https://docs.microsoft.com/azure/frontdoor/front-door-overview)提供 HTTP/HTTPS(第 7 層)路由選項。 Azure 前門服務的其他功能包括 TLS 終止、自定義域、Web 應用程式防火牆、URL 重寫和會話關聯性。 請檢閱應用程式流量的需求，以了解哪一種解決方案最合適。

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>以全域虛擬網路對等互連區域

如果群集需要相互對話,則可以通過[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)實現將虛擬網路相互連接。 該技術將虛擬網路相互連接,為Microsoft的主幹網路提供高頻寬,甚至跨不同的地理區域。

對運行 AKS 群集的虛擬網路進行對等的先決條件是在 AKS 群集中使用標準負載均衡器,以便 Kubernetes 服務能夠跨虛擬網路對等互連訪問。

## <a name="enable-geo-replication-for-container-images"></a>為容器映像啟用異地複寫

**最佳做法**:將容器映像存儲在 Azure 容器註冊表中,並將註冊表異地複製到每個 AKS 區域。

若要在 AKS 中部署及執行應用程式，您必須有辦法儲存和提取容器映像。 容器註冊錶與 AKS 整合,因此可以安全地儲存容器圖像或 Helm 圖表。 容器註冊表支援多主地理複製,以自動將映射複製到世界各地的 Azure 區域。 

為提高性能和可用性,請使用容器註冊表異地複製在具有 AKS 群集的每個區域創建註冊表。 然後,每個 AKS 群集從同一區域中的本地容器註冊表中拉出容器映射:

![容器映像的容器註冊表異地複製](media/operator-best-practices-bc-dr/acr-geo-replication.png)

當您使用容器註冊表異地複製從同一區域提取影像時,結果是:

* **更快**:從同一 Azure 區域內的高速、低延遲網路連接中提取圖像。
* **更可靠**:如果區域不可用,AKS 群集將從可用的容器註冊表中提取映射。
* **更便宜**:數據中心之間沒有網路出口費用。

異地複製是*高級*SKU 容器註冊表的一項功能。 有關如何設定異地複製的資訊,請參閱[容器註冊表異地複製](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。

## <a name="remove-service-state-from-inside-containers"></a>從容器內移除服務狀態

**最佳實踐**:在可能的情況下,不要在容器內存儲服務狀態。 相反,請使用 Azure 平台作為支援多區域複製的服務 (PaaS)。

*服務狀態*是指服務正常運行所需的記憶體中或磁碟上數據。 狀態包括服務會讀取及寫入的資料結構和成員變數。 根據服務的架構方式,狀態可能還包括存儲在磁碟上的檔或其他資源。 例如,狀態可能包括資料庫用於存儲數據和事務日誌的檔。

狀態可以外部化,也可以與操作狀態的代碼共存。 通常,通過使用通過網路在不同的計算機上運行或在同一台計算機上的進程耗盡的資料庫或其他數據存儲,將狀態外部化。

當容器和微服務內部運行的進程不保留狀態時,它們具有最大的彈性。 由於應用程式幾乎總是包含某些狀態,因此請使用 PaaS 解決方案,例如 MySQL 的 Azure 資料庫、PostgreSQL 的 Azure 資料庫或 Azure SQL 資料庫。

要構建便攜式應用程式,請參閱以下指南:

* [12 因素應用方法](https://12factor.net/)
* [在多個 Azure 區域中執行 Web 應用程式](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>建立儲存體移轉計劃

**最佳實務**:如果使用 Azure 儲存,請準備並測試如何將存儲從主區域遷移到備份區域。

應用程式可能對其數據使用 Azure 儲存。 由於應用程式分佈在不同區域中的多個 AKS 群集中,因此需要保持存儲同步。 以下是複製儲存的兩種常見方法:

* 以基礎結構為基礎的非同步複寫
* 以應用程式為基礎的非同步複寫

### <a name="infrastructure-based-asynchronous-replication"></a>以基礎結構為基礎的非同步複寫

即使刪除 pod,您的應用程式也可能需要持久儲存。 在 Kubernetes 中,您可以使用持久卷來持久儲存數據。 持久性卷將裝載到節點 VM,然後公開到 pod。 即使 pod 移至同一群集內的不同節點,持久性捲也會跟隨 pod。

您使用的複製策略取決於儲存解決方案。 常見的儲存解決方案,如[Gluster、Ceph、Rook](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)和[Portworx,](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps)提供有關災難恢復和[Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/)[Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)複製的指南。

典型的策略是提供一個常見的存儲點,應用程式可以在其中寫入其數據。 然後，此資料會複寫到各個區域，再於本機存取。

![以基礎結構為基礎的非同步複寫](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

如果使用 Azure 託管磁碟,則可以選擇複製和 DR 解決方案,例如:

* [Azure 的 Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>以應用程式為基礎的非同步複寫

Kubernetes 目前不為基於應用程式的非同步複製提供本機實現。 由於容器和 Kubernetes 是鬆散耦合的,因此任何傳統的應用程式或語言方法都應該起作用。 通常,應用程式本身複製存儲請求,然後這些請求寫入每個群集的基礎數據存儲。

![以應用程式為基礎的非同步複寫](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>後續步驟

本文重點介紹 AKS 群集的業務連續性和災難恢復注意事項。 有關 AKS 中的群集操作的詳細資訊,請參閱以下有關最佳實踐的文章:

* [多租戶和群集隔離][aks-best-practices-cluster-isolation]
* [基本的 Kubernetes 排程器功能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
