---
title: Azure Kubernetes Service 的支援原則（AKS）
description: 瞭解 Azure Kubernetes Service （AKS）支援原則、共同責任，以及處於預覽狀態的功能（或 Alpha 或搶鮮版（Beta））。
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: 9a68a0d0a288a27d67a9615385391c06be2b662d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76767371"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes Service 的支援原則

本文提供有關 Azure Kubernetes Service 的技術支援原則和限制的詳細資料（AKS）。 本文也會詳細說明背景工作節點管理、受管理的控制平面元件、協力廠商的開放原始碼元件，以及安全性或修補程式管理。

## <a name="service-updates-and-releases"></a>服務更新和發行

* 如需版本資訊，請參閱[AKS 版本](https://github.com/Azure/AKS/releases)資訊。
* 如需預覽功能的詳細資訊，請參閱[AKS 預覽功能和相關專案](https://github.com/Azure/AKS/blob/master/previews.md)。

## <a name="managed-features-in-aks"></a>AKS 中的受控功能

基礎結構即服務（IaaS）雲端元件（例如計算或網路元件）可讓使用者存取低層級的控制項和自訂選項。 相較之下，AKS 提供了全包式的 Kubernetes 部署，讓客戶能夠擁有所需的一組通用設定和功能。 AKS 客戶具有有限的自訂、部署和其他選項。 這些客戶不需要直接擔心或管理 Kubernetes 叢集。

有了 AKS，客戶就能取得完全受控的*控制平面*。 控制平面包含客戶操作所需的所有元件和服務，並為使用者提供 Kubernetes 叢集。 所有 Kubernetes 元件都是由 Microsoft 所維護及運作。

Microsoft 會透過 [控制] 窗格來管理和監視下列元件：

* Kubelet 或 Kubernetes API 伺服器
* Etcd 或相容的索引鍵/值存放區，提供服務品質（QoS）、擴充性和執行時間
* DNS 服務（例如 kube-dns 或 CoreDNS）
* Kubernetes proxy 或網路功能

AKS 不是完全受控的叢集解決方案。 某些元件（例如背景工作角色節點）具有*共同責任*，使用者必須協助維護 AKS 叢集。 例如，需要使用者輸入，才能套用背景工作節點作業系統（OS）安全性修補程式。

這些服務是以 Microsoft 和 AKS 團隊部署、操作及負責服務可用性和功能的意義來*管理*。 客戶無法改變這些受管理的元件。 Microsoft 會限制自訂，以確保一致且可調整的使用者體驗。 如需可完全自訂的解決方案，請參閱[AKS Engine](https://github.com/Azure/aks-engine)。

> [!NOTE]
> AKS 背景工作節點會在 Azure 入口網站中顯示為一般的 Azure IaaS 資源。 但這些虛擬機器會部署到自訂的 Azure 資源群組（前面加上 MC\\*）。 您可以變更 AKS 背景工作節點。 例如，您可以使用安全殼層（SSH），以變更一般虛擬機器的方式變更 AKS 背景工作節點（不過，您無法變更基本 OS 映射，而且變更可能不會透過更新或重新開機來保存），而且您可以將其他 Azure 資源附加至 AKS背景工作節點。 但是當您在 *頻外管理和自訂* 時進行變更時，AKS 叢集可能會變成不受支援。 除非 Microsoft 支援服務會引導您進行變更，否則請避免變更背景工作節點。

如上面所述發出不支援的作業（例如，所有代理程式節點的頻外解除配置），則會呈現不支援的叢集。 AKS 保留封存控制飛機的權利，此中心已設定為不超過30天的延長期間。 AKS 會維護叢集 etcd 中繼資料的備份，而且可以輕易地重新配置叢集。 此重新配置可由任何 PUT 作業起始，使叢集回到支援，例如升級或調整為使用中的代理程式節點。

## <a name="shared-responsibility"></a>共同負擔的責任

建立叢集時，客戶會定義 AKS 建立的 Kubernetes 背景工作角色節點。 客戶工作負載會在這些節點上執行。 客戶擁有和可以查看或修改背景工作節點。

由於客戶的叢集節點會執行私用程式碼並儲存敏感性資料，因此 Microsoft 支援服務只能以有限的方式進行存取。 Microsoft 支援服務無法登入這些節點、在其中執行命令，或在客戶沒有快速允許或協助的情況下，檢視這些節點的記錄。

由於背景工作節點是機密的，因此 Microsoft 會特別小心限制其背景管理。 在許多情況下，即使 Kubernetes 主要節點、etcd 和其他 Microsoft 管理的元件失敗，您的工作負載仍會繼續執行。 隨意修改過的背景工作節點可能會導致資料和工作負載遺失，而且可以轉譯叢集不受支援。

## <a name="aks-support-coverage"></a>AKS 支援涵蓋範圍

Microsoft 可針對下列問題提供技術支援：

* 與 Kubernetes 服務提供並支援的所有 Kubernetes 元件（例如 API 伺服器）的連線能力。
* Kubernetes 控制平面服務的管理、執行時間、QoS 和作業（例如 Kubernetes 主要節點、API 伺服器、etcd 和 kube dns）。
* Etcd. 支援包括每30分鐘自動備份所有 etcd 資料，以進行嚴重損壞規劃和叢集狀態還原。 這些備份無法直接提供給客戶或使用者。 它們可確保資料的可靠性和一致性。
* 適用于 Kubernetes 的 Azure 雲端提供者驅動程式中的任何整合點。 其中包括與其他 Azure 服務的整合，例如負載平衡器、持續性磁片區或網路功能（Kubernetes 和 Azure CNI）。
* 有關自訂控制平面元件的問題或問題，例如 Kubernetes API 伺服器、etcd 和 kube dns。
* 網路的相關問題，例如 Azure CNI、kubenet，或其他網路存取和功能問題。 問題可能包括 DNS 解析、封包遺失、路由等等。 Microsoft 支援各種網路功能案例：
  * 叢集中的 Kubenet （基本）和 advanced 網路（Azure CNI）和相關聯的元件
  * 與其他 Azure 服務和應用程式的連線能力
  * 輸入控制器和輸入或負載平衡器設定
  * 網路效能和延遲

Microsoft 不會提供下列各項的技術支援：

* 有關如何使用 Kubernetes 的問題。 例如，Microsoft 支援服務不會建議您如何建立自訂輸入控制器、使用應用程式工作負載，或套用第三方或開放原始碼軟體套件或工具。
  > [!NOTE]
  > Microsoft 支援服務可以建議 AKS 叢集功能、自訂和調整（例如 Kubernetes 作業問題和程式）。
* 協力廠商開放原始碼專案未提供為 Kubernetes 控制平面的一部分，或與 AKS 叢集一起部署。 這些專案可能包括 Istio、Helm、Envoy 或其他專案。
  > [!NOTE]
  > Microsoft 可以為協力廠商開放原始碼專案（例如 Helm 和 Kured）提供最佳的支援。 當協力廠商開放原始碼工具與 Kubernetes Azure 雲端提供者或其他 AKS 特定的錯誤整合時，Microsoft 會支援 Microsoft 檔中的範例和應用程式。
* 協力廠商的開放原始碼軟體。 此軟體可以包含安全性掃描工具和網路裝置或軟體。
* 建置多重雲端或多重廠商的相關問題。 例如，Microsoft 不支援與執行同盟多重公用雲端廠商解決方案相關的問題。
* [AKS 檔](https://docs.microsoft.com/azure/aks/)中所列以外的網路自訂。
  > [!NOTE]
  > Microsoft 支援與網路安全性群組（Nsg）相關的問題和錯誤。 例如，Microsoft 支援服務可以回答 NSG 無法更新的問題，或未預期的 NSG 或負載平衡器行為。

## <a name="aks-support-coverage-for-worker-nodes"></a>背景工作節點的 AKS 支援涵蓋範圍

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>AKS 背景工作節點的 Microsoft 責任

Microsoft 和客戶會分擔 Kubernetes 背景工作節點的責任，其中：

* 基本 OS 映射具有必要的新增專案（例如監視和網路代理程式）。
* 背景工作節點會自動接收 OS 修補程式。
* 在背景工作節點上執行之 Kubernetes 控制平面元件的問題會自動進行補救。 元件包括下列各項：
  * Kube-proxy
  * 提供 Kubernetes 主要元件通訊路徑的網路通道
  * Kubelet
  * Docker 或 Moby daemon

> [!NOTE]
> 在背景工作節點上，如果控制平面元件無法運作，AKS 小組可能需要重新開機個別元件或整個背景工作節點。 這些重新開機作業會自動化，並針對常見的問題提供自動修復。 這些重新開機只會在_節點_層級執行，而不會發生在叢集上，除非有緊急維護或中斷。

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>AKS 背景工作節點的客戶責任

Microsoft 不會自動重新開機背景工作節點來套用 OS 層級的修補程式。 雖然 OS 修補程式會傳遞至背景工作節點，但*客戶*須負責重新開機背景工作角色節點，以套用變更。 共用程式庫、系統或作業系統層級上的守護程式（如固態混合式磁片磁碟機（SSHD）和其他元件）都會自動修補。

客戶須負責執行 Kubernetes 升級。 他們可以透過 Azure 控制台或 Azure CLI 來執行升級。 這適用于包含 Kubernetes 安全性或功能改進的更新。

> [!NOTE]
> 由於 AKS 是*受控服務*，因此其最終目標包括移除修補程式、更新和記錄檔收集的責任，讓服務管理更完整且更容易使用。 隨著服務的端對端管理容量增加，未來版本可能會省略一些功能（例如，節點重新開機和自動修補）。

### <a name="security-issues-and-patching"></a>安全性問題和修補

如果在 AKS 的一或多個元件中發現安全性缺陷，AKS 小組會修補所有受影響的叢集，以減輕問題。 或者，Microsoft 的小組也會向使用者提供升級指引。

對於安全性缺陷會影響的背景工作節點，如果有零停機修補程式可供使用，AKS 小組會套用該修補程式，並通知使用者該變更。

當安全性修補程式需要背景工作節點重新開機時，Microsoft 會通知客戶這項需求。 客戶須負責重新開機或更新，以取得叢集修補程式。 如果使用者未根據 AKS 指引來套用修補程式，其叢集將會繼續受到安全性問題的影響。

### <a name="node-maintenance-and-access"></a>節點維護和存取

背景工作節點是共同責任，且由客戶所擁有。 因此，客戶能夠登入其背景工作節點，並進行可能有害的變更，例如核心更新以及安裝或移除套件。

如果客戶進行破壞性變更，或導致控制平面元件離線或變成無法運作，AKS 會偵測到此失敗，並自動將背景工作節點還原為先前的工作狀態。

雖然客戶可以登入和變更背景工作節點，但不建議這麼做，因為變更可能會使叢集不受支援。

## <a name="network-ports-access-and-nsgs"></a>網路埠、存取和 Nsg

AKS 是一種受控服務，具有特定的網路和連線能力需求。 這些需求與一般 IaaS 元件的需求相比較不具彈性。 在 AKS 中，像是自訂 NSG 規則、封鎖特定埠（例如，使用封鎖輸出埠443的防火牆規則）和允許清單 Url 等作業，都可以讓您的叢集不受支援。

> [!NOTE]
> 目前，AKS 不允許您完全鎖定來自叢集的輸出流量。 若要控制叢集可用於輸出流量的 Url 和埠清單，請參閱[限制傳出流量](limit-egress-traffic.md)。

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>不支援的 Alpha 和 Beta Kubernetes 功能

AKS 僅支援上游 Kubernetes 專案內的穩定功能。 除非另有記載，否則 AKS 不支援上游 Kubernetes 專案中提供的 Alpha 和 Beta 版功能。

在兩種情況下，Alpha 或 Beta 版功能可能會在正式推出之前推出：

* 客戶已符合 AKS 產品、支援或工程小組，並已要求試用這些新功能。
* [功能旗](https://github.com/Azure/AKS/blob/master/previews.md)標已啟用這些功能。 客戶必須明確選擇使用這些功能。

## <a name="preview-features-or-feature-flags"></a>預覽功能或功能旗標

對於需要擴充測試和使用者意見反應的特性和功能，Microsoft 發行了功能旗標背後的新預覽功能或功能。 請將這些功能視為發行前版本或搶鮮版 (Beta) 功能。

預覽功能或功能旗標功能並非設計來用於生產環境。 應用程式開發介面和行為、錯誤修正和其他變更的持續性變更，可能會導致不穩定的叢集和停機時間。

公開預覽中的功能正處於「最佳支援」狀態，因為這些功能處於預覽階段，不適用於生產環境，而且僅限上班時間的 AKS 技術支援小組支援。 如需其他資訊，請參閱：

* [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> 預覽功能會在 Azure*訂*用帳戶層級生效。 請勿在生產訂用帳戶上安裝預覽功能。 在生產訂用帳戶上，預覽功能可能會變更預設 API 行為，並影響一般作業。

## <a name="upstream-bugs-and-issues"></a>上游錯誤和問題

由於上游 Kubernetes 專案中的開發速度，bug 總是會發生。 其中一些 bug 無法在 AKS 系統內修補或解決。 相反地，bug 修正需要較大的對上游專案的修補程式（例如 Kubernetes、節點或背景工作作業系統，以及核心）。 針對 Microsoft 所擁有的元件（例如 Azure 雲端提供者），AKS 和 Azure 人員致力於修正在社區上游的問題。

當技術支援問題是由一或多個上游 bug 所造成的根本原因時，AKS 支援和工程小組將會：

* 識別並連結上游 bug 與任何支援的詳細資料，以協助說明此問題為什麼會影響您的叢集或工作負載。 客戶會收到所需存放庫的連結，讓他們能夠監看問題，並查看新版本何時會提供修正程式。
* 提供潛在的解決方法或緩和措施。 如果可以減輕問題，AKS 存放庫中將會記載[已知問題](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)。 已知問題的歸檔會說明：
  * 問題，包括上游 bug 的連結。
  * 解決方案的升級或其他持續性的因應措施和詳細資料。
  * 根據上游發行步調，大致瞭解問題的包含時間軸。
