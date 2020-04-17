---
title: 容器實例的安全性
description: 保護 Azure 容器實例映像和機密的建議,以及任何容器平臺的一般安全注意事項
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: 87fa28cf9bdb546a5f108284023a9f787645a1fd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457989"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure 容器實例的安全注意事項

本文介紹了使用 Azure 容器實例運行容器應用的安全注意事項。 主題包括：

> [!div class="checklist"]
> * 管理 Azure 容器實體的影像和機密**的安全建議**
> * 容器生態系統在整個容器生命週期中,任何容器平臺的**注意事項**

## <a name="security-recommendations-for-azure-container-instances"></a>Azure 容器實例的安全建議

### <a name="use-a-private-registry"></a>使用私人註冊表

容器是透過一或多個存放庫中所儲存的映像來建立的。 這些儲存庫可以屬於公共註冊表(如[Docker Hub)](https://hub.docker.com)或專用註冊表。 私人登錄的範例是 [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/)，它可以安裝在內部部署環境或虛擬私人雲端中。 您還可以使用基於雲的私有容器註冊表服務,包括[Azure 容器註冊表](../container-registry/container-registry-intro.md)。 

公開可用的容器映像不保證安全性。 容器映射由多個軟體層組成,每個軟體層可能都有漏洞。 為了幫助減少攻擊的威脅,應從專用註冊表(如 Azure 容器註冊表或 Docker 可信註冊表)存儲和檢索映射。 除了提供託管專用註冊表外,Azure 容器註冊表還通過 Azure 活動目錄支援[基於服務主體的身份驗證](../container-registry/container-registry-authentication.md),用於基本身份驗證流。 此身份驗證包括基於角色的唯讀(拉取)、寫入(推送)和其他許可權的訪問。

### <a name="monitor-and-scan-container-images"></a>監視並掃描容器映像

利用解決方案掃描私有註冊表中的容器映射並識別潛在漏洞。 瞭解不同解決方案提供的威脅檢測深度非常重要。

例如,Azure 容器註冊表可以選擇[與 Azure 安全中心集成](../security-center/azure-container-registry-integration.md),以自動掃描推送到註冊表的所有 Linux 映射。 Azure 安全中心的整合 Qualys 掃描程式可檢測圖像漏洞、對其進行分類並提供修正指南。

通過 Azure 應用商店,還提供安全監視和圖像掃描解決方案,如[Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview)和[Aqua Security。](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview)  

### <a name="protect-credentials"></a>保護憑據

容器可以分佈在多個群集和 Azure 區域。 因此,您必須保護登錄或 API 存取所需的認證,如密碼或權杖。 確保只有特權使用者才能訪問這些容器在傳輸和休息。 清點所有憑據機密,然後要求開發人員使用專為容器平臺設計的新興機密管理工具。  確保您的解決方案包括加密資料庫、傳輸中機密資料的 TLS 加密以及基於角色的權限最小的[存取控制](../role-based-access-control/overview.md)。 [Azure 密鑰保管庫](../key-vault/general/secure-your-key-vault.md)是一種雲服務,用於保護容器化應用程式的加密密鑰和機密(如證書、連接字串和密碼)。 由於此數據是敏感且業務關鍵,因此安全訪問密鑰保管庫,以便只有經過授權的應用程式和用戶可以訪問它們。

## <a name="considerations-for-the-container-ecosystem"></a>容器生態系統的注意事項

以下安全措施實施良好,管理有效,可説明您保護容器生態系統。 從開發到生產部署,以及一系列容器協調器、主機和平臺,這些方法在整個容器生命週期中都適用。 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>將漏洞管理用作容器開發生命週期的一部分 

通過在容器開發生命週期中使用有效的漏洞管理,您可以提高在安全問題成為更嚴重的問題之前識別和解決安全問題的可能性。 

### <a name="scan-for-vulnerabilities"></a>掃描漏洞 

新的漏洞會一直被發現,因此掃描和識別漏洞是一個持續的過程。 在整個容器生命週期中整合漏洞掃描:

* 作為開發管道的最後檢查,在將映射推送到公共或私有註冊表之前,應對容器執行漏洞掃描。 
* 繼續掃描註冊表中的容器映射,以識別開發過程中遺漏的任何缺陷,並解決容器映射中使用的代碼中可能存在的任何新發現的漏洞。  

### <a name="map-image-vulnerabilities-to-running-containers"></a>將影像漏洞對應到執行的容器 

您需要有一種將容器映射中識別的漏洞映射到正在運行的容器的方法,以便可以緩解或解決安全問題。  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>確保環境中只使用已批准的影像 

容器生態系統中有足夠的變化和波動性,而不允許未知的容器。 僅允許批准的容器映射。 制定工具和流程,以監視和防止使用未經批准的容器映射。 

減少攻擊面和防止開發人員犯嚴重安全錯誤的有效方法是控制容器映射流入開發環境。 例如,您可以認可單個 Linux 發行版作為基本映像,最好是精益映射(阿爾卑斯或 CoreOS 而不是 Ubuntu),以盡量減少潛在攻擊的表面。 

圖像簽名或指紋識別可以提供保管鏈,使您能夠驗證容器的完整性。 例如,Azure 容器註冊表支援 Docker[的內容信任](https://docs.docker.com/engine/security/trust/content_trust)模型,該模型允許映射發行者對推送到註冊表的圖像進行簽名,並且映像消費者只能提取簽名圖像。

### <a name="permit-only-approved-registries"></a>只允許批准的註冊 

確保環境僅使用已批准映射的擴展是只允許使用已批准的容器註冊表。 要求使用經批准的容器註冊表,通過限制引入未知漏洞或安全問題的可能性,減少風險風險。 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>確保影像在整個生命週期中的完整性 

在整個容器生命週期中管理安全性的一部分是確保註冊表中的容器映射的完整性,以及這些映射被更改或部署到生產中。 

* 不應允許具有漏洞(即使是次要)的圖像在生產環境中運行。 理想情況下,在生產中部署的所有映射都應保存在可供少數部分人訪問的專用註冊表中。 保持生產映射的數量較小,以確保它們能夠得到有效管理。

* 由於很難從公開可用的容器映射中確定軟體的來源,因此從源生成映射以確保瞭解圖層的來源。 當自行建置的容器映像出現弱點，客戶可以很快地找到解決途徑。 使用公共映射時,客戶需要找到公共映射的根部來修復它或從發佈者獲取另一個安全映射。 

* 在生產中部署的徹底掃描映射不能保證在應用程式的生存期內是最新的。 針對先前未知的映像層或在部署生產環境後才導入的映像層，系統可能會報告它們有安全性弱點。 

  定期審核在生產中部署的圖像,以識別已過期或一段時間未更新的圖像。 您可以使用藍綠色部署方法和滾動升級機制來更新容器映射,而不會停機。 您可以使用上一節中描述的工具掃描圖像。 

* 使用具有整合安全掃描的連續整合 (CI) 管道來生成安全映射並將其推送到專用註冊表。 CI 解決方案內建的弱點掃描，可確保通過所有測試的映像都會推送至用來部署生產工作負載的私人登錄。 

  CI 管道故障可確保不會將易受攻擊的映射推送到用於生產工作負載部署的專用註冊表。 如果存在大量圖像,它還可自動進行圖像安全掃描。 否則，手動稽核映像中是否有安全性弱點會是一件痛苦、冗長又容易出錯的工作。 

### <a name="enforce-least-privileges-in-runtime"></a>在執行時強制實施最少的權限 

最小特權的概念是一種基本的安全最佳實踐,也適用於容器。 當漏洞被利用時,它通常給予攻擊者與受攻擊的應用程式或進程相等的許可權和特權。 確保容器以完成工作所需的最低許可權和訪問許可權運行,可降低您面臨的風險。 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>使用不需要的權限來減少容器攻擊面 

還可以通過從容器運行時中刪除任何未使用或不必要的進程或特權來最小化潛在攻擊面。 特權容器以 root 身份運行。 如果惡意使用者或工作負載在特權容器中逃脫,則該容器將作為根運行在該系統上。

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>預先批准允許容器存取或執行的檔案與執行檔 

減少變數或未知數有助於維護穩定、可靠的環境。 限制容器,以便它們只能訪問或運行預先批准或安全列出的檔和可執行檔,這是限制風險暴露的成熟方法。  

當安全清單從一開始就實現時,管理它要容易得多。 安全清單提供控制和可管理性的度量,因為您需要應用程式正常運行所需的檔案和可執行檔。 

安全清單不僅減少了攻擊面,而且還可以為異常提供基線,並防止"嘈雜鄰居"和容器分組方案的用例。 

### <a name="enforce-network-segmentation-on-running-containers"></a>在執行的容器上強制實施網路分段  

為了説明保護一個子網中的容器免受另一個子網中的安全風險,維護網路分段(或納米分段)或正在運行的容器之間的隔離。 在滿足合規性要求所需的行業中使用容器可能還需要維護網路分段。  

例如,合作夥伴工具[Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview)提供了一種用於奈米分割的自動化方法。 Aqua 在運行時監視容器網路活動。 它識別與其他容器、服務、IP 位址和公共 Internet 的所有入站和出站網路連接。 納米分段根據監控流量自動創建。 

### <a name="monitor-container-activity-and-user-access"></a>監視容器活動和使用者存取 

與任何 IT 環境一樣,您應該始終監控活動和使用者對容器生態系統的訪問,以便快速識別任何可疑或惡意活動。 Azure 提供容器監視解決方案,包括:

* [容器的 Azure 監視器](../azure-monitor/insights/container-insights-overview.md)監視部署到 Azure 庫伯奈斯服務 (AKS) 上託管的庫伯奈斯環境的工作負載的性能。 適用於容器的 Azure 監視器可藉由透過計量 API 從 Kubernetes 中取得的控制器、節點與容器來收集記憶體與處理器計量，為您提供效能可見度。 

* [Azure 容器監視解決方案](../azure-monitor/insights/containers.md)可説明您在單個位置查看和管理其他 Docker 和 Windows 容器主機。 例如：

  * 查看顯示與容器一起使用的命令的詳細審核資訊。 
  * 通過查看和搜索集中式日誌來對容器進行故障排除,而無需遠端查看 Docker 或 Windows 主機。  
  * 查找可能嘈雜的容器,並在主機上消耗多餘的資源。
  * 查看容器的集中式 CPU、記憶體、儲存和網路使用方式和性能資訊。  

  該解決方案支援容器協調器,包括 Docker Swarm、DC/OS、非託管庫伯奈斯、服務交換矩陣和紅帽 OpenShift。 

### <a name="monitor-container-resource-activity"></a>監視容器資源活動 

監視資源活動,如容器訪問的文件、網路和其他資源。 監視資源活動和消耗對於性能監視和安全措施都很有用。 

[Azure 監視器](../azure-monitor/overview.md)通過允許收集指標、活動日誌和診斷日誌,支援 Azure 服務的核心監視。 例如，活動記錄會告訴您新資源的建立或修改時間。 

  系統會提供計量，這些計量可提供不同資源 (甚至是虛擬機器內的作業系統) 的效能統計資料。 您可以在 Azure 入口網站中使用其中一個總管檢視這項資料，並且根據這些計量建立警示。 Azure 監視器提供最快的計量管線 (5 分鐘降至 1 分鐘)，因此您應該將它用於講究時效性的警示和通知。 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>紀錄所有容器管理使用者存取權限以進行審核 

維護對容器生態系統的管理訪問的準確審核跟蹤,包括 Kubernetes 群集、容器註冊錶和容器映射。 這些日誌可能對於審核目的是必需的,在任何安全事件后將作為取證證據非常有用。 Azure 解決方案包括:

* [將 Azure 庫伯奈斯服務與 Azure 安全中心整合](../security-center/azure-kubernetes-service-integration.md),以監視群集環境的安全配置並生成安全建議
* [Azure 容器監視解決方案](../azure-monitor/insights/containers.md)
* [Azure 容器實體](container-instances-log-analytics.md)和 Azure[容器註冊表](../container-registry/container-registry-diagnostics-audit-logs.md)的資源紀錄

## <a name="next-steps"></a>後續步驟

* 詳細瞭解在容器化環境中使用[Azure 安全中心](../security-center/container-security.md)進行即時威脅檢測。

* 瞭解有關使用[Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/)和[Aqua 安全](https://www.aquasec.com/solutions/azure-container-security/)解決方案管理容器漏洞的更多詳細資訊。