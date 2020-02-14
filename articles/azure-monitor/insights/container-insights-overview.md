---
title: 適用於容器的 Azure 監視器概觀 | Microsoft Docs
description: 本文說明適用於容器的 Azure 監視器，其會監視 AKS 容器深入解析解決方案，以及它藉由監視您 AKS 叢集和 Azure 中容器執行個體的健康情況來提供的值。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198032"
---
# <a name="azure-monitor-for-containers-overview"></a>適用於容器的 Azure 監視器概觀

容器的 Azure 監視器是一項功能，其設計是用來監視部署到的容器工作負載效能：

- 裝載于[Azure Kubernetes Service （AKS）](../../aks/intro-kubernetes.md)上的受控 Kubernetes 叢集
- 使用[AKS 引擎](https://github.com/Azure/aks-engine)在 Azure 上託管的自我管理 Kubernetes 叢集
- [Azure 容器執行個體](../../container-instances/container-instances-overview.md)
- 裝載于[Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910)或內部部署環境的自我管理 Kubernetes 叢集
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

適用于容器的 Azure 監視器支援執行 Linux 和 Windows Server 2019 作業系統的叢集。 

監視容器很重要，尤其在您使用多個應用程式大規模執行生產環境叢集時。

適用於容器的 Azure 監視器可藉由透過計量 API 從 Kubernetes 中取得的控制器、節點與容器來收集記憶體與處理器計量，為您提供效能可見度。 容器記錄也會一併收集。  當您從 Kubernetes 叢集啟用監視之後，系統會透過適用于 Linux 的 Log Analytics 代理程式容器化版本自動收集計量和記錄。 計量會寫入計量存放區，並將記錄資料寫入與您的[Log Analytics](../log-query/log-query-overview.md)工作區相關聯的記錄存放區。 

![容器的 Azure 監視器架構](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>適用於容器的 Azure 監視器提供哪些功能？

適用于容器的 Azure 監視器可使用 Azure 監視器的不同功能來提供完整的監視體驗。 這些功能可讓您瞭解執行 Linux 和 Windows Server 2019 作業系統的 Kubernetes 叢集的效能和健康情況，以及容器工作負載。 使用適用于容器的 Azure 監視器，您可以：

* 識別正在節點上執行的 AKS 容器，以及其平均的處理器與記憶體使用率。 此知識可協助您識別資源瓶頸。
* 識別容器群組及其裝載於 Azure Container Instances 之容器的處理器和記憶體使用率。  
* 識別容器在控制器或 Pod 中的所在位置。 此知識可協助您檢視控制器或 Pod 的整體效能。 
* 檢閱在和支援 Pod 的標準程序無關之主機上執行的工作負載的資源使用率。
* 了解叢集在平均負載和最高負載之下的行為。 此知識可協助您識別所需的容量，並判斷叢集可承受的負載上限。 
* 設定警示，以主動通知您，或在節點或容器上的 CPU 和記憶體使用率超過閾值時，或在基礎結構或節點健全狀況匯總套件中發生健全狀況狀態變更時加以記錄。
* 與[Prometheus](https://prometheus.io/docs/introduction/overview/)整合，以查看它從節點和 Kubernetes 收集的應用程式和工作負載計量使用[查詢](container-insights-log-search.md)來建立自訂警示、儀表板，以及詳細的執行詳細分析。
* 監視[部署至 AKS 引擎](https://github.com/Azure/aks-engine)內部部署的容器工作負載，以及 Azure Stack 上的[AKS 引擎](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)。
* 監視[部署至 Azure Red Hat OpenShift](../../openshift/intro-openshift.md)的容器工作負載。

    >[!NOTE]
    >Azure Red Hat OpenShift 的支援目前是公開預覽中的一項功能。
    >

與 Linux 叢集相較之下，監視 Windows Server 叢集的主要差異如下：

- 記憶體 RSS 計量不適用於 Windows 節點和容器。
- 磁片儲存體容量資訊不適用於 Windows 節點。
- 容器記錄不適用於在 Windows 節點中執行的容器。
- 「即時資料」（預覽）功能支援適用于 Windows 容器記錄的例外狀況。
- 只有 pod 環境會受到監視，而不是 Docker 環境。
- 在預覽版本中，最多可支援30個 Windows Server 容器。 這項限制不適用於 Linux 容器。 

查看下列提供中繼層級深入探討的影片，以協助您瞭解如何使用容器的 Azure 監視器監視 AKS 叢集。

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>如何存取此功能？

您有兩種方式可用來存取適用於容器的 Azure 監視器：從 Azure 監視器或直接從所選取的 AKS 叢集。 從 Azure 監視器中，您會看到所有已部署的容器的全域觀點，這些都是受監視的，而不是，可讓您在訂用帳戶和資源群組之間進行搜尋和篩選，然後向下切入至容器的 Azure 監視器選取的容器。  否則，您可以直接從 [AKS] 頁面中選取的 AKS 容器存取該功能。  

![存取適用於容器的 Azure 監視器方法概觀](./media/container-insights-overview/azmon-containers-experience.png)

如果您想要監視和管理在 AKS 外部執行的 Docker 和 Windows 容器主機，以查看設定、audit 和資源使用率，請參閱[容器監視解決方案](../../azure-monitor/insights/containers.md)。

## <a name="next-steps"></a>後續步驟

若要開始監視您的 Kubernetes 叢集，請參閱[如何啟用容器的 Azure 監視器](container-insights-onboard.md)，以瞭解啟用監視的需求和可用方法。 
