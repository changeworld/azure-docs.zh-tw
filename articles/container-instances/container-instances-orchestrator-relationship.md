---
title: 容器實例和容器協調流程
description: 了解 Azure 容器執行個體與容器 Orchestrator 的互動方式。
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: f3f8693d1a9a12e7c35d126ab3e3ca53448e5e40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74533669"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure 容器執行個體和容器 Orchestrator

由於規模較小和應用程式方向的緣故，容器很適合用於敏捷式傳遞環境和微服務式架構。 自動執行和管理大量容器以及其互動方式的工作稱為「協調流程」**。 熱門容器協調器包括 Kubernetes、DC/OS 和 Docker Swarm。

Azure 容器執行個體提供了一些基本的協調流程平台排程功能。 但未涵蓋這些平台所提供的較高價值服務，因此事實上可與這些平台互補。 本文說明 Azure 容器執行個體所能處理的項目範圍，以及完整的容器 Orchestrator 如何與其互動。

## <a name="traditional-orchestration"></a>傳統協調流程

協調流程的標準定義包含下列工作：

- **排程**：在給定容器映像和資源要求的情況下，尋找適合用來執行容器的電腦。
- **親和性/反親和性**：指定一組容器應該在彼此附近執行 (以獲取效能) 或是彼此離得夠遠來執行 (以獲得可用性)。
- **健康情況監視**：監視容器的失敗，並自動為容器重新排程。
- **容錯移轉**：追蹤每部電腦上執行的項目，並將容器從失敗的電腦重新排程到狀況良好的節點。
- **調整**：以手動或自動方式新增或移除容器執行個體以符合需求。
- **網路**：提供重疊的網路以協調容器，讓它們跨越多部主機電腦彼此通訊。
- **服務探索**：讓容器可以自動找到彼此，即使它們移到不同的主機電腦和變更 IP 位址。
- **協調的應用程式升級**：管理容器的升級以避免應用程式停機，並可在發生錯誤時復原。

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>使用 Azure 容器執行個體的協調流程：分層式方法

Azure 容器執行個體可提供分層式協調流程方法，提供執行單一容器所需的所有排程和管理功能，同時讓 Orchestrator 平台管理在其上執行的多個容器工作。

容器執行個體的基礎結構皆由 Azure 負責管理，因此 Orchestrator 平台本身不需要擔心如何尋找用來執行單一容器的適當主機電腦。 雲端的彈性可確保永遠有適當的主機電腦可供使用。 相反地，Orchestrator 可以專注於簡化多容器架構開發的工作，包括調整作業與協調的升級。

## <a name="scenarios"></a>案例

雖然 Orchestrator 與 Azure 容器執行個體的整合技術仍未成熟，但我們預期可能會出現幾個不同的環境：

### <a name="orchestration-of-container-instances-exclusively"></a>獨佔的容器執行個體協調流程

因為這些執行個體啟動速度快且依秒計費，以獨佔方式立基於 Azure 容器執行個體的環境可提供最快的方法來開始使用，並處理高度變化的工作負載。

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>結合容器執行個體與虛擬機器中的容器

針對長時間執行的穩定工作負載，協調專用虛擬機器叢集中的容器所需的成本，一般會比使用 Azure 容器執行個體來執行相同容器還低。 不過，容器執行個體能提供絕佳的解決方案來快速擴展和收縮整體容量，以處理未預期的使用量或短暫出現的高峰使用量。

Orchestrator 可以直接排程使用 Azure 容器執行個體的其他容器，並在不再需要時加以刪除，而不是相應放大叢集中的虛擬機器數目，然後將其他容器部署至這些電腦。

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>範例實作：Azure Kubernetes Service (AKS) 的虛擬節點

若要快速調整 [Azure Kubernetes Service](../aks/intro-kubernetes.md) (AKS) 叢集中的應用程式工作負載，您可以使用在 Azure 容器執行個體中動態建立的「虛擬節點」**。 虛擬節點能夠進行在 ACI 與 AKS 叢集中執行的 pod 之間的網路通訊。 

虛擬節點目前支援 Linux 容器執行個體。 使用 [Azure CLI](https://go.microsoft.com/fwlink/?linkid=2047538) 或 [Azure 入口網站](https://go.microsoft.com/fwlink/?linkid=2047545)，開始使用虛擬節點。

虛擬節點會藉由註冊為無限容量的節點，進而使用開放原始碼 [Virtual Kubelet][aci-connector-k8s] 來模擬 Kubernetes [kubelet][kubelet-doc]。 Virtual Kubelet 會將 [pod][pod-doc] 的建立分派為 Azure 容器執行個體中的容器群組。

請參閱 [Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) 專案，以取得將 Kubernetes API 擴充到無伺服器容器平台的其他範例。

## <a name="next-steps"></a>後續步驟

使用[快速入門指南](container-instances-quickstart.md)對 Azure Container Instances 建立您的第一個容器。

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
