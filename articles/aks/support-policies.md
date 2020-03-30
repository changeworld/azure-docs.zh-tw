---
title: 支援 Azure 庫伯奈斯服務 （AKS） 的策略
description: 瞭解 Azure 庫伯奈斯服務 （AKS） 支援策略、分擔責任以及預覽版（或 Alpha 或 Beta 版）中的功能。
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: a5d90106a85a61cbf499c4c08130392b922a45f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593575"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>支援 Azure 庫伯奈斯服務的策略

本文詳細介紹了 Azure 庫伯奈斯服務 （AKS） 的技術支援策略和限制。 本文還詳細介紹了輔助節點管理、託管控制平面元件、協力廠商開源元件以及安全或修補程式管理。

## <a name="service-updates-and-releases"></a>服務更新和發佈

* 有關發佈資訊，請參閱[AKS 版本資訊](https://github.com/Azure/AKS/releases)。
* 有關預覽版要素的資訊，請參閱[AKS 預覽功能和相關專案](https://github.com/Azure/AKS/blob/master/previews.md)。

## <a name="managed-features-in-aks"></a>AKS 中的託管功能

將基礎結構作為服務 （IaaS） 雲元件（如計算或網路元件）允許使用者訪問低級控制項和自訂選項。 相比之下，AKS 提供了交鑰匙 Kubernetes 部署，為客戶提供所需的通用配置和功能集。 AKS 客戶具有有限的自訂、部署和其他選項。 這些客戶無需直接擔心或管理庫伯奈斯集群。

使用 AKS，客戶獲得完全託管*的控制平面*。 控制平面包含客戶操作所需的所有元件和服務，並將 Kubernetes 群集提供給最終使用者。 所有Kubernetes元件都由微軟維護和操作。

Microsoft 通過控制窗格管理和監視以下元件：

* 庫貝萊特或庫伯奈斯 API 伺服器
* 蝕刻或相容的金鑰值存儲，提供服務品質 （QoS）、可擴充性和運行時
* DNS 服務（例如，kube dns 或 CoreDNS）
* 庫伯內斯代理或網路

AKS 不是完全託管的群集解決方案。 某些元件（如輔助節點）具有*共用責任*，使用者必須説明維護 AKS 群集。 例如，需要使用者輸入才能應用輔助節點作業系統 （OS） 安全修補程式。

這些服務*的管理*方式是 Microsoft 和 AKS 團隊部署、操作並負責服務可用性和功能。 客戶無法更改這些託管元件。 Microsoft 限制自訂，以確保使用者體驗一致且可擴展。 有關完全可自訂的解決方案，請參閱[AKS 引擎](https://github.com/Azure/aks-engine)。

> [!NOTE]
> AKS 輔助節點作為常規 Azure IaaS 資源顯示在 Azure 門戶中。 但是，這些虛擬機器被部署到自訂 Azure 資源組（預綴在\\MC *中）。 可以更改 AKS 輔助節點。 例如，可以使用安全外殼 （SSH） 更改 AKS 工作節點的方式更改正常虛擬機器（但是，您無法更改基本作業系統映射，並且更改可能不會通過更新或重新開機持久存在），並且可以將其他 Azure 資源附加到 AKS輔助節點。 但是，當您*從頻段管理和自訂中*進行更改時，AKS 群集可能會變得不可支援。 除非 Microsoft 支援部門指示您進行更改，否則應避免更改輔助節點。

按上述定義發出不受支援的操作（如所有代理節點的帶外處理）會使群集不受支援。 AKS 保留存檔已配置超出支援準則的控制平面的權利，這些控制平面的長時間為 30 天或超過 30 天。 AKS 維護群集蝕刻中繼資料的備份，並可以輕鬆地重新分配群集。 此重新分配可以通過任何 PUT 操作啟動，使群集恢復支援，例如升級或擴展到活動代理節點。

## <a name="shared-responsibility"></a>共同責任

創建群集時，客戶定義 AKS 創建的 Kubernetes 輔助節點。 客戶工作負載會在這些節點上執行。 客戶擁有並可以查看或修改輔助節點。

由於客戶的叢集節點會執行私用程式碼並儲存敏感性資料，因此 Microsoft 支援服務只能以有限的方式進行存取。 Microsoft 支援服務無法登入這些節點、在其中執行命令，或在客戶沒有快速允許或協助的情況下，檢視這些節點的記錄。

由於輔助節點是敏感的，Microsoft 會非常小心限制其後台管理。 在許多情況下，即使 Kubernetes 主節點（etc）和其他 Microsoft 管理的元件發生故障，您的工作負載仍將繼續運行。 不小心修改的工作節點可能會導致資料和工作負載的損失，並可能導致群集不可支援。

## <a name="aks-support-coverage"></a>AKS 支援範圍

Microsoft 可針對下列問題提供技術支援：

* 連接到 Kubernetes 服務提供和支援的所有 Kubernetes 元件，如 API 伺服器。
* Kubernets 的管理、正常執行時間、QoS 和操作控制平面服務（例如，庫伯奈斯主節點、API 伺服器等等和 kube-dns）。
* 蝕刻。 支援包括每 30 分鐘自動、透明地備份所有蝕刻資料，以便進行災難規劃和群集狀態恢復。 這些備份不能直接提供給客戶或使用者。 它們可確保資料的可靠性和一致性。
* 庫伯奈斯 Azure 雲提供程式驅動程式中的任何集成點。 其中包括集成到其他 Azure 服務，如負載等化器、持久卷或網路（庫伯內特和 Azure CNI）。
* 有關自訂控制平面元件的問題或問題，如 Kubernetes API 伺服器等，以及 kube-dns。
* 有關網路的問題，例如 Azure CNI、kubenet 或其他網路訪問和功能問題。 問題可能包括 DNS 解析、資料包丟失、路由等。 Microsoft 支援各種網路方案：
  * 群集和相關元件中的 Kubenet（基本）和高級網路 （Azure CNI）
  * 連接到其他 Azure 服務和應用程式
  * 入口控制器和入口或負載等化器配置
  * 網路性能和延遲

Microsoft 不為以下提供技術支援：

* 有關如何使用 Kubernetes 的問題。 例如，Microsoft 支援服務不會建議您如何建立自訂輸入控制器、使用應用程式工作負載，或套用第三方或開放原始碼軟體套件或工具。
  > [!NOTE]
  > Microsoft 支援部門可以就 AKS 群集功能、自訂和調優（例如，庫伯內斯操作問題和程式）提供建議。
* 協力廠商開源專案，這些專案不是作為 Kubernets 控制平面的一部分提供，或者與 AKS 群集一起部署。 這些專案可能包括伊西歐、赫爾姆、特使或其他專案。
  > [!NOTE]
  > 微軟可以為協力廠商開源專案（如赫爾姆和庫勒德）提供盡力而為的支援。 如果協力廠商開源工具與 Kubernetes Azure 雲供應商或其他特定于 AKS 的錯誤 Bug 集成，Microsoft 支援 Microsoft 文檔中的示例和應用程式。
* 協力廠商閉源軟體。 此軟體可以包含安全性掃描工具和網路裝置或軟體。
* 建置多重雲端或多重廠商的相關問題。 例如，Microsoft 不支援與執行同盟多重公用雲端廠商解決方案相關的問題。
* 除了[AKS 文檔中](https://docs.microsoft.com/azure/aks/)列出的自訂之外，網路自訂項。
  > [!NOTE]
  > Microsoft 確實支援與網路安全性群組 （NSG） 相關的問題和 Bug。 例如，Microsoft 支援可以回答有關 NSG 未能更新或意外的 NSG 或負載等化器行為的問題。

## <a name="aks-support-coverage-for-worker-nodes"></a>輔助節點的 AKS 支援範圍

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>微軟對 AKS 輔助節點的責任

Microsoft 和客戶共同負責 Kubernetes 工作節點，其中：

* 基本作業系統映射需要添加（如監視和網路代理）。
* 輔助節點自動接收作業系統修補程式。
* Kubernetes 控制在輔助節點上運行的平面元件的問題將自動修復。 元件包括以下內容：
  * 庫貝代理
  * 提供與庫伯奈斯主元件的通訊路徑的網路隧道
  * Kubelet
  * 碼頭或莫比守護神

> [!NOTE]
> 在輔助節點上，如果控制平面元件不工作，AKS 團隊可能需要重新開機單個元件或整個輔助節點。 這些重新開機操作是自動化的，並為常見問題提供自動修復。 除非存在緊急維護或中斷，否則這些重新開機僅在_節點_級別進行，而不是群集。

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>AKS 工作節點的客戶責任

Microsoft 不會自動重新開機輔助節點以應用作業系統級修補程式。 儘管 OS 修補程式已傳遞到輔助節點，*但客戶*負責重新開機輔助節點以應用更改。 共用庫、守護進程（如固態混合磁碟機 （SSHD））以及系統或作業系統級別的其他元件將自動修補。

客戶負責執行庫伯內斯升級。 它們可以通過 Azure 控制台或 Azure CLI 執行升級。 這適用于包含對 Kubernetes 的安全性或功能改進的更新。

> [!NOTE]
> 由於 AKS 是*託管服務*，因此其最終目標包括取消修補程式、更新和日誌收集的責任，以使服務管理更加完整和實際操作。 隨著服務的端到端管理能力增加，將來的版本可能會忽略某些功能（例如，節點重新開機和自動修補）。

### <a name="security-issues-and-patching"></a>安全性問題和修補

如果在 AKS 的一個或多個元件中發現安全性漏洞，AKS 團隊將修補所有受影響的群集以緩解此問題。 或者，Microsoft 的小組也會向使用者提供升級指引。

對於安全性漏洞影響的工作人員節點，如果零停機時間修補程式可用，AKS 團隊將應用該修補程式並通知使用者更改。

當安全修補程式需要重新開機輔助節點時，Microsoft 將通知客戶此要求。 客戶負責重新開機或更新以獲取群集修補程式。 如果使用者未根據 AKS 指南應用修補程式，則其群集將繼續容易受到安全問題的影響。

### <a name="node-maintenance-and-access"></a>節點維護和訪問

輔助節點是共同的責任，由客戶擁有。 因此，客戶能夠登錄到其工作節點並進行潛在的有害更改，如內核更新和安裝或刪除包。

如果客戶進行破壞性更改或導致控制平面元件離線或變得無法正常工作，AKS 將檢測到此故障，並自動將輔助節點還原到以前的工作狀態。

儘管客戶可以登錄到和更改輔助節點，但建議這樣做，因為更改會使群集不可支援。

## <a name="network-ports-access-and-nsgs"></a>網路埠、訪問和 NSG

作為託管服務，AKS 具有特定的網路和連接要求。 這些要求不如普通 IaaS 元件的要求靈活。 在 AKS 中，自訂 NSG 規則、阻止特定埠（例如，使用阻止出站埠 443 的防火牆規則）和白名單 URL 等操作會使群集無法支援。

> [!NOTE]
> 目前，AKS 不允許完全鎖定群集的傳出流量。 要控制 URL 和埠的清單，群集可用於出站流量，請參閱[限制出口流量](limit-egress-traffic.md)。

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>不支援的 Alpha 和 Beta 庫伯奈斯功能

AKS 僅支援上游庫伯奈斯專案中的穩定功能。 除非另有檔，AKS 不支援上游 Kubernetes 專案中可用的 Alpha 和 Beta 功能。

在兩種情況下，Alpha 或 Beta 功能可能在通常可用之前推出：

* 客戶已經遇到 AKS 產品、支援或工程團隊，並被要求試用這些新功能。
* 這些功能[已啟用要素標誌](https://github.com/Azure/AKS/blob/master/previews.md)。 客戶必須明確選擇使用這些功能。

## <a name="preview-features-or-feature-flags"></a>預覽功能或功能標誌

對於需要擴展測試和使用者回饋的功能，Microsoft 會發佈功能標誌後面的新的預覽功能或功能。 請將這些功能視為發行前版本或搶鮮版 (Beta) 功能。

預覽功能或功能旗標功能並非設計來用於生產環境。 API 和行為的持續更改、錯誤修復和其他更改可能會導致群集不穩定和停機。

公共預覽版中的功能屬於"盡力而為"支援，因為這些功能處於預覽狀態，不用於生產，並且僅在工作時間由 AKS 技術支援小組提供支援。 有關其他資訊，請參閱：

* [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> 預覽功能在 Azure*訂閱*級別生效。 不要在生產訂閱上安裝預覽功能。 在生產訂閱上，預覽功能可以更改預設 API 行為並影響常規操作。

## <a name="upstream-bugs-and-issues"></a>上游錯誤和問題

鑒於上游Kubernetes專案的發展速度，錯誤總是會出現。 其中一些 Bug 無法在 AKS 系統中修補或解決。 相反，Bug 修復需要對上游專案（如 Kubernetes、節點或輔助作業系統和內核）進行更大的修補程式。 對於 Microsoft 擁有的元件（如 Azure 雲提供程式），AKS 和 Azure 人員致力於修復社區上游的問題。

當技術支援問題由一個或多個上游 Bug 引起的根本原因時，AKS 支援和工程團隊將：

* 使用任何支援詳細資訊標識和連結上游 Bug，以説明解釋此問題影響群集或工作負載的原因。 客戶將收到指向所需存儲庫的連結，以便他們可以查看問題，並查看新版本何時提供修補程式。
* 提供潛在的解決方法或緩解措施。 如果可以緩解此問題，將在 AKS 存儲庫中提交[已知問題](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)。 已知問題檔解釋：
  * 此問題，包括指向上游 Bug 的連結。
  * 有關解決方案升級或其他持久性的解決方法和詳細資訊。
  * 基於上游發佈節奏的問題的包含時間軸。
