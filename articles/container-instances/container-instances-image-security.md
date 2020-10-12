---
title: 容器實例的安全性考慮
description: 針對 Azure 容器實例保護映射與秘密的建議，以及任何容器平臺的一般安全性考慮
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: f49f115e10326887cf4d23406437467256b7df2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922229"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure 容器實例的安全性考慮

本文介紹使用 Azure 容器實例來執行容器應用程式的安全性考慮。 主題包括：

> [!div class="checklist"]
> * 管理 Azure 容器實例映射和秘密的**安全性建議**
> * 整個容器生命週期的**容器生態系統考慮**，適用于任何容器平臺

如需可協助您改善部署安全性狀態的完整建議，請參閱 [容器實例的 Azure 安全性基準](security-baseline.md)。


## <a name="security-recommendations-for-azure-container-instances"></a>Azure 容器實例的安全性建議

### <a name="use-a-private-registry"></a>使用私人登錄

容器是透過一或多個存放庫中所儲存的映像來建立的。 這些存放庫可以屬於公用登錄，例如 [Docker Hub](https://hub.docker.com)或私用登錄。 私人登錄的範例是 [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/)，它可以安裝在內部部署環境或虛擬私人雲端中。 您也可以使用雲端式私用容器登錄服務，包括 [Azure Container Registry](../container-registry/container-registry-intro.md)。 

公開可用的容器映射不保證安全性。 容器映射包含多個軟體層級，而每個軟體層級可能會有弱點。 為了協助降低攻擊的威脅，您應該儲存和取出私人登錄中的映射，例如 Azure Container Registry 或 Docker 受信任的登錄。 除了提供受控的私人登錄之外，Azure Container Registry 可透過 Azure Active Directory 進行基本驗證流程來支援以 [服務主體為基礎的驗證](../container-registry/container-registry-authentication.md) 。 這種驗證封裝括以角色為基礎的唯讀存取 (提取) 、寫入 (推入) ，以及其他許可權。

### <a name="monitor-and-scan-container-images"></a>監視和掃描容器映射

利用解決方案來掃描私人登錄中的容器映射，並找出潛在的弱點。 請務必瞭解不同解決方案所提供的威脅偵測深度。

例如，Azure Container Registry 選擇性地 [與 Azure 資訊安全中心整合](../security-center/azure-container-registry-integration.md) ，以自動掃描推送至登錄的所有 Linux 映射。 Azure 資訊安全中心的整合式 Qualys 掃描器會偵測映射弱點、將其分類，並提供補救指導方針。

安全性監視和映射掃描解決方案（例如 [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) 和青色 [安全性](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) ）也可透過 Azure Marketplace 取得。  

### <a name="protect-credentials"></a>保護認證

容器可以分散到多個叢集和 Azure 區域。 因此，您必須保護登入或 API 存取（例如密碼或權杖）所需的認證。 確定只有特殊許可權的使用者可以存取傳輸中和待用的容器。 清查所有認證秘密，然後要求開發人員使用針對容器平臺所設計的新興秘密管理工具。  請確定您的解決方案包含加密的資料庫、傳輸中秘密資料的 TLS 加密，以及最小許可權的 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md)。 [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) 是一種雲端服務，可保護加密金鑰和秘密 (，例如容器化應用程式) 的憑證、連接字串和密碼。 因為這項資料是敏感且商務關鍵的，所以安全地存取您的金鑰保存庫，讓只有經過授權的應用程式和使用者可以存取它們。

## <a name="considerations-for-the-container-ecosystem"></a>容器生態系統的考慮

下列安全性措施（有效地實行和管理）可協助您保護容器生態系統的安全。 這些量值適用于整個容器生命週期、從開發到生產環境部署，以及一系列的容器協調器、主機和平臺。 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>使用弱點管理作為容器開發生命週期的一部分 

藉由在整個容器開發生命週期中使用有效的弱點管理，您可以改善在安全性問題成為更嚴重的問題之前，識別並解決安全性問題的機率。 

### <a name="scan-for-vulnerabilities"></a>掃描弱點 

新的弱點隨時都會發現，因此掃描和識別弱點是連續的程式。 跨容器生命週期併入弱點掃描：

* 作為開發管線的最後一項檢查，您應該先在容器上執行弱點掃描，再將映射推送至公用或私人登錄。 
* 繼續掃描登錄中的容器映射，以找出任何在開發過程中遺失的瑕疵，以及解決容器映射中所使用之程式碼中可能存在的任何新發現弱點。  

### <a name="map-image-vulnerabilities-to-running-containers"></a>將映射弱點對應至執行中的容器 

您需要將容器映射中所識別的弱點對應至執行中的容器，因此可以減輕或解決安全性問題。  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>確定您的環境中只使用已核准的影像 

容器生態系統中有足夠的變更和變動性，也不允許有未知的容器。 只允許核准的容器映射。 提供工具和程式來監視和防止使用未核准的容器映射。 

減少受攻擊面以及防止開發人員造成重大安全性錯誤的有效方式，就是控制容器映射進入開發環境的流程。 例如，您可能會批准單一的 Linux 散發套件作為基礎映射，最好是精簡 (Alpine 或 CoreOS，而不是 Ubuntu) ，以將潛在攻擊的介面降至最低。 

映射簽署或指紋可提供一鏈監管，讓您能夠驗證容器的完整性。 例如，Azure Container Registry 支援 Docker 的 [內容信任](https://docs.docker.com/engine/security/trust/content_trust) 模型，可讓映射發行者簽署推送至登錄的映射，而映射取用者則只提取已簽署的映射。

### <a name="permit-only-approved-registries"></a>只允許核准的登錄 

確保您的環境只使用核准的映射的延伸模組，只允許使用已核准的容器登錄。 需要使用已核准的容器登錄，藉由限制引入未知弱點或安全性問題的可能性，來降低暴露風險的風險。 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>確保整個生命週期中映射的完整性 

在整個容器生命週期中管理安全性的一部分，是確保登錄中容器映射的完整性，以及將它們修改或部署到生產環境。 

* 具有弱點的映射（甚至是次要）不應允許在生產環境中執行。 在理想情況下，部署在生產環境中的所有映射都應儲存在可供選取的部分存取的私人登錄中。 讓生產映射的數目維持在最小，以確保可以有效地管理它們。

* 因為很難從公開可用的容器映射中找出軟體的來源，所以請從來源建立映射，以確保該圖層的來源知道。 當自行建置的容器映像出現弱點，客戶可以很快地找到解決途徑。 使用公用映射時，客戶需要找出公用映射的根目錄來修正它，或從發行者取得另一個安全的映射。 

* 在生產環境中部署的全面掃描映射不保證會在應用程式的存留期內保持最新狀態。 針對先前未知的映像層或在部署生產環境後才導入的映像層，系統可能會報告它們有安全性弱點。 

  定期審核在生產環境中部署的映射，以識別已過期或尚未在一段時間內更新的映射。 您可以使用藍色-綠色部署方法及輪流升級機制來更新容器映射，而不需要停機。 您可以使用上一節所述的工具來掃描影像。 

* 使用持續整合 (CI) 管線搭配整合式安全性掃描來建立安全映射，並將其推送至您的私人登錄。 CI 解決方案內建的弱點掃描，可確保通過所有測試的映像都會推送至用來部署生產工作負載的私人登錄。 

  CI 管線失敗可確保易受攻擊的映射不會被推送到用於生產工作負載部署的私人登錄。 如果有大量影像，它也會將影像安全性掃描自動化。 否則，手動稽核映像中是否有安全性弱點會是一件痛苦、冗長又容易出錯的工作。 

### <a name="enforce-least-privileges-in-runtime"></a>在執行時間中強制執行最低許可權 

最低許可權的概念是同樣適用于容器的基本安全性最佳作法。 惡意探索弱點時，通常會讓攻擊者的存取權和許可權等於遭入侵的應用程式或進程。 確定容器以最少的許可權運作，以及完成工作所需的存取權，可降低風險暴露程度。 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>藉由移除不必要的許可權來減少容器的受攻擊面 

您也可以從容器執行時間移除任何未使用或不必要的進程或許可權，以將潛在的攻擊面降至最低。 具有特殊許可權的容器會以 root 的形式執行。 如果惡意使用者或工作負載在具特殊許可權的容器中進行轉義，容器就會在該系統上以 root 的形式執行。

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Preapprove 容器允許存取或執行的檔案和可執行檔 

減少變數或未知數目可協助您維護穩定可靠的環境。 限制容器，使其只能存取或執行 preapproved 或 safelisted 檔案，而可執行檔是一種經過證實的方法，可限制暴露于風險的風險。  

從一開始就能輕鬆地管理安全的安全檔案，是很容易的。 當您瞭解哪些檔案和可執行檔是應用程式正確運作所需的檔案和可執行檔時，可使用安全考慮來提供控制和管理能力。 

安全的安全，不僅可減少受攻擊面，還可以提供異常的基準，並防止「有雜訊的鄰居」和「容器」分組案例的使用案例。 

### <a name="enforce-network-segmentation-on-running-containers"></a>在執行中的容器上強制執行網路分割  

若要協助保護某個子網中的容器免于另一個子網的安全性風險，請在執行中的容器之間維護網路分割 (或 nano 分割) 或隔離。 在需要符合合規性規定的產業中使用容器時，也可能需要維護網路分割。  

例如，[夥伴] 工具的 [綠色](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) 會提供自動化的 nano 分割方式。 綠色：在執行時間中監視容器網路活動。 它會識別與其他容器、服務、IP 位址和公用網際網路之間的所有輸入和輸出網路連接。 Nano 分割會根據受監視的流量自動建立。 

### <a name="monitor-container-activity-and-user-access"></a>監視容器活動和使用者存取 

如同任何 IT 環境，您應該一致地監視容器生態系統的活動和使用者存取權，以快速識別任何可疑或惡意的活動。 Azure 提供容器監視解決方案，包括：

* [容器的 Azure 監視器](../azure-monitor/insights/container-insights-overview.md) 會監視部署至裝載于 AZURE KUBERNETES SERVICE (AKS) 上 Kubernetes 環境的工作負載效能。 適用於容器的 Azure 監視器可藉由透過計量 API 從 Kubernetes 中取得的控制器、節點與容器來收集記憶體與處理器計量，為您提供效能可見度。 

* [Azure 容器監視解決方案](../azure-monitor/insights/containers.md)可協助您在單一位置中查看和管理其他 Docker 和 Windows 容器主機。 例如：

  * 查看顯示與容器搭配使用之命令的詳細審核資訊。 
  * 藉由查看及搜尋集中式記錄，而不需要遠端查看 Docker 或 Windows 主機，來對容器進行疑難排解。  
  * 找出可能有雜訊的容器，並在主機上耗用多餘的資源。
  * 查看容器的集中式 CPU、記憶體、儲存體和網路使用量和效能資訊。  

  解決方案支援容器協調器，包括 Docker Swarm、DC/OS、非受控 Kubernetes、Service Fabric 和 Red Hat OpenShift。 

### <a name="monitor-container-resource-activity"></a>監視容器資源活動 

監視您的資源活動，例如您的容器所存取的檔案、網路和其他資源。 監視資源活動和耗用量適用于效能監視和安全性措施。 

[Azure 監視器](../azure-monitor/overview.md) 藉由允許收集計量、活動記錄和診斷記錄，來啟用 Azure 服務的核心監視功能。 例如，活動記錄會告訴您新資源的建立或修改時間。 

  系統會提供計量，這些計量可提供不同資源 (甚至是虛擬機器內的作業系統) 的效能統計資料。 您可以在 Azure 入口網站中使用其中一個總管檢視這項資料，並且根據這些計量建立警示。 Azure 監視器提供最快的計量管線 (5 分鐘降至 1 分鐘)，因此您應該將它用於講究時效性的警示和通知。 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>記錄所有容器系統管理使用者存取權以進行審核 

維護對容器生態系統的系統管理存取權的精確審核線索，包括您的 Kubernetes 叢集、容器登錄和容器映射。 這些記錄可能是為了進行審核，而且在任何安全性事件之後可作為法庭辨識項的用途。 Azure 解決方案包括：

* [Azure Kubernetes Service 與 Azure 資訊安全中心整合](../security-center/azure-kubernetes-service-integration.md) ，以監視叢集環境的安全性設定並產生安全性建議
* [Azure 容器監視解決方案](../azure-monitor/insights/containers.md)
* [Azure 容器實例](container-instances-log-analytics.md)和[Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md)的資源記錄

## <a name="next-steps"></a>後續步驟

* 如需可協助您改善部署安全性狀態的完整建議，請參閱 [容器實例的 Azure 安全性基準](security-baseline.md) 。

* 深入瞭解如何在容器化環境中使用 [Azure 資訊安全中心](../security-center/container-security.md) 進行即時威脅偵測。

* 深入瞭解如何使用 [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) 和青色 [安全性](https://www.aquasec.com/solutions/azure-container-security/)的解決方案來管理容器弱點。
