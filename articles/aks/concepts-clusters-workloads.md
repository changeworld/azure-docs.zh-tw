---
title: Concepts - Azure Kubernetes Services (AKS) 的 Kubernetes 基本概念
description: 了解 Kubernetes 的基本叢集和工作負載元件，及其與 Azure Kubernetes Service (AKS) 中的功能有何關聯
services: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 17203123ceb0c196bd8f9011e2962f5022e54698
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92901296"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Services (AKS) 的 Kubernetes 核心概念

隨著應用程式開發移往以容器為基礎的方法，協調和管理資源的需求很重要。 Kubernetes 是領先的平台，可用來提供容錯應用程式工作負載的可靠排程。 Azure Kubernetes Service (AKS) 是受管理的 Kubernetes 供應項目，可進一步簡化容器型應用程式的部署和管理。

本文介紹核心 Kubernetes 基礎結構元件，例如 *控制平面* 、 *節點* 和 *節點* 集區。 此外也介紹 *Pod* 、 *部署* 和 *集合* 等工作負載資源，並說明如何將資源分組到 *命名空間* 中。

## <a name="what-is-kubernetes"></a>什麼是 Kubernetes？

Kubernetes 是一個快速發展中的平台，可管理容器型應用程式及其相關聯的網路和儲存體元件。 重點在於應用程式工作負載，而不是基礎結構元件。 Kubernetes 提供宣告式部署方法，並以一組完善的 API 管理作業，作為此部署方法的後盾。

您可以建置並執行新型、可攜、以微服務為基礎的應用程式，藉由使用 Kubernetes 來協調和管理這些應用程式元件的可用性而獲益。 隨著小組採用以微服務為基礎的應用程式而獲得進展，Kubernetes 對於無狀態與具狀態的應用程式均提供支援。

Kubernetes 屬於開放式平台，可讓您使用慣用的程式設計語言、作業系統、程式庫或訊息匯流排來建置您的應用程式。 現有的持續整合與持續傳遞 (CI/CD) 工具可與 Kubernetes 整合，以排程及部署發行。

Azure Kubernetes Service (AKS) 提供受控 Kubernetes 服務，可降低部署和核心管理工作的複雜度，包括協調升級。 AKS 控制平面是由 Azure 平臺所管理，您只需針對執行應用程式的 AKS 節點付費。 AKS 建置於開放原始碼 Azure Kubernetes Service 引擎 ([AKS 引擎][aks-engine]) 上。

## <a name="kubernetes-cluster-architecture"></a>Kubernetes 叢集架構

Kubernetes 叢集分成兩個元件：

- *控制平面* 節點可提供應用程式工作負載的核心 Kubernetes 服務和協調流程。
- *節點* 會執行您的應用程式工作負載。

![Kubernetes 控制平面和節點元件](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>控制平面

當您建立 AKS 叢集時，系統會自動建立並設定控制平面。 此控制平面會以受控 Azure 資源形式提供，並從使用者抽象化。 控制平面不會產生任何費用，只有屬於 AKS 叢集中的節點。 控制平面和其資源只會在您建立叢集所在的區域上。

控制平面包含下列核心 Kubernetes 元件：

- *kube-apiserver* - API 伺服器是基礎 Kubernetes API 得以公開的途徑。 此元件可提供管理工具的互動，例如 `kubectl` 或 Kubernetes 儀表板。
- *etcd* - 具有高可用性的 *etcd* 是 Kubernetes 中的金鑰值存放區，用以維護 Kubernetes 叢集和設定的狀態。
- *kube-scheduler* - 當您建立或調整應用程式時，排程器會判斷哪些節點可執行工作負載，並加以啟動。
- *kube-controller-manager* - 控制器管理員會監看較小型的，這些控制器負責執行複寫 Pod 和處理節點作業之類的動作。

AKS 提供單一租使用者控制平面，具有專用的 API 伺服器、排程器等。您可以定義節點的數目和大小，而 Azure 平臺會設定控制平面和節點之間的安全通訊。 與控制平面的互動是透過 Kubernetes Api （例如 `kubectl` 或 Kubernetes 儀表板）進行。

此受控控制平面表示您不需要設定像高可用性 *etcd* 存放區的元件，但這也表示您無法直接存取控制平面。 升級至 Kubernetes 是透過 Azure CLI 或 Azure 入口網站進行協調，而這會升級控制平面和節點。 若要針對可能的問題進行疑難排解，您可以透過 Azure 監視器記錄來檢查控制平面記錄檔。

如果您需要以特定方式設定控制平面，或需要直接存取它，您可以使用 [aks 引擎][aks-engine]來部署您自己的 Kubernetes 叢集。

如需相關的最佳作法，請參閱 [AKS 中叢集安全性和升級的最佳做法][operator-best-practices-cluster-security]。

## <a name="nodes-and-node-pools"></a>節點和節點集區

若要執行應用程式和支援的服務，您必須要有 Kubernetes *節點* 。 AKS 叢集中有一或多個節點，而此類節點是可執行 Kubernetes 節點元件和容器執行階段的 Azure 虛擬機器 (VM)：

- `kubelet`是 Kubernetes 代理程式，可處理來自控制平面的協調流程要求，以及排程執行要求的容器。
- 虛擬網路由每個節點上的 *kube-proxy* 負責處理。 Proxy 會路由網路流量，以及管理服務和 Pod 的 IP 定址。
- *容器執行階段* 這項元件可讓容器化應用程式執行其他資源並與其互動，例如虛擬網路和儲存體。 在 AKS 中，會使用 Moby 作為容器執行時間。

![Kubernetes 節點的 Azure 虛擬機器和支援的資源](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

節點的 Azure VM 大小將定義可用的 CPU 數量、記憶體數量，以及儲存體的大小和類型 (例如高效能 SSD 或一般 HDD)。 如果您預期有應用程式需要大量的 CPU 和記憶體或高效能儲存體，請據以規劃節點大小。 您也可以相應放大 AKS 叢集中的節點數目，以符合需求。

在 AKS 中，您叢集中節點的 VM 映射目前以 Ubuntu Linux 或 Windows Server 2019 為基礎。 當您建立 AKS 叢集或向外延展節點數目時，Azure 平臺會建立所要求的 Vm 數目並加以設定。 沒有手動設定可供您執行。 代理程式節點會以標準虛擬機器計費，因此您在使用的 VM 大小上所擁有的任何折扣 (包括 [Azure 保留][reservation-discounts]) 都會自動套用。

如果您需要使用不同的主機 OS、容器執行階段或要納入自訂套件，您可以使用 [aks-engine][aks-engine] 自行部署 Kubernetes 叢集。 上游 `aks-engine` 會在功能於 AKS 叢集中正式受到支援之前發行這些功能，並提供設定選項。 例如，如果您想要使用 Moby 以外的容器執行時間，您可以使用 `aks-engine` 來設定和部署符合您目前需求的 Kubernetes 叢集。

### <a name="resource-reservations"></a>資源保留

AKS 會使用節點資源，讓節點功能成為叢集的一部分。 此使用方式可能會在 AKS 中使用時，在節點的總資源和資源 allocatable 之間建立差異。 當您為使用者部署的 pod 設定要求和限制時，請務必注意這項資訊。

若要尋找節點的 allocatable 資源，請執行：
```kubectl
kubectl describe node [NODE_NAME]

```

為了維持節點的效能和功能，AKS 會在每個節點上保留資源。 當節點成長到更大的資源時，資源保留會因為需要管理的使用者部署的 pod 數量增加而增加。

>[!NOTE]
> 使用 AKS 附加元件（例如容器深入解析 (OMS) 將會耗用額外的節點資源。

- **Cpu** 保留的 cpu 取決於節點類型和叢集設定，因為執行其他功能，這可能會導致較少的 allocatable CPU

| 主機上的 CPU 核心 | 1    | 2    | 4    | 8    | 16 | 32|64|
|---|---|---|---|---|---|---|---|
|Kube-reserved (millicore) |60|100|140|180|260|420|740|

- **記憶體** -AKS 使用的記憶體包括兩個值的總和。

1. Kubelet daemon 會安裝在所有 Kubernetes 代理程式節點上，以管理容器建立和終止。 根據預設，在 AKS 上，此背景程式具有下列收回規則： *記憶體。可用<750Mi* ，這表示節點必須一律至少有 750 Mi allocatable。  當主機低於可用記憶體的閾值時，kubelet 會終止其中一個執行中的 pod 以釋出主機電腦上的記憶體，並加以保護。 當可用記憶體減少超過750Mi 臨界值時，就會觸發此動作。

2. 第二個值是 kubelet daemon (的記憶體保留率的回歸率，可正常運作 kube 保留) 。
    - 前 4 GB 記憶體的25%
    - 20% 的 (下 4 GB 的記憶體，最多 8 GB) 
    - 下 8 GB 記憶體的 10% (高達 16 GB) 
    - 下 112 GB 記憶體的 6% (高達 128 GB) 
    - 128 GB 以上記憶體的2%

上述的記憶體和 CPU 配置規則可用來讓代理程式節點保持良好狀態，包括一些對叢集健全狀況很重要的裝載系統 pod。 如果節點不是 Kubernetes 叢集的一部分，則這些配置規則也會讓節點回報較不常 allocatable 的記憶體和 CPU。 無法變更上述資源保留。

例如，如果節點提供 7 GB，它會報告34% 的記憶體未 allocatable，包括750Mi 的硬性收回閾值。

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

除了 Kubernetes 本身的保留之外，基礎節點作業系統也會保留大量的 CPU 和記憶體資源，以維護作業系統功能。

如需相關的最佳作法，請參閱 [AKS 中基本排程器功能的最佳做法][operator-best-practices-scheduler]。

### <a name="node-pools"></a>節點集區

相同設定的節點會一起分組到 *節點集區* 中。 Kubernetes 叢集包含一或多個節點集區。 您在建立 AKS 叢集時會定義初始的節點數目和大小，而建立 *預設節點集區* 。 AKS 中的這個預設節點集區包含用來執行代理程式節點的基礎 VM。

> [!NOTE]
> 若要確保叢集能夠可靠地運作，您應該在預設節點集區中執行至少 2 個 (兩個) 節點。

當您調整或升級 AKS 叢集時，相關動作會對預設節點集區執行。 您也可以選擇調整或升級特定的節點集區。 進行升級作業時，執行中的容器會排程於節點集區中的其他節點上，直到所有節點皆成功升級。

如需有關如何在 AKS 中使用多個節點集區的詳細資訊，請參閱 [在 AKS 中建立和管理叢集的多個節點][use-multiple-node-pools]集區。

### <a name="node-selectors"></a>節點選取器

在包含多個節點集區的 AKS 叢集中，您可能需要告知 Kubernetes 排程器要針對指定的資源使用哪個節點集區。 例如，輸入控制器不應該在 Windows Server 節點上執行。 節點選取器可讓您定義各種參數，例如節點 OS，以控制 pod 應排程的位置。

下列基本範例會使用節點選取器 *"Beta.kubernetes.io/os"* 來排定 linux 節點上的 NGINX 實例： linux：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

如需如何控制 pod 排程位置的詳細資訊，請參閱 [AKS 中先進排程器功能的最佳做法][operator-best-practices-advanced-scheduler]。

## <a name="pods"></a>Pod

Kubernetes 會使用 *Pod* 執行您的應用程式執行個體。 一個 Pod 代表應用程式的單一執行個體。 Pod 與容器之間通常會有 1:1 的對應，不過在進階案例中，Pod 可能會包含多個容器。 這些多容器 Pod 會一起排程於相同的節點上，並允許容器共用相關資源。

當您建立 pod 時，可以定義 *資源要求* 來要求特定數量的 CPU 或記憶體資源。 Kubernetes 排程器會嘗試將 Pod 排程在具有可用資源的節點上執行，以符合要求。 您也可以指定資源上限，以防止指定的 Pod 在基礎節點上耗用太多計算資源。 最佳做法是為所有 Pod 加上資源限制，以協助 Kubernetes 排程器了解哪些是必要且可供使用的資源。

如需詳細資訊，請參閱 [Kubernetes Pod][kubernetes-pods] 和 [Kubernetes Pod 生命週期][kubernetes-pod-lifecycle]。

Pod 是邏輯資源，但應用程式工作負載執行的所在之處是容器。 Pod 通常是暫時性、可處置的資源，且個別排程的 Pod 會無法獲益於 Kubernetes 所提供的一些高可用性和備援功能。 取而代之的是，pod 是由 Kubernetes *控制器* （例如部署控制器）所部署和管理。

## <a name="deployments-and-yaml-manifests"></a>部署和 YAML 資訊清單

一項 *部署* 可代表一或多個由 Kubernetes 部署控制器管理的相同 Pod。 部署會定義要建立的 *複本* (Pod) 數目，且 Kubernetes 排程器會確保在 Pod 或節點發生問題時可在狀況良好的節點上排程其他 Pod。

您可以更新部署以變更 Pod 的設定、使用的容器映像，或連結的儲存體。 部署控制器會清空並終止指定數目的複本、從新的部署定義建立複本，然後繼續進行處理，直到部署中的所有複本皆完成更新。

AKS 中的多數無狀態應用程式均應使用部署模型，而不是排程個別的 Pod。 Kubernetes 可監視部署的健康情況和狀態，以確定有所需數量的複本執行於叢集內。 當您只排程個別 pod 時，如果 pod 遇到問題，就不會重新開機，而且如果其目前節點發生問題，則不會重新排程狀況良好的節點。

如果應用程式需要執行個體仲裁以便隨時可供管理決策擬定之用，您就不應讓更新程序中斷該項功能。 您可以使用 *Pod 中斷預算* ，定義在更新或節點升級期間可在部署中停止多少個複本。 例如，如果您的部署中有 *五個 (5)* 的複本，您可以將 pod 中斷定義為 *4* ，一次只允許一個複本遭到刪除/重新排程。 與 Pod 資源限制相同，最佳做法是為需要有最少量複本持續存在的應用程式定義 Pod 中斷預算。

部署通常可透過 `kubectl create` 或 `kubectl apply` 來建立和管理。 若要建立部署，您可以定義 YAML (YAML 不是標記語言) 格式的資訊清單檔。 下列範例會建立 NGINX Web 伺服器的基本部署。 部署會指定要建立的 *三個 (3)* 複本，而且需要在容器上開啟埠 *80* 。 此外也會定義 CPU 和記憶體的資源要求和限制。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

您也可以在 YAML 資訊清單中納入負載平衡器之類的服務，以建立更複雜的應用程式。

如需詳細資訊，請參閱 [Kubernetes 部署][kubernetes-deployments]。

### <a name="package-management-with-helm"></a>使用 Helm 管理套件

[Helm][helm] 是在 Kubernetes 中管理應用程式的常見方法。 您可以建置和使用現有的公用 Helm *圖表* ，其中包含封裝版的應用程式程式碼，和用來部署資源的 Kubernetes YAML 資訊清單。 這些 Helm 圖表可儲存於本機，或通常儲存在遠端存放庫中，例如 [Azure Container Registry Helm 圖表存放庫][acr-helm]。

若要使用 Helm，請在您的電腦上安裝 Helm 用戶端，或使用 [Azure Cloud Shell][azure-cloud-shell]中的 Helm 用戶端。 您可以使用用戶端搜尋或建立 Helm 圖表，然後將其安裝至 Kubernetes 叢集。 如需詳細資訊，請參閱 [在 AKS 中使用 Helm 安裝現有的應用程式][aks-helm]。

## <a name="statefulsets-and-daemonsets"></a>StatefulSet 和 Daemonset

部署控制器會使用 Kubernetes 排程器，在任何有可用資源的可用節點上執行指定數量的複本。 這種部署使用方法可能足以因應無狀態應用程式的需要，但不適用於需要持續性命名慣例或儲存體的應用程式。 對於需要有複本存在於叢集內各個節點 (或選取的節點) 上的應用程式，部署控制器並不會考量複本分散到各節點間的方式。

有兩項 Kubernetes 資源可讓您管理此類型的應用程式：

- *StatefulSet* - 跨個別 Pod 生命週期維護應用程式的狀態，例如儲存體。
- *Daemonset* - 及早在 Kubernetes 啟動程序執行時確定每個節點都有執行中的執行個體。

### <a name="statefulsets"></a>StatefulSet

現今的應用程式開發通常以無狀態應用程式為目標，但 *StatefulSet* 可用於具狀態的應用程式，例如包含資料庫元件的應用程式。 StatefulSet 類似於會建立和管理一或多個相同 Pod 的部署。 StatefulSet 中的複本會依照正常、循序的方法進行部署、調整、升級和終止。 使用 StatefulSet (as 複本會重新排程) 命名慣例、網路名稱和儲存區保存。

您可以使用 `kind: StatefulSet` 定義 YAML 格式的應用程式，隨後再由 StatefulSet 控制器處理必要複本的部署和管理。 資料會寫入至 Azure 受控磁碟或 Azure 檔案所提供的永續性儲存體。 透過 StatefulSet，即使在 StatefulSet 刪除後，基礎的永續性儲存體仍將保存。

如需詳細資訊，請參閱 [Kubernetes StatefulSet][kubernetes-statefulsets]。

StatefulSet 中的複本可在 AKS 叢集中任何可用的節點上排程及執行。 如果您必須確定每個節點都至少要執行您集合中的一個 Pod，您可以改用 DaemonSet。

### <a name="daemonsets"></a>DaemonSet

針對特定的記錄收集或監視需求，您可能需要在所有或選取的節點上執行指定的 Pod。 *DaemonSet* 同樣可用來部署一或多個相同的 Pod，但 DaemonSet 控制器可確保每個指定的節點都會執行一個 Pod 執行個體。

DaemonSet 控制器可及早在叢集啟動程序執行時，在預設 Kubernetes 排程器啟動之前將 Pod 排程於節點上。 這項功能可確保 DaemonSet 中的 Pod 會在部署或 StatefulSet 中的傳統 Pod 排程之前啟動。

和 StatefulSet 相同，DaemonSet 也可使用 `kind: DaemonSet` 定義為 YAML 定義的一部分。

如需詳細資訊，請參閱 [Kubernetes DaemonSet][kubernetes-daemonset]。

> [!NOTE]
> 如果使用 [虛擬節點附加](virtual-nodes-cli.md#enable-virtual-nodes-addon)元件，daemonset 將不會在虛擬節點上建立 pod。

## <a name="namespaces"></a>命名空間

Kubernetes 資源 (例如 Pod 和部署) 會依邏輯分組到 *命名空間* 中。 藉由這樣的分組，即可依邏輯區隔 AKS 叢集，並限制建立、檢視或管理資源的存取權。 例如，您可以建立命名空間以區隔商務群組。 使用者只能與其指派的命名空間內包含的資源互動。

![依邏輯區隔資源和應用程式的 Kubernetes 命名空間](media/concepts-clusters-workloads/namespaces.png)

您在建立 AKS 叢集時，可以使用下列命名空間：

- *default* - 此命名空間是在未提供任何 Pod 和部署時依預設用來建立這些項目的位置。 在較小的環境中，您可以直接將應用程式部署到預設命名空間中，而無須建立額外的邏輯分隔。 當您與 Kubernetes API 互動時 (例如 `kubectl get pods`)，若未指定命名空間，將會使用預設值。
- *kube-system* - 此命名空間是核心資源的所在之處，例如 DNS 和 Proxy 之類的網路功能，或是 Kubernetes 儀表板。 您通常不會將自己的應用程式部署到此命名空間中。
- *kube-public* - 此命名空間通常不會使用，但可用於要在整個叢集中顯示，並且可供任何使用者檢視的資源。

如需詳細資訊，請參閱 [Kubernetes 命名空間][kubernetes-namespaces]。

## <a name="next-steps"></a>下一步

本文說明了部分核心 Kubernetes 元件，及其套用至 AKS 叢集的方式。 如需有關 Kubernetes 及 AKS 核心概念的詳細資訊，請參閱下列文章：

- [Kubernetes / AKS 存取和身分識別][aks-concepts-identity]
- [Kubernetes / AKS 安全性][aks-concepts-security]
- [Kubernetes / AKS 虛擬網路][aks-concepts-network]
- [Kubernetes / AKS 儲存體][aks-concepts-storage]
- [Kubernetes / AKS 調整][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
