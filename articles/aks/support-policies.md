---
title: Azure Kubernetes Service (AKS) 支援原則
description: 瞭解 Azure Kubernetes Service (AKS) 支援原則、共同責任，以及預覽狀態 (或 Alpha 或搶鮮版 (Beta)) 中的功能。
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: ce014fc338ebfad18b91ba65b614ca0a380a3f7c
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243846"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes Service 支援原則

本文會詳細說明 Azure Kubernetes Service (AKS) 的技術支援原則和限制。 本文也會詳細說明背景工作節點管理、受控的控制平面元件、第三方開放原始碼元件，以及安全性或修補程式管理。

## <a name="service-updates-and-releases"></a>服務更新和版本

* 如需詳細資訊，請參閱 [AKS 版本資訊](https://github.com/Azure/AKS/releases)。
* 如需預覽功能的詳細資訊，請參閱 [AKS 預覽功能和相關的專案](https://github.com/Azure/AKS/blob/master/previews.md)。

## <a name="managed-features-in-aks"></a>AKS 中的受控功能

基礎結構即服務 (IaaS) 雲端元件 (例如，計算或網路元件) 可讓使用者存取低層級的控制和自訂選項。 對比之下，AKS 則提供了周全的 Kubernetes 部署，讓客戶能夠擁有所需的一組通用設定和功能。 AKS 客戶具有有限的自訂、部署和其他選項。 這些客戶無須擔心也不用直接管理 Kubernetes 叢集。

有了 AKS，客戶即可獲得完全受控的*控制平面*。 控制平面包含客戶需要的的所有元件和服務，以操作並為終端使用者提供 Kubernetes 叢集。 所有 Kubernetes 元件都是由 Microsoft 維護及運作。

Microsoft 會透過控制窗格來管理和監視下列元件：

* Kubelet 或 Kubernetes API 伺服器
* Etcd 或相容的機碼值存放區，提供服務品質 (QoS)、可擴縮性，以及執行階段
* DNS 服務 (例如，kube-dns 或 CoreDNS)
* Kubernetes Proxy 或網路

AKS 不是完全受控的叢集解決方案。 某些元件 (例如背景工作節點) 具有*共同責任*，其中使用者必須協助維護 AKS 叢集。 例如，需要使用者輸入，才能套用背景工作節點作業系統 (OS) 安全性修補程式。

這些服務是*受控*的，亦即由 Microsoft 和 AKS 部署、操作及負責服務的可用性和功能。 客戶無法變更這些受控元件。 Microsoft 會限制自訂，以確保一致且可調整的使用者體驗。 如需可完全自訂的解決方案，請參閱 [AKS 引擎](https://github.com/Azure/aks-engine)。

## <a name="shared-responsibility"></a>共同責任

建立叢集時，客戶會定義 AKS 所建立的 Kubernetes 背景工作節點。 客戶工作負載會在這些節點上執行。 客戶擁有並可檢視或修改背景工作節點。

由於客戶的叢集節點會執行私用程式碼並儲存敏感性資料，因此 Microsoft 支援服務只能以有限的方式進行存取。 Microsoft 支援服務無法登入這些節點、在其中執行命令，或在客戶沒有快速允許或協助的情況下，檢視這些節點的記錄。

由於背景工作節點具有敏感性，所以 Microsoft 特別小心限制其背景管理。 在許多情況下，即使 Kubernetes 主要節點、etcd 和其他由 Microsoft 管理的元件失敗，您的工作負載仍會繼續執行。 隨意修改背景工作節點可能會造成資料和工作負載遺失，而且可能導致叢集不受支援。

## <a name="aks-support-coverage"></a>AKS 支援涵蓋範圍

Microsoft 可針對下列問題提供技術支援：

> [!NOTE]
> Microsoft/AKS 所採取的任何叢集動作，都是在內建的 Kubernetes 角色 `aks-service` 和內建角色系結之下，以使用者同意來進行 `aks-service-rolebinding` 。 此角色可讓 AKS 對叢集問題進行疑難排解和診斷，但無法修改許可權，也無法建立角色或角色系結，或其他高許可權動作。 只有在具有即時 (JIT) 存取權的作用中支援票證底下，才會啟用角色存取。

* 連線至 Kubernetes 服務提供和支援的所有 Kubernetes 元件，例如 API 伺服器。
* Kubernetes 控制平面服務的管理、運作時間、QoS 和作業 (例如 Kubernetes 主要節點、API 伺服器、etcd 和 kube-dns)。
* Etcd。 支援包括每 30 分鐘自動且透明地備份所有 etcd 資料，以進行災害規劃和叢集狀態還原。 這些備份無法直接提供給客戶或使用者。 它們可確保資料的可靠性和一致性。 Etcd。 「隨選復原」或「還原」不支援做為功能。
* 適用於 Kubernetes 的 Azure 雲端提供者驅動程式中的任何整合點。 其中包括與其他 Azure 服務的整合，例如負載平衡器、永久性磁碟區或網路 (Kubernetes 和 Azure CNI)。
* 自訂控制平面元件的相關問題，例如 Kubernetes API 伺服器、etcd 和 kube-dns。
* 網路的相關問題，例如 Azure CNI、kubenet，或其他網路存取和功能問題。 問題可能包括 DNS 解析、封包遺失、路由等等。 Microsoft 支援各種網路功能案例：
  * 叢集中的 Kubenet (基本) 和進階網路 (Azure CNI) 以及相關元件
  * 與其他 Azure 服務和應用程式的連線能力
  * 輸入控制器和輸入或負載平衡器設定
  * 網路效能與延遲

Microsoft 不會針對下列領域提供技術支援：

* 有關如何使用 Kubernetes 的問題。 例如，Microsoft 支援服務不會建議您如何建立自訂輸入控制器、使用應用程式工作負載，或套用第三方或開放原始碼軟體套件或工具。
  > [!NOTE]
  > Microsoft 支援服務可以建議 AKS 叢集功能、自訂和調整 (例如，Kubernetes 作業問題和程序)。
* 未提供為 Kubernetes 控制平面的一部分或不是以 AKS 叢集所部署的第三方開放原始碼專案。 這些專案可能包括 Istio、Helm、Envoy 等等。
  > [!NOTE]
  > Microsoft 可以為第三方開放原始碼專案 (例如 Helm 和 Kured) 提供最佳支援。 當第三方開放原始碼工具與 Kubernetes Azure 雲端提供者或其他 AKS 特定的錯誤整合時，Microsoft 會支援 Microsoft 文件中的範例和應用程式。
* 第三方封閉原始碼軟體。 此軟體可以包含安全性掃描工具和網路裝置或軟體。
* 建置多重雲端或多重廠商的相關問題。 例如，Microsoft 不支援與執行同盟多重公用雲端廠商解決方案相關的問題。
* [AKS 文件](./index.yml)中所列項目以外的網路自訂。
  > [!NOTE]
  > Microsoft 支援與網路安全性群組 (NSG) 相關的問題和錯誤。 例如，Microsoft 支援服務可以回答 NSG 無法更新的問題，或未預期的 NSG 或負載平衡器行為。

## <a name="aks-support-coverage-for-worker-nodes"></a>背景工作節點的 AKS 支援涵蓋範圍

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>AKS 背景工作節點的 Microsoft 責任

Microsoft 和客戶會共同負責 Kubernetes 背景工作節點，其中：

* 基礎 OS 映像具有必要的新增項目 (例如，監視和網路代理程式)。
* 背景工作節點會自動接收 OS 修補程式。
* 在背景工作節點上執行的 Kubernetes 控制平面元件問題會自動進行補救。 元件包括下列項目：
  * Kube-proxy
  * 提供通往 Kubernetes 主要元件通訊路徑的網路通道
  * Kubelet
  * Docker 或 Moby 精靈

> [!NOTE]
> 在背景工作節點上，如果控制平面元件無法運作，AKS 團隊可能需要重新啟動個別元件或整個背景工作節點。 這些重新啟動作業會自動化，並針對常見問題提供自動修復。 這些重新啟動只會發生在_節點_層級，而非叢集，除非有緊急維護或中斷。

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>AKS 背景工作節點的客戶責任

Microsoft 不會為了套用作業系統層級的修補程式而自動重新啟動背景工作節點。 雖然作業系統修補程式會傳遞至背景工作節點，但須由*客戶*負責重新啟動背景工作節點以套用變更。 共用程式庫、精靈 (例如固態混合式磁碟機 (SSHO))，以及其他系統或作業系統層級的元件會自動進行修補。

客戶負責執行 Kubernetes 升級。 他們可透過 Azure 控制台或 Azure CLI 執行升級。 這適用於包含 Kubernetes 安全性或功能改進的更新。

#### <a name="user-customization-of-worker-nodes"></a>背景工作節點的使用者自訂
> [!NOTE]
> AKS 背景工作節點在 Azure 入口網站中會顯示為一般的 Azure IaaS 資源。 但這些虛擬機器會部署到自訂的 Azure 資源群組 (前面加上 MC\\*)。 您可以從其基本設定擴充 AKS 背景工作節點。 例如，您可以使用安全殼層 (SSH)，以變更一般虛擬機器的方式來變更 AKS 背景工作節點。 但是，您無法變更基礎 OS 映像。 任何自訂的變更可能不會透過升級、縮放、更新或重新啟動而保存。 **不過**，*在 AKS API 頻外或範圍之外*進行變更，會導致 AKS 叢集變成不受支援。 除非 Microsoft 支援服務引導您進行變更，否則請避免變更背景工作節點。

發行上述定義的不受支援作業，例如將所有代理程式節點以頻外方式解除配置，將導致叢集不受支援。 對於超過支援準則範圍 (等於或超過 30 天) 的已設定控制平面，AKS 保留封存的權利。 AKS 會維護叢集 etcd 中繼資料的備份，並且可以隨時重新配置叢集。 此重新配置可由任何 PUT 作業啟動，回復叢集的支援，例如升級或調整為作用中的代理程式節點。

AKS 會代表客戶管理背景工作節點的生命週期和作業 - **不支援**修改與背景工作節點相關的 IaaS 資源。 不受支援的作業範例包括：透過 VMSS 入口網站或 VMSS API 手動變更 VMSS 上的設定，以自訂節點集區 VM 擴展集。
 
對於工作負載特定設定或套件，AKS 建議使用 [Kubernetes daemonset](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)。

使用 Kubernetes 特殊權限 Daemonset 和初始容器，可讓客戶在叢集背景工作節點上調整/修改或安裝協力廠商軟體。 這類自訂的範例包括新增自訂安全性掃描軟體或更新 sysctl 設定。

雖然這是上述需求適用的建議路徑，但 AKS 工程和支援無法協助疑難排解或診斷損毀/無法正常運作的修改，或是由於客戶部署 daemonset 而導致節點無法使用的情況。

> [!NOTE]
> 使用 AKS 作為*受控服務*，其最終目標是移除修補程式、更新、記錄收集的責任，讓服務管理更加完善，而且可自動處理。 隨著服務的端對端管理容量增加，未來版本可能會省略一些功能 (例如，節點重新啟動和自動修補)。

### <a name="security-issues-and-patching"></a>安全性問題和修補

如果在 AKS 的一或多個元件中發現安全性缺陷，AKS 團隊會修補所有受影響的叢集，以減輕問題。 或者，Microsoft 的小組也會向使用者提供升級指引。

對於受到安全性缺陷影響的背景工作節點，如果有可用的零停機修補程式，AKS 團隊將套用該修補程式，並通知使用者變更。

當安全性修補程式需要背景工作節點重新啟動時，Microsoft 會通知客戶這項需求。 客戶須負責重新啟動或更新，以取得叢集修補程式。 如果使用者未根據 AKS 的指引來套用修補程式，其叢集將會繼續受到安全性問題的影響。

### <a name="node-maintenance-and-access"></a>節點維護和存取

背景工作節點是共同的責任，並且由客戶擁有。 因此，客戶能夠登入其背景工作節點，並進行可能有害的變更，例如核心更新，以及安裝或移除套件。

如果客戶進行破壞性變更，或造成控制平面元件離線或變成無法運作，AKS 將會偵測到此故障，並自動將背景工作節點還原至先前的運作狀態。

雖然客戶可以登入和變更背景工作節點，但不建議這麼做，因為變更可能會使叢集不受支援。

## <a name="network-ports-access-and-nsgs"></a>網路連接埠、存取權和 NSG

AKS 是一種受控服務，具有特定的網路和連線能力需求。 這些需求與一般 IaaS 元件的需求相比，較不具彈性。 在 AKS 中，像是自訂 NSG 規則、封鎖特定連接埠 (例如，使用封鎖輸出連接埠 443 的防火牆規則) 和允許清單 URL 等作業，都可以讓您的叢集不受支援。

> [!NOTE]
> 目前，AKS 不允許您完全鎖定來自叢集的輸出流量。 若要控制叢集可用於輸出流量的 URL 和連接埠清單，請參閱[限制輸出流量](limit-egress-traffic.md)。

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>不支援的 Alpha 版和搶鮮版 (Beta) Kubernetes 功能

AKS 僅支援上游 Kubernetes 專案內的穩定功能。 除非另有記載，否則 AKS 不支援上游 Kubernetes 專案中提供的 Alpha 版和搶鮮版 (Beta) 功能。

在兩種情況下，Alpha 版或搶鮮版 (Beta) 功能可能會在正式推出之前發行：

* 客戶已與 AKS 產品、支援或工程師團隊會面，並受到試用這些新功能的要求。
* 這些功能[已根據功能旗標啟用](https://github.com/Azure/AKS/blob/master/previews.md)。 客戶必須明確選擇使用這些功能。

## <a name="preview-features-or-feature-flags"></a>預覽功能或功能旗標

對於需要延伸測試和使用者意見反應的特性和功能，Microsoft 發行了新的預覽功能或功能旗標後的功能。 請將這些功能視為發行前版本或搶鮮版 (Beta) 功能。

預覽功能或功能旗標功能並非設計來用於生產環境。 持續的 API 變更和行為、錯誤修正以及其他變更，可能會導致不穩定的叢集和停機時間。

對於公開預覽中的功能，其支援方式為「盡力支援」，因為這些功能處於預覽狀態，且不適用於生產環境，AKS 技術支援團隊僅在上班時間提供支援。 如需相關資訊，請參閱：

* [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> 預覽功能會在 Azure *訂閱*層級生效。 請勿在生產訂閱上安裝預覽功能。 在生產訂閱上，預覽功能可能會變更預設的 API 行為，並影響一般作業。

## <a name="upstream-bugs-and-issues"></a>上游的錯誤和問題

有鑑於上游 Kubernetes 專案中的開發速度，錯誤難免會發生。 其中部分錯誤無法在 AKS 系統內修補或解決。 上游專案需要較大型的修補程式 (例如 Kubernetes、節點或背景工作角色作業系統，以及核心) 來修正錯誤。 對於 Microsoft 擁有的元件 (例如 Azure 雲端提供者)，AKS 和 Azure 人員致力於修正社群上游的問題。

當技術支援問題是由一或多個上游錯誤所造成，AKS 支援和工程師團隊將會：

* 識別上游錯誤，並將其與支援詳細資料連結，協助說明此問題為何會影響到您的叢集或工作負載。 客戶會收到所需存放庫的連結以便監看問題，並查看新版本何時會提供修正程式。
* 提供潛在的因應措施或緩和措施。 如果可以緩解問題，則會將[已知問題](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)建檔在 AKS 存放庫。 已知問題的建檔內容會包括下列說明：
  * 此問題的內容，包括上游錯誤的連結。
  * 關於升級或其他持續性解決方案的因應措施和詳細資料。
  * 根據上游的發行頻率，提供包含此問題的大致時間軸。
