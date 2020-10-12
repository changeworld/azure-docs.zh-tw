---
title: Azure Kubernetes Service (AKS) 支援原則
description: 瞭解 Azure Kubernetes Service (AKS) 支援原則、共同責任，以及預覽狀態 (或 Alpha 或搶鮮版 (Beta)) 中的功能。
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 188416d9ef6f8e7568b10e8ccbb405be0bff315d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888975"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes Service 支援原則

本文會詳細說明 Azure Kubernetes Service (AKS) 的技術支援原則和限制。 本文也詳細說明代理程式節點管理、受控控制平面元件、協力廠商開放原始碼元件，以及安全性或修補程式管理。

## <a name="service-updates-and-releases"></a>服務更新和版本

* 如需詳細資訊，請參閱 [AKS 版本資訊](https://github.com/Azure/AKS/releases)。
* 如需預覽功能的詳細資訊，請參閱 [AKS 預覽功能和相關的專案](https://awesomeopensource.com/projects/aks?categoryPage=11)。

## <a name="managed-features-in-aks"></a>AKS 中的受控功能

基礎結構即服務 (IaaS) 雲端元件，例如計算或網路元件，可讓您存取低層級的控制項和自訂選項。 相較之下，AKS 提供了一個 Kubernetes 部署，為您的叢集提供您所需的一組通用設定和功能。 如果您是 AKS 使用者，則自訂和部署選項有限。 在 exchange 中，您不需要直接擔心或管理 Kubernetes 叢集。

透過 AKS，您可以取得完全受控的 *控制平面*。 控制平面包含操作所需的所有元件和服務，並提供 Kubernetes 叢集給終端使用者。 所有 Kubernetes 元件都是由 Microsoft 維護及運作。

Microsoft 會透過控制窗格來管理和監視下列元件：

* Kubelet 或 Kubernetes API 伺服器
* Etcd 或相容的機碼值存放區，提供服務品質 (QoS)、可擴縮性，以及執行階段
* DNS 服務 (例如，kube-dns 或 CoreDNS)
* Kubernetes Proxy 或網路
* 在 kube 系統命名空間中執行的任何其他附加元件或系統元件

AKS 不是平臺即服務 (PaaS) 解決方案。 某些元件（例如代理程式節點）具有 *共同責任*，使用者必須協助維護 AKS 叢集。 例如，需要使用者輸入，才能將代理程式節點作業系統 (作業系統) 安全性修補程式。

這些服務是*受控*的，亦即由 Microsoft 和 AKS 部署、操作及負責服務的可用性和功能。 客戶無法變更這些受控元件。 Microsoft 會限制自訂，以確保一致且可調整的使用者體驗。 如需可完全自訂的解決方案，請參閱 [AKS 引擎](https://github.com/Azure/aks-engine)。

## <a name="shared-responsibility"></a>共同責任

建立叢集時，您會定義 AKS 建立的 Kubernetes 代理程式節點。 您的工作負載會在這些節點上執行。

因為您的代理程式節點會執行私用程式碼並儲存機密資料，Microsoft 支援服務只能以非常有限的方式存取它們。 Microsoft 支援服務無法登入，請在中執行命令，或在沒有您的 express 許可權或協助的情況下，查看這些節點的記錄。

使用任何 IaaS Api 直接對代理程式節點進行的任何修改，都會呈現叢集不受支援。 對代理程式節點完成的任何修改都必須使用 kubernetes 原生機制（例如）來完成 `Daemon Sets` 。

同樣地，雖然您可以將任何中繼資料新增至叢集和節點（例如標籤和標籤），但變更任何系統建立的中繼資料將會轉譯不支援的叢集。

## <a name="aks-support-coverage"></a>AKS 支援涵蓋範圍

Microsoft 會提供下列範例的技術支援：

* 連線至 Kubernetes 服務提供和支援的所有 Kubernetes 元件，例如 API 伺服器。
* Kubernetes 控制平面服務的管理、執行時間、QoS 和操作 (Kubernetes 控制平面、API 伺服器、etcd 和 coreDNS，例如) 。
* Etcd 資料存放區。 支援包括每 30 分鐘自動且透明地備份所有 etcd 資料，以進行災害規劃和叢集狀態還原。 這些備份不會直接供您或任何使用者使用。 它們可確保資料的可靠性和一致性。 Etcd。 不支援隨選復原或還原作為功能。
* 適用於 Kubernetes 的 Azure 雲端提供者驅動程式中的任何整合點。 其中包括與其他 Azure 服務的整合，例如負載平衡器、永久性磁碟區或網路 (Kubernetes 和 Azure CNI)。
* 自訂控制平面元件的相關問題，例如 Kubernetes API 伺服器、etcd 和 coreDNS。
* 網路的相關問題，例如 Azure CNI、kubenet，或其他網路存取和功能問題。 問題可能包括 DNS 解析、封包遺失、路由等等。 Microsoft 支援各種網路功能案例：
  * 使用受控 Vnet 或自訂 (的 Kubenet 和 Azure CNI 攜帶您自己的) 子網。
  * 與其他 Azure 服務和應用程式的連線能力
  * 輸入控制器和輸入或負載平衡器設定
  * 網路效能與延遲


> [!NOTE]
> Microsoft/AKS 所採取的任何叢集動作都是在內建 Kubernetes 角色 `aks-service` 和內建角色系結的使用者同意下進行 `aks-service-rolebinding` 。 此角色可讓 AKS 對叢集問題進行疑難排解和診斷，但無法修改許可權，也無法建立角色或角色系結，或其他高許可權動作。 只有在具有即時 (JIT) 存取權的主動式支援票證下，才會啟用角色存取。

Microsoft 不會提供下列範例的技術支援：

* 有關如何使用 Kubernetes 的問題。 例如，Microsoft 支援服務不會建議您如何建立自訂輸入控制器、使用應用程式工作負載，或套用第三方或開放原始碼軟體套件或工具。
  > [!NOTE]
  > Microsoft 支援服務可以建議 AKS 叢集功能、自訂和調整 (例如，Kubernetes 作業問題和程序)。
* 未提供為 Kubernetes 控制平面的一部分或不是以 AKS 叢集所部署的第三方開放原始碼專案。 這些專案可能包括 Istio、Helm、Envoy 等等。
  > [!NOTE]
  > Microsoft 可以為協力廠商開放原始碼專案（例如 Helm）提供最佳支援。 當第三方開放原始碼工具與 Kubernetes Azure 雲端提供者或其他 AKS 特定的錯誤整合時，Microsoft 會支援 Microsoft 文件中的範例和應用程式。
* 第三方封閉原始碼軟體。 此軟體可以包含安全性掃描工具和網路裝置或軟體。
* [AKS 檔](./index.yml)中所列以外的網路自訂。


## <a name="aks-support-coverage-for-agent-nodes"></a>AKS 代理程式節點的支援涵蓋範圍

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>AKS 代理程式節點的 Microsoft 責任

Microsoft 和使用者共同負責 Kubernetes 代理程式節點，其中：

* 基礎 OS 映像具有必要的新增項目 (例如，監視和網路代理程式)。
* 代理程式節點會自動接收 OS 修補程式。
* 系統會自動補救在代理程式節點上執行的 Kubernetes 控制平面元件問題。 這些元件包括下列各項：
  * `Kube-proxy`
  * 提供通往 Kubernetes 主要元件通訊路徑的網路通道
  * `Kubelet`
  * `Moby` 或 `ContainerD`

> [!NOTE]
> 如果代理程式節點無法運作，AKS 可能會重新開機個別元件或整個代理程式節點。 這些重新開機作業會自動進行，並針對常見的問題提供自動修復。 如果您想要深入瞭解自動補救機制，請參閱 [節點自動修復](node-auto-repair.md)

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>AKS 代理程式節點的客戶責任

Microsoft 會為您每週提供映射節點的修補程式和新映射，但預設不會自動加以修補。 若要讓您的代理程式節點作業系統和執行時間元件保持修補，您應該保留一般 [節點映射升級](node-image-upgrade.md) 排程或將它自動化。

同樣地，AKS 會定期發行新的 kubernetes 修補程式和次要版本。 這些更新可包含 Kubernetes 的安全性或功能改進。 您必須負責將叢集的 kubernetes 版本更新，並根據 [AKS Kubernetes 支援版本原則](supported-kubernetes-versions.md)進行更新。

#### <a name="user-customization-of-agent-nodes"></a>代理程式節點的使用者自訂
> [!NOTE]
> AKS 代理程式節點會以一般 Azure IaaS 資源的形式出現在 Azure 入口網站中。 但是這些虛擬機器已部署至自訂的 Azure 資源群組， (通常會加上 MC_ \*) 。 您無法變更基底 OS 映射，或使用 IaaS Api 或資源對這些節點進行任何直接的自訂。 未透過 AKS API 完成的任何自訂變更都不會透過升級、調整、更新或重新開機來保存。 除非 Microsoft 支援服務指示您進行變更，否則請避免執行代理程式節點的變更。

AKS 管理代理程式節點的生命週期和作業，代表您修改與代理程式節點相關聯的 IaaS 資源， **不受支援**。 不支援的作業範例是透過虛擬機器擴展集入口網站或 API 手動變更設定，以自訂節點集區虛擬機器擴展集。
 
針對特定工作負載的設定或套件，AKS 建議[使用 `daemon sets` Kubernetes ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)。

使用 Kubernetes 特殊許可權 `daemon sets` 和 init 容器，可讓您在叢集代理程式節點上調整/修改或安裝協力廠商軟體。 這類自訂的範例包括新增自訂安全性掃描軟體或更新 sysctl 設定。

雖然在上述需求適用時建議使用此路徑，但 AKS 工程和支援無法協助疑難排解或診斷因自訂部署而導致節點無法使用的修改 `daemon set` 。

### <a name="security-issues-and-patching"></a>安全性問題和修補

如果在 AKS 的一或多個受控元件中發現安全性瑕疵，AKS 小組會修補所有受影響的叢集以減輕問題。 或者，Microsoft 的小組也會向使用者提供升級指引。

對於受安全性瑕疵影響的代理程式節點，Microsoft 會通知您有關影響的詳細資料，以及修正或降低安全性問題的步驟， (通常是節點映射升級或叢集修補升級) 。

### <a name="node-maintenance-and-access"></a>節點維護和存取

雖然您可以登入並變更代理程式節點，但不建議您這麼做，因為變更可以進行叢集不受支援。

## <a name="network-ports-access-and-nsgs"></a>網路連接埠、存取權和 NSG

您只能自訂子網的 Nsg。 您不得在受控子網或代理程式節點的 NIC 層級上自訂 Nsg。 AKS 具有特定端點的輸出需求、控制輸出和確保必要的連線能力，請參閱 [限制輸出流量](limit-egress-traffic.md)。

## <a name="stopped-or-de-allocated-clusters"></a>已停止或已解除配置的叢集

根據上述，透過 IaaS Api/CLI/入口網站手動取消配置所有叢集節點，會導致叢集不支援。
停止/取消配置所有節點的唯一支援方法是 [停止 aks 叢集，此叢集會保留最多12個月的叢集狀態。

停止超過12個月的叢集將不再保留狀態。 

在 AKS Api 之外解除配置的叢集無法保證狀態保留。 處於此狀態的叢集的控制項平面會在30天后封存，並在12個月後刪除。

對於超過支援準則範圍 (等於或超過 30 天) 的已設定控制平面，AKS 保留封存的權利。 AKS 會維護叢集 etcd 中繼資料的備份，並且可以隨時重新配置叢集。 此重新配置可由任何 PUT 作業啟動，回復叢集的支援，例如升級或調整為作用中的代理程式節點。

如果您的訂用帳戶已暫止或刪除，則會在90天后刪除您的叢集控制平面和狀態。

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>不支援的 Alpha 版和搶鮮版 (Beta) Kubernetes 功能

AKS 只支援上游 Kubernetes 專案內的穩定和搶鮮版（Beta）功能。 除非另有說明，否則 AKS 不支援上游 Kubernetes 專案中可用的任何 Alpha 功能。

## <a name="preview-features-or-feature-flags"></a>預覽功能或功能旗標

對於需要延伸測試和使用者意見反應的特性和功能，Microsoft 發行了新的預覽功能或功能旗標後的功能。 請將這些功能視為發行前版本或搶鮮版 (Beta) 功能。

預覽功能或功能旗標功能並非設計來用於生產環境。 持續的 API 變更和行為、錯誤修正以及其他變更，可能會導致不穩定的叢集和停機時間。

對於公開預覽中的功能，其支援方式為「盡力支援」，因為這些功能處於預覽狀態，且不適用於生產環境，AKS 技術支援團隊僅在上班時間提供支援。 如需詳細資訊，請參閱

* [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>上游的錯誤和問題

有鑑於上游 Kubernetes 專案中的開發速度，錯誤難免會發生。 其中部分錯誤無法在 AKS 系統內修補或解決。 相反地，bug 修正需要 (上游專案的較大修補程式，例如 Kubernetes、節點或代理程式作業系統，以及核心) 。 對於 Microsoft 擁有的元件 (例如 Azure 雲端提供者)，AKS 和 Azure 人員致力於修正社群上游的問題。

當技術支援問題是由一或多個上游錯誤所造成，AKS 支援和工程師團隊將會：

* 識別上游錯誤，並將其與支援詳細資料連結，協助說明此問題為何會影響到您的叢集或工作負載。 客戶會收到所需存放庫的連結以便監看問題，並查看新版本何時會提供修正程式。
* 提供潛在的解決方法或緩和措施。 如果可以緩解問題，則會將[已知問題](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)建檔在 AKS 存放庫。 已知問題的建檔內容會包括下列說明：
  * 此問題的內容，包括上游錯誤的連結。
  * 關於升級或其他持續性解決方案的因應措施和詳細資料。
  * 根據上游的發行頻率，提供包含此問題的大致時間軸。
