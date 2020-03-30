---
title: 適用於容器的 Azure 監視器概觀 | Microsoft Docs
description: 本文說明適用於容器的 Azure 監視器，其會監視 AKS 容器深入解析解決方案，以及它藉由監視您 AKS 叢集和 Azure 中容器執行個體的健康情況來提供的值。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275226"
---
# <a name="azure-monitor-for-containers-overview"></a>適用於容器的 Azure 監視器概觀

容器的 Azure 監視器是一項功能，旨在監視部署到：

- 託管庫伯奈斯群集託管在[Azure 庫伯奈斯服務 （AKS）](../../aks/intro-kubernetes.md)上
- 使用[AKS 引擎](https://github.com/Azure/aks-engine)託管在 Azure 上的自管理的庫伯奈斯群集
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- 託管在[Azure 堆疊](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910)或本地的自管理的庫伯奈斯群集
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

容器的 Azure 監視器支援運行 Linux 和 Windows Server 2019 作業系統的群集。 

監視容器很重要，尤其在您使用多個應用程式大規模執行生產環境叢集時。

適用於容器的 Azure 監視器可藉由透過計量 API 從 Kubernetes 中取得的控制器、節點與容器來收集記憶體與處理器計量，為您提供效能可見度。 容器記錄也會一併收集。  啟用從 Kubernetes 群集進行監視後，指標和日誌將自動通過 Linux 日誌分析代理的容器化版本為您收集。 指標寫入指標存儲，日誌資料寫入與[日誌分析](../log-query/log-query-overview.md)工作區關聯的日誌存儲。 

![容器體系結構的 Azure 監視器](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>適用於容器的 Azure 監視器提供哪些功能？

容器的 Azure 監視器使用 Azure 監視器的不同功能提供全面的監視體驗。 這些功能使您能夠瞭解運行 Linux 和 Windows Server 2019 作業系統的 Kubernetes 群集的性能和運行狀況以及容器工作負載。 使用容器的 Azure 監視器，您可以：

* 識別正在節點上執行的 AKS 容器，以及其平均的處理器與記憶體使用率。 此知識可協助您識別資源瓶頸。
* 識別容器群組及其裝載於 Azure Container Instances 之容器的處理器和記憶體使用率。  
* 識別容器在控制器或 Pod 中的所在位置。 此知識可協助您檢視控制器或 Pod 的整體效能。 
* 檢閱在和支援 Pod 的標準程序無關之主機上執行的工作負載的資源使用率。
* 了解叢集在平均負載和最高負載之下的行為。 此知識可協助您識別所需的容量，並判斷叢集可承受的負載上限。 
* 配置警報，以便在節點或容器上的 CPU 和記憶體利用率超過閾值時，或在基礎結構或節點運行狀況匯總的群集中發生運行狀況狀態更改時主動通知您或記錄警報。
* 與[Prometheus](https://prometheus.io/docs/introduction/overview/)集成，查看它從節點和 Kubernetes 收集的應用程式和工作負載指標，使用[查詢](container-insights-log-search.md)創建自訂警報、儀表板，並詳細執行詳細分析。
* 監視[部署到本地 AKS 引擎](https://github.com/Azure/aks-engine)和[Azure 堆疊上的 AKS 引擎的](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)容器工作負載。
* 監視[部署到 Azure 紅帽 OpenShift 的](../../openshift/intro-openshift.md)容器工作負載。

    >[!NOTE]
    >支援 Azure 紅帽 OpenShift 是此時公共預覽中的一項功能。
    >

與 Linux 群集相比，監視 Windows Server 群集的主要區別如下：

- 記憶體 RSS 指標不適用於 Windows 節點和容器。
- 磁片存儲容量資訊不適用於 Windows 節點。
- 容器日誌不適用於在 Windows 節點中運行的容器。
- 除 Windows 容器日誌外，還提供即時資料（預覽）功能支援。
- 僅監視窗格環境，而不是 Docker 環境。
- 使用預覽版本，最多支援 30 個 Windows Server 容器。 此限制不適用於 Linux 容器。 

請查看以下視頻，提供中間級別的深度潛水，以説明您瞭解如何使用 Azure 監視器監視容器的 AKS 群集。

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>如何存取此功能？

您有兩種方式可用來存取適用於容器的 Azure 監視器：從 Azure 監視器或直接從所選取的 AKS 叢集。 從 Azure 監視器中，您可以全域查看部署的所有容器（這些容器受監視且未進行），從而允許您跨訂閱和資源組搜索和篩選，然後深入瞭解 Azure 監視器中的容器。選定的容器。  否則，可以直接從 AKS 頁面從選定的 AKS 容器訪問該功能。  

![存取適用於容器的 Azure 監視器方法概觀](./media/container-insights-overview/azmon-containers-experience.png)

如果您有興趣監視和管理在 AKS 之外運行的 Docker 和 Windows 容器主機以查看配置、審核和資源利用率，請參閱[容器監視解決方案](../../azure-monitor/insights/containers.md)。

## <a name="next-steps"></a>後續步驟

要開始監視 Kubernetes 群集，請查看[如何啟用容器的 Azure 監視器](container-insights-onboard.md)以瞭解啟用監視的要求和可用方法。 
