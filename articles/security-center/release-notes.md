---
title: Azure 資訊安全中心版本資訊
description: Azure 資訊安全中心新增項目和變更內容的相關描述。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b1bd0ff28906f35ddc56afbd0287bbe29879deb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91438943"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 資訊安全中心的新功能

Azure 安全性持續再開發改良。 為了讓您隨時掌握最新的開發訊息，此頁面提供下列相關資訊：

- 新功能
- 錯誤修正
- 已被取代的功能

此頁面會定期更新，因此請時常瀏覽。 如果想要尋找超過 6 個月的項目，請前往[Azure 資訊安全中心內新功能的封存](release-notes-archive.md)。


## <a name="september-2020"></a>2020 年 9 月

9 月的更新包括：
- [全新的資訊安全中心外觀！](#security-center-gets-a-new-look)
- [已發行 Azure Defender](#azure-defender-released)
- [適用於 Key Vault 的 Azure Defender 已正式推出](#azure-defender-for-key-vault-is-generally-available)
- [適用於儲存體的 Azure Defender 保護已可正式用於檔案儲存體和 ADLS Gen2](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [資產清查工具現已正式推出](#asset-inventory-tools-are-now-generally-available)
- [停用容器登錄和虛擬機器掃描的特定弱點結果](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [讓資源豁免建議](#exempt-a-resource-from-a-recommendation)
- [資訊安全中心內的 AWS 和 GCP 連接器引入多雲端體驗](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Kubernetes 工作負載保護建議組合](#kubernetes-workload-protection-recommendation-bundle)
- [在適用於 IoT 的 Azure Defender 中增強 IoT 威脅防護功能](#iot-threat-protection-enhancements-in-azure-defender-for-iot)
- [連續匯出現在支援弱點評量結果](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [在建立新資源時強制執行建議，以防止安全性設定錯誤](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [已改善網路安全性群組建議](#network-security-group-recommendations-improved)
- [已淘汰預覽 AKS 建議：「Pod 安全性原則應定義於 Kubernetes Services 中」](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [已改善Azure 資訊安全中心的電子郵件通知](#email-notifications-from-azure-security-center-improved)
- [安全分數不包含預覽建議](#secure-score-doesnt-include-preview-recommendations)
- [建議現在包含嚴重性指標和時效性間隔](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>全新的資訊安全中心外觀！

我們已全面更新資訊安全中心入口網站頁面的 UI。 新頁面包含新的概觀頁面，以及安全分數、資產清查和 Azure Defender 的儀表板。

現在，在重新設計的概觀頁面上，有一個可用來存取安全分數、資產清查和 Azure Defender 儀表板的磚。 還有一個連結至法規合規性儀表板的磚。

深入了解[概觀頁面](overview-page.md)。


### <a name="azure-defender-released"></a>已發行 Azure Defender

**Azure Defender** 是整合於資訊安全中心的雲端工作負載保護平台 (CWPP)，可為您的 Azure 和混合式工作負載提供進階的智慧型保護功能。 其會取代資訊安全中心的標準定價層選項。 

當您從 Azure 資訊安全中心的 [定價和設定] 區域啟用 Azure Defender 時，下列 Defender 方案會同時啟用，並為您環境的計算、資料和服務層提供全面的防護：

- [適用於伺服器的 Azure Defender](defender-for-servers-introduction.md)
- [適用於 App Service 的 Azure Defender](defender-for-app-service-introduction.md)
- [適用於儲存體的 Azure Defender](defender-for-storage-introduction.md)
- [適用於 SQL 的 Azure Defender](defender-for-sql-introduction.md)
- [適用於 IoT 的 Azure Defender](defender-for-iot-introduction.md)
- [適用於 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)
- [適用於 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)
- [適用於容器登錄的 Azure Defender](defender-for-container-registries-introduction.md)

這些方案會分別在資訊安全中心文件中說明。

Azure Defender 會透過其專用儀表板，為虛擬機器、SQL 資料庫、容器、Web 應用程式及網路等項目提供安全性警示和進階威脅防護。

[深入了解 Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>適用於 Key Vault 的 Azure Defender 已正式推出

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 

**適用於 Key Vault 的 Azure Defender** 提供適用於 Azure Key Vault 的 Azure 原生進階威脅防護，可額外提供多一層的安全情報。 透過擴充，適用於 Key Vault 的 Azure Defender 可保護許多與您 Key Vault 帳戶相依的資源。

選擇性方案現已正式推出。 這項功能在預覽版時稱為「適用於 Azure Key Vault 的進階威脅防護」。

此外，Azure 入口網站中的 Key Vault 頁面現在包含專屬的**安全性**頁面，用以提供**資訊安全中心**的建議和警示。

請在＜[適用於 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)＞中深入了解。


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>適用於儲存體的 Azure Defender 保護已可正式用於檔案儲存體和 ADLS Gen2 

**適用於儲存體的 Azure Defender** 會偵測您 Azure 儲存體帳戶上可能有害的活動。 不論您的資料儲存為 Blob 容器、檔案共用或資料湖，皆可受到保護。

現已正式提供 [Azure 檔案儲存體](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)和 [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) 的支援。

從 2020 年 10 月 1 日起，我們將開始收取保護這些服務資源的費用。

請在＜[適用於儲存體的 Azure Defender](defender-for-storage-introduction.md)＞中深入了解。


### <a name="asset-inventory-tools-are-now-generally-available"></a>資產清查工具現已正式推出

Azure 資訊安全中心的資產清查頁面會提供單一頁面，讓您檢視已連線至資訊安全中心的資源有何安全性狀態。

資訊安全中心會定期分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 然後為您提供如何補救這些弱點的建議。

只要任何資源有未處理的建議，這些建議就會出現在清查中。

請在＜[使用資產清查和管理工具探索及管理資源](asset-inventory.md)＞中深入了解。



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>停用容器登錄和虛擬機器掃描的特定弱點結果

Azure Defender 包含弱點掃描程式，可掃描您 Azure Container Registry 和虛擬機器中的映像。

如果您的組織需要忽略某個結果，而不是將其修復，您可以選擇性地停用該結果。 停用的結果不會影響您的安全分數或產生不想要的雜訊。

當某個結果符合停用規則中定義的準則時，其就不會出現在結果清單中。

此選項位在建議詳細資料頁面：

- **應補救 Azure Container Registry 映像中的弱點**
- **應補救虛擬機器中的弱點**

請在＜[停用容器映像的特定結果](defender-for-container-registries-usage.md#disable-specific-findings-preview)＞和＜[停用虛擬機器的特定結果](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)＞中深入了解。


### <a name="exempt-a-resource-from-a-recommendation"></a>讓資源豁免建議

有時候，資源會因為特定建議而列為狀況不良 (因此降低您的安全分數)，但您認為該資源並非狀況不良。 其可能已由資訊安全中心未追蹤的程序進行補救。 或者您的組織可能已決定接受該特定資源的風險。 

在這種情況下，您可以建立豁免規則，並確保該資源未來不會列在狀況不良的資源中。 這些規則可以包含下列所述的已記載理由。

請在＜[從建議與安全分數中豁免資源](exempt-resource.md)＞中深入了解。


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>資訊安全中心內的 AWS 和 GCP 連接器引入多雲端體驗

由於雲端工作負載通常需要跨越多個雲端平台，因此雲端安全性服務必須執行相同動作。

Azure 資訊安全中心現在會保護 Azure、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 中的工作負載。

將您的 AWS 和 GCP 帳戶上架到資訊安全中心，即可整合 AWS 安全性中樞、GCP 安全性命令和 Azure 資訊安全中心。 

請在＜[將您的 AWS 帳戶連線到 Azure 資訊安全中心](quickstart-onboard-aws.md)＞及＜[將您的 GCP 帳戶連線到 Azure 資訊安全中心](quickstart-onboard-gcp.md)＞中深入了解。


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Kubernetes 工作負載保護建議組合

為了確保預設的 Kubernetes 工作負載是安全的，資訊安全中心會新增 Kubernetes 層級的強化建議，包括 Kubernetes 許可控制的強制選項。

如果您已在 AKS 叢集上安裝適用於 Kubernetes 的 Azure 原則附加元件，則針對 Kubernetes API 伺服器提出的每個要求都會依據預先定義的最佳做法來加以監視，然後再保存到叢集。 接著，您可以進行設定以強制執行最佳做法，並要求未來的工作負載遵循。

例如，您可以要求不得建立具特殊權限的容器，而且未來要求這麼做的任何要求都會遭到封鎖。

請在＜[使用 Kubernetes 許可控制保護工作負載的最佳做法](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)＞中深入了解。


### <a name="iot-threat-protection-enhancements-in-azure-defender-for-iot"></a>在適用於 IoT 的 Azure Defender 中增強 IoT 威脅防護功能

由於適用於 IoT 的 Azure Defender 包含 CyberX 無代理程式技術，所以帶來了更多威脅防護功能。 這會為營運技術 (OT) 環境中使用的非受控棕色地帶裝置帶來安全性保護，這些環境包括製造、建築物管理系統 (BMS)、生命科學、能源與水務公用事業、石油與天然氣，以及物流。

請在＜[適用於 IoT 的 Azure Defender 簡介](defender-for-iot-introduction.md)＞中深入了解。


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>連續匯出現在支援弱點評量結果

使用連續匯出將您的警示和建議即時串流至 Azure 事件中樞、Log Analytics 工作區或 Azure 監視器。 然後您可以在該處整合此資料與 SIEM (例如 Azure Sentinel、Power BI、Azure 資料總管等等)。

資訊安全中心的整合式弱點評量工具會傳回您資源的相關結果，以作為「父系」建議中可採取動作的建議，例如「應補救虛擬機器中的弱點」。 

現在，當您選取建議並啟用 [包含安全性結果] 選項時，即可透過連續匯出來匯出安全性結果。

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="在連續匯出設定中包含安全性結果切換" :::

相關頁面：

- [資訊安全中心的 Azure 虛擬機器整合式弱點評量解決方案](deploy-vulnerability-assessment-vm.md)
- [資訊安全中心的 Azure Container Registry 映像整合式弱點評量解決方案](defender-for-container-registries-usage.md)
- [連續匯出](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>在建立新資源時強制執行建議，以防止安全性設定錯誤

錯誤的安全性設定是安全性事件的主要原因。 資訊安全中心現在可以協助「避免」因特定建議而造成的新資源設定錯誤。 

這項功能可協助保護您工作負載的安全，並穩定您的安全分數。

根據特定建議來強制執行安全設定的模式有兩種：

- 使用 Azure 原則的 **Deny** 效果，以防止建立狀況不良的資源

- 使用 [強制執行] 選項，以利用 Azure 原則的 **DeployIfNotExist** 效果，以及在建立資源時自動補救不符合規範的資源
 
這可在選取的安全性建議上取得，也可以在資源詳細資料頁面的頂端找到。

請在＜[防止強制/拒絕建議的設定錯誤](prevent-misconfigurations.md)＞中深入了解。

###  <a name="network-security-group-recommendations-improved"></a>已改善網路安全性群組建議

下列與網路安全性群組相關的安全性建議已經過改良，可減少一些誤判實例。

- 在與您 VM 建立關聯的 NSG 上應限制所有網路連接埠
- 應關閉虛擬機器上的管理連接埠
- 應使用網路安全性群組保護網際網路對應的虛擬機器
- 子網路應該與網路安全性群組建立關聯


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>已淘汰預覽 AKS 建議：「Pod 安全性原則應定義於 Kubernetes Services 中」

預覽建議「Pod 安全性原則應定義於 Kubernetes Services 中」已淘汰，如 [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/use-pod-security-policies) 文件中所述。

Pod 安全性原則 (預覽) 功能即將淘汰，2020 年 10 月 15 日之後將不再可供使用，請改為使用適用於 AKS 的 Azure 原則。

淘汰 Pod 安全性原則 (預覽) 之後，您必須在任何使用已淘汰功能的現有叢集上停用此功能，以執行未來的叢集升級並保留在 Azure 支援的範圍中。


### <a name="email-notifications-from-azure-security-center-improved"></a>已改善Azure 資訊安全中心的電子郵件通知

關於安全性警示的下列電子郵件區域已獲得改善： 

- 已針對所有嚴重性層級的警示，新增傳送電子郵件通知的功能
- 已新增在訂用帳戶上對不同 RBAC 角色的使用者發出通知的功能
- 根據預設，針對高嚴重性的警示 (其成為真正缺口的機率很高)，我們會主動通知訂用帳戶擁有者
- 我們已從電子郵件通知設定頁面移除 [電話號碼] 欄位

請在＜[設定安全性警示的電子郵件通知](security-center-provide-security-contact-details.md)＞中深入了解。


### <a name="secure-score-doesnt-include-preview-recommendations"></a>安全分數不包含預覽建議 

資訊安全中心會持續評估資源、訂用帳戶、組織的安全性問題。 然後將所有的發現彙總成一個分數，讓您可以立即得知目前的安全性情況：分數越高，風險層級越低。

探索到新威脅時，資訊安全中心會透過新建議來提供新的安全性意見。 為了避免意外變更您的安全分數，以及提供可讓您在新建議影響到分數之前先進行探索的寬限期，標示為**預覽**的建議已不再包含於您的安全分數計算中。 這些建議仍會盡可能進行補救，以在預覽期間結束時計入您的分數。

此外，**預覽**建議不會將資源轉譯為「狀況不良」。

預覽建議的範例：

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="在連續匯出設定中包含安全性結果切換":::

[深入了解安全分數](secure-score-security-controls.md)。


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>建議現在包含嚴重性指標和時效性間隔

建議的詳細資料頁面現在包含時效性間隔指標 (如果適用)，並清楚顯示建議的嚴重性。

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="在連續匯出設定中包含安全性結果切換":::



## <a name="august-2020"></a>2020 年 8 月

8 月的更新包括：

- [資產清查 - 資產安全性狀態的強大新觀點](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [已新增 Azure Active Directory 安全性預設值的支援 (適用於多重要素驗證)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [已新增服務主體建議](#service-principals-recommendation-added)
- [VM 上的弱點評量 - 合併的建議和原則](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [新增至 ASC_default 方案的新 AKS 安全性原則 - 僅供個人預覽版的客戶使用](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>資產清查 - 資產安全性狀態的強大新觀點

資訊安全中心的資產清查 (目前為預覽狀態) 可針對已連線至資訊安全中心的資源，提供檢視其安全性狀態的方式。

資訊安全中心會定期分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 然後為您提供如何補救這些弱點的建議。 只要任何資源有未處理的建議，這些建議就會出現在清查中。

您可以使用此檢視及其篩選器來瀏覽您的安全性狀態資料，並根據您的結果採取進一步的動作。

在＜[資產清查](asset-inventory.md)＞中深入了解。


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>已新增 Azure Active Directory 安全性預設值的支援 (適用於多重要素驗證)

資訊安全中心已新增[安全性預設值](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)的完整支援，這是免費的 Microsoft 身分識別安全性保護。

安全性預設值提供預先設定的身分識別安全性設定，可防止您的組織遭受一般身分識別相關的攻擊。 總體來說，安全性預設值已保護超過 500 萬個租用戶，而 50,000 個租用戶還會受到資訊安全中心的保護。

資訊安全中心現在會在發現 Azure 訂用帳戶未啟用安全性預設值時，提供安全性建議。 到目前為止，資訊安全中心都建議您使用條件式存取來啟用多重要素驗證，這是 Azure Active Directory (AD) 進階授權的一部分。 我們現在建議使用免費 Azure AD 的客戶，也啟用安全性預設值。 

我們的目標是鼓勵更多客戶使用 MFA 保護其雲端環境，並降低最嚴重且高度影響您[安全分數](secure-score-security-controls.md)的其中一個風險。

深入了解[安全性預設值](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)。


### <a name="service-principals-recommendation-added"></a>已新增服務主體建議

已加入新建議，建議使用管理憑證來管理其訂用帳戶的資訊安全中心客戶切換到服務主體。

「**應使用服務主體來保護訂用帳戶，而不是使用管理憑證**」的建議會勸您使用服務主體或 Azure Resource Manager，以更安全地管理您的訂用帳戶。 

深入了解 [Azure Active Directory 中的應用程式和服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>VM 上的弱點評量 - 合併的建議和原則

資訊安全中心會檢查您的 VM，偵測其是否正在執行弱點評量解決方案。 如果找不到任何弱點評量解決方案，資訊安全中心會提供簡化部署的建議。

發現弱點時，資訊安全中心會提供結果的總結建議，以供您調查並視需要進行補救。

為了確保所有使用者都有一致的體驗，不論他們使用的掃描程式類型為何，我們都已將四個建議整合到下列兩個建議中：

|統一的建議|變更描述|
|----|:----|
|**虛擬機器上應啟用弱點評估解決方案**|取代下列兩項建議：<br> **•** 在虛擬機器上啟用內建弱點評量解決方案 (Qualys 技術支援 (現已淘汰)) (隨附於標準層)<br> **•** 虛擬機器上應該安裝弱點評量解決方案 (現已淘汰) (隨附於標準層和免費層)|
|**應補救虛擬機器中的弱點**|取代下列兩項建議：<br>**•** 補救在您虛擬機器上發現的弱點 (Qualys 技術提供) (現已淘汰)<br>**•** 弱點評量解決方案應補救弱點 (現已淘汰)|
|||

現在您將使用相同的建議，從合作夥伴 (例如 Qualys 或 Rapid7) 部署資訊安全中心的弱點評量擴充功能或私下授權的解決方案 ("BYOL")。

此外，當發現弱點並回報資訊安全中心時，不論發現這些弱點的弱點評量解決方案為何，都會有單一建議會向您發出這些結果的警示。

#### <a name="updating-dependencies"></a>更新相依性

如果您有參考先前建議或原則金鑰/名稱的指令碼、查詢或自動化，請使用下列資料表來更新參考：

##### <a name="before-august-2020"></a>2020 年 8 月之前

|建議|影響範圍|
|----|:----|
|**在虛擬機器上啟用內建弱點評量解決方案 (Qualys 技術支援)**<br>機碼：550e890b-e652-4d22-8274-60b3bdb24c63|內建|
|**補救在您的虛擬機器上發現的弱點 (Qualys 技術提供)**<br>機碼：1195afff-c881-495e-9bc5-1486211ae03f|內建|
|**虛擬機器上應安裝弱點評定解決方案**<br>機碼：01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**弱點評量解決方案應修復弱點**<br>機碼：71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|原則|影響範圍|
|----|:----|
|**虛擬機器上應啟用弱點評定**<br>原則識別碼：501541f7-f7e7-4cd6-868c-4190fdad3ac9|內建|
|**弱點評量解決方案應補救弱點**<br>原則識別碼：760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>從 2020 年 8 月起

|建議|影響範圍|
|----|:----|
|**虛擬機器上應啟用弱點評估解決方案**<br>金鑰：ffff0522-1e88-47fc-8382-2a80ba848f5d|內建 + BYOL|
|**應補救虛擬機器中的弱點**<br>機碼：1195afff-c881-495e-9bc5-1486211ae03f|內建 + BYOL|
||||

|原則|影響範圍|
|----|:----|
|[**虛擬機器上應啟用弱點評量**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>原則識別碼：501541f7-f7e7-4cd6-868c-4190fdad3ac9 |內建 + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>新增至 ASC_default 方案的新 AKS 安全性原則 - 僅供個人預覽版的客戶使用

為了確保預設的 Kubernetes 工作負載是安全的，資訊安全中心會新增 Kubernetes 層級的原則和強化建議，包括 Kubernetes 許可控制的強制選項。

此專案的初期階段包括個人預覽版，以及將新原則 (預設為停用) 新增至 ASC_default 方案。

您可以放心地忽略這些原則，並不會對您的環境造成任何影響。 如果您想要啟用這些原則，請在 https://aka.ms/SecurityPrP 註冊預覽版，然後從下列選項中選取：

1. **單一預覽** – 僅加入此個人預覽版。 明確提及「ASC 連續掃描」作為您想要加入的預覽。
1. **進行中的計畫** – 新增到此個人預覽版及未來的個人預覽版。 您必須完成設定檔和隱私權合約。


## <a name="july-2020"></a>2020 年 7 月

7 月的更新包括：
- [虛擬機器的弱點評量現在可用於非市集映像](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Azure 儲存體的威脅防護已擴充為包含 Azure 檔案儲存體和 Azure Data Lake Storage Gen2 (預覽)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [啟用威脅防護功能的八個新建議](#eight-new-recommendations-to-enable-threat-protection-features)
- [容器安全性改善 - 加快登錄掃描和重新整理文件的速度](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [使用新建議更新自適性應用程式控制和路徑規則中支援萬用字元](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [已淘汰 SQL 進階資料安全性的六個原則](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>虛擬機器的弱點評量現在可用於非市集映像

部署弱點評量解決方案時，資訊安全中心已先執行驗證檢查，然後再進行部署。 檢查是要確認目的地虛擬機器的市集SKU。 

此更新中已移除這項檢查，您現在可以將弱點評量工具部署到「自訂」的 Windows 和 Linux 電腦。 自訂映像是您從市集預設值中修改的映像。

雖然您現在可以在許多電腦上部署整合式弱點評量擴充功能 (由 Qualys 提供技術支援)，但只有當您使用＜[將整合式弱點掃描程式部署到標準層 VM 中](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-vulnerability-scanner-to-your-vms)＞所列的作業系統時，才能受到支援

深入了解[適用於虛擬機器的整合式弱點掃描程式 (需要 Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)。

若要深入了解如何從 Qualys 或 Rapid7 使用您自己的私下授權弱點評量解決方案，請參閱[部署合作夥伴弱點掃描解決方案](deploy-vulnerability-assessment-vm.md)。


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Azure 儲存體的威脅防護已擴充為包含 Azure 檔案儲存體和 Azure Data Lake Storage Gen2 (預覽)

Azure 儲存體的威脅防護會偵測 Azure 儲存體帳戶上可能有害的活動。 資訊安全中心會在偵測到儲存體帳戶上有存取或惡意探索的嘗試時，顯示警示。 

不論您的資料儲存為 Blob 容器、檔案共用或資料湖，皆可受到保護。




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>啟用威脅防護功能的八個新建議

已新增八項新建議，目的是提供簡單的方式來為下列資源類型啟用 Azure 資訊安全中心的威脅防護功能：虛擬機器、App Service 方案、Azure SQL Database 伺服器、機器上的 SQL 伺服器、Azure 儲存體帳戶、Azure Kubernetes Service 叢集、Azure Container Registry 登錄，以及 Azure Key Vault 保存庫。

新建議如下：

- **Azure SQL Database 伺服器應啟用進階資料安全性**
- **應在機器上的 SQL 伺服器啟用進階資料安全性**
- **應在 Azure App Service 方案上啟用進階威脅防護**
- **應在 Azure Container Registry 登錄上啟用進階威脅防護**
- **應在 Azure Key Vault 保存庫上啟用進階威脅防護**
- **應在 Azure Kubernetes Service 叢集上啟用進階威脅防護**
- **應在 Azure 儲存體帳戶上啟用進階威脅防護**
- **應在虛擬機器上啟用進階威脅防護**

這些新建議屬於**啟用進階威脅防護**安全性控制。

這些建議也包含快速修正功能。 

> [!IMPORTANT]
> 補救這些建議中的任何一項，都會產生相關資源的保護費用。 如果您目前的訂用帳戶中有相關資源，這些費用就會立即產生。 或是，如果您是在日後新增這些資源，則費用會在未來產生。
> 
> 例如，如果您的訂用帳戶中沒有任何 Azure Kubernetes Service 叢集，那麼您啟用威脅防護並不會產生任何費用。 如果您未來會在相同的訂用帳戶上新增叢集，則該叢集將會自動受到保護，並於該時間開始收費。

如需詳細資訊，請參閱[安全性建議參考頁面](recommendations-reference.md)。

深入了解 [Azure 資訊安全中心內的威脅防護](azure-defender.md)。




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>容器安全性改善 - 加快登錄掃描和重新整理文件的速度

在不斷投資容器安全性領域的過程中，我們很高興資訊安全中心在對 Azure Container Registry 中儲存的容器映像進行動態掃描時，已大幅地改善效能。 現在，掃描通常會在大約兩分鐘內完成。 在某些情況下，最多可能需要 15 分鐘的時間。

為了在 Azure 資訊安全中心的容器安全功能上改善簡潔度和指引方式，我們也重新整理了容器安全性的文件頁面。 

若要深入了解資訊安全中心的容器安全性，請參閱下列文章：

- [資訊安全中心的容器安全功能概觀](container-security.md)
- [與 Azure Container Registry 整合的詳細資料](defender-for-container-registries-introduction.md)
- [與 Azure Kubernetes Service 整合的詳細資料](defender-for-kubernetes-introduction.md)
- [如何掃描您的登錄並強化 Docker 主機](container-security.md)
- [來自 Azure Kubernetes Service 叢集威脅防護功能的安全性警示](alerts-reference.md#alerts-akscluster)
- [來自 Azure Kubernetes Service 主機威脅防護功能的安全性警示](alerts-reference.md#alerts-containerhost)
- [容器的安全性建議](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>使用新建議更新自適性應用程式控制和路徑規則中支援萬用字元

自適性應用程式控制功能已收到兩個重大更新：

* 新建議會識別先前未允許的可能合理行為。 「**您應該更新自適性應用程式控制原則中的允許清單規則**」建議會提示您將新規則新增至現有原則，以減少自適性應用程式控制違規警示中的誤判數量。

* 路徑規則現在支援萬用字元。 在此更新中，您可以使用萬用字元來設定允許的路徑規則。 支援的案例有兩種：

    * 在路徑結尾使用萬用字元，以允許此資料夾和子資料夾中的所有可執行檔

    * 在路徑中間使用萬用字元，可啟用其資料夾名稱不固定的已知可執行檔名稱 (例如，具有已知可執行檔的個人使用者資料夾、自動產生的資料夾名稱等等)。


[深入了解自適性應用程式控制](security-center-adaptive-application.md)。



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>已淘汰 SQL 進階資料安全性的六個原則

與 SQL 機器進階資料安全性相關的六個原則即將淘汰：

- 在 SQL 受控執行個體的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]
- 在 SQL 伺服器的進階資料安全性設定中，進階威脅防護類型應設定為「全部」
- SQL 受控執行個體的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址
- SQL 伺服器的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址
- 應在 SQL 受控執行個體進階資料安全性設定中，啟用傳給系統管理員和訂用帳戶擁有者的電子郵件通知
- 應在 SQL 伺服器進階資料安全性設定中啟用傳給系統管理員和訂用帳戶擁有者的通知

深入了解[內建原則](security-center-policy-definitions.md)。





## <a name="june-2020"></a>2020 年 6 月

六月的更新包括：
- [安全分數 API (預覽)](#secure-score-api-preview)
- [SQL 機器的進階資料安全性 (Azure、其他雲端及內部部署環境) (預覽)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [將 Log Analytics 代理程式部署至 Azure Arc 機器的兩項新建議 (預覽)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [可大規模建立連續匯出和工作流程自動化設定的新原則](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [使用 NSG 來保護非網際網路面向虛擬機器的新建議](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [啟用威脅防護和進階資料安全性的新原則](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>安全分數 API (預覽)

您現在可以透過[安全分數 API](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (目前為預覽狀態) 來存取您的分數。 API 方法可讓您靈活地查詢資料，並在一段時間後建立您自己的安全分數報告機制。 例如，您可以使用**安全分數** API 來取得特定訂用帳戶的分數。 此外，您可以使用**安全分數控制項** API 來列出您訂用帳戶的安全性控制項和目前分數。

如需安全分數 API 搭配使用外部工具的範例，請參閱 [GitHub 社群的安全分數區域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。

深入了解 [Azure 資訊安全中心內的安全分數和安全性控制項](secure-score-security-controls.md)。



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>SQL 機器的進階資料安全性 (Azure、其他雲端及內部部署環境) (預覽)

Azure 資訊安全中心的 SQL 機器進階資料安全性現在會保護裝載於 Azure、其他雲端環境，甚至是內部部署機器上的 SQL Server。 這會擴充 Azure 原生的 SQL 伺服器保護，進而完整支援混合式環境。

進階資料安全性可為您的 SQL 機器提供弱點評量和進階威脅防護，且無論機器位於何處都適用。

設定包含兩個步驟：

1. 將 Log Analytics 代理程式部署到您 SQL Server 的主機機器，以提供 Azure 帳戶的連線。

1. 在資訊安全中心的 [定價和設定] 頁面中啟用選用套件組合。

深入了解[適用於 SQL 機器的進階資料安全性](defender-for-sql-usage.md)。



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>將 Log Analytics 代理程式部署至 Azure Arc 機器的兩項新建議 (預覽)

已新增兩個新建議，協助您將 [Log Analytics 代理程式](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)部署至您的 Azure Arc 機器，並確保其受到 Azure 資訊安全中心的保護：

- **Windows 版 Azure Arc 機器上應安裝 Log Analytics 代理程式 (預覽)**
- **Linux 版 Azure Arc 機器上應安裝 Log Analytics 代理程式 (預覽)**

這些新建議會出現在與現有 (相關) 建議 (**您的機器上應安裝監視代理程式**) 相同的四個安全性控制項中：補救安全性設定、套用自適性應用程式控制、套用系統更新，以及啟用端點保護。

這些建議也包含快速修正功能，可協助加速部署程序。 

在[計算和應用程式建議](recommendations-reference.md#recs-computeapp)資料表中深入了解這兩項新的建議。

若要深入了解 Azure 資訊安全中心如何使用代理程式，請參閱[什麼是 Log Analytics 代理程式？](faq-data-collection-agents.md#what-is-the-log-analytics-agent)。

深入了解[適用於 Azure Arc 機器的擴充功能](../azure-arc/servers/manage-vm-extensions.md#enable-extensions-from-the-portal)。


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>可大規模建立連續匯出和工作流程自動化設定的新原則

自動化組織的監視和事件回應程程序，可大幅改善調查和緩解安全性事件所需的時間。

若要在整個組織中部署您的自動化設定，請使用這些內建的 'DeployIfdNotExist' Azure 原則來建立和設定[連續匯出](continuous-export.md)和[工作流程自動化](workflow-automation.md)程序：

您可以在 Azure 原則中找到這些原則：


|目標  |原則  |原則識別碼  |
|---------|---------|---------|
|連續匯出至事件中樞|[為 Azure 資訊安全中心警示與建議部署「匯出至事件中樞」](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|連續匯出至 Log Analytics 工作區|[為 Azure 資訊安全中心警示與建議部署「匯出至 Log Analytics 工作區」](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|安全性警示的工作流程自動化|[為 Azure 資訊安全中心警示部署工作流程自動化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|安全性建議的工作流程自動化|[為 Azure 資訊安全中心建議部署工作流程自動化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

開始使用[工作流程自動化範本](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。

若要深入了解如何使用這兩個匯出原則，請參閱[透過原則連續匯出 Azure 資訊安全中心警示和建議](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745)。


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>使用 NSG 來保護非網際網路面向虛擬機器的新建議

「實作安全性最佳做法」的安全性控制項現在包含下列新的建議：

- **應使用網路安全性群組保護非網際網路對應的虛擬機器**

「**網際網路面向虛擬機器應使用網路安全性群組加以保護**」的現有建議，並無法區分網際網路面向和非網際網路面向的 VM。 針對這兩種情況，如果 VM 未指派給網路安全性群組，就會產生高嚴重性建議。 這項新的建議會區隔非網際網路面向的機器，以減少誤判，並避免不必要的高嚴重性警示。

請在[網路建議](recommendations-reference.md#recs-network)資料表中深入了解。




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>啟用威脅防護和進階資料安全性的新原則

以下新原則已新增至 ASC 預設方案，其設計目的是為了協助相關資源類型啟用威脅防護或進階資料安全性。

您可以在 Azure 原則中找到這些原則：


| 原則                                                                                                                                                                                                                                                                | 原則識別碼                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Azure SQL Database 伺服器應啟用進階資料安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [應在機器上的 SQL 伺服器啟用進階資料安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [應在 Azure 儲存體帳戶上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [應在 Azure Key Vault 保存庫上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [應在 Azure App Service 方案上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [應在 Azure Container Registry 登錄上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [應在 Azure Kubernetes Service 叢集上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [應在虛擬機器上啟用進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

深入了解 [Azure 資訊安全中心內的威脅防護](azure-defender.md)。





## <a name="may-2020"></a>2020 年 5 月

5 月的更新包括：
- [重複警示歸併規則 (預覽)](#alert-suppression-rules-preview)
- [虛擬機器弱點評估現已正式推出](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Just-In-Time (JIT) 虛擬機器 (VM) 存取的變更](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [自訂建議已移至個別的安全性控制](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [在控制項中或以一般清單的方式，加入查看建議的切換](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [擴充的安全性控制「實作安全性最佳做法」](#expanded-security-control-implement-security-best-practices)
- [具有自訂中繼資料的自訂原則現已正式推出](#custom-policies-with-custom-metadata-are-now-generally-available)
- [損毀傾印分析功能移轉至無檔案攻擊偵測](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>重複警示歸併規則 (預覽)

這項新功能 (目前處於預覽狀態) 有助於減少警示疲勞。 可使用規則自動隱藏已知為無害或組織一般活動的相關警示。 如此您就可以專注處理最為密切相關的威脅。 

仍會產生符合已啟用歸併規則的警示，但其狀態會設定為 [關閉]。 您可以在 Azure 入口網站中看到狀態，或是您存取資訊安全中心的安全性警示的方式。

歸併規則會定義應會自動關閉警示的條件。 一般會使用歸併規則來執行下列動作：

- 隱藏已識別為誤判為真的警示

- 隱藏太常觸發而不實用的警示

深入瞭解[隱藏來自 Azure 資訊安全中心威脅防護的警示](alerts-suppression-rules.md)。


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>虛擬機器弱點評估現已正式推出

資訊安全中心標準層級現在包含了虛擬機器的整合式弱點評估，不需額外付費。 此延伸模組是由 Qualys 提供技術支援，但會直接回報給資訊安全中心。 您不需要 Qualys 授權或 Qualys 帳戶，一切都可以在資訊安全中心內流暢進行。

新的解決方案可持續掃描您的虛擬機器，並找出弱點，然後在資訊安全中心中呈現結果。 

若要部署解決方案，請使用新的安全性建議：

「在虛擬機器上啟用內建弱點評定解決方案 (Qualys 技術支援)」

深入瞭解[資訊安全中心的虛擬機器整合式弱點評估](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)。



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Just-In-Time (JIT) 虛擬機器 (VM) 存取的變更

資訊安全中心包含可保護 VM 管理連接埠的選用功能。 這可讓您防禦最常見的暴力密碼破解攻擊形式。

此更新會對這項功能進行下列變更：

- 建議您在 VM 上啟用 JIT 的建議已重新命名。 以前是「Just-In-Time 網路存取控制應套用在虛擬機器上」，現在是：「應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠」。

- 只有在有開啟的管理連接埠時才會觸發建議。

深入瞭解 [JIT 存取功能](security-center-just-in-time.md)。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>自訂建議已移至個別的安全性控制

增強型安全分數所引進的其中一個安全性控制項是「實作安全性最佳做法」。 針對您的訂閱所建立的任何自訂建議都會自動放在該控制項中。 

為了讓您更輕鬆地找到您的自訂建議，我們已將其移至專用的安全性控制「自訂建議」。 此控制項不會影響您的安全分數。

深入瞭解[在 Azure 資訊安全中心之內的增強型安全分數 (預覽)](secure-score-security-controls.md) 中出現的安全性控制項。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>在控制項中或以一般清單的方式，加入查看建議的切換

安全性控制是相關安全性建議的邏輯群組， 會反映您的易受攻擊面。 控制項是一組安全性建議，其中包含有助於執行建議的指示。

若要立即查看您的組織如何保護每個個別的受攻擊面，請參閱每個安全性控制的分數。

根據預設，您的建議會顯示在 [安全性] 控制項中。 在此更新中，您也可以將其顯示為清單。 若要按受影響資源的健全狀況狀態排序，以顯示為方便查看的簡明清單，請使用新的切換功能 [依據控制項分組]。 切換功能位在入口網站中的清單上方。

安全性控制措施和此切換功能皆屬於新的安全分數體驗。 請記得在入口網站中傳送您的意見反應給我們。

深入瞭解[在 Azure 資訊安全中心之內的增強型安全分數 (預覽)](secure-score-security-controls.md) 中出現的安全性控制項。

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="在連續匯出設定中包含安全性結果切換":::

### <a name="expanded-security-control-implement-security-best-practices"></a>擴充的安全性控制「實作安全性最佳做法」 

增強型安全分數所引進的其中一個安全性控制項是「實作安全性最佳做法」。 當此控制項中有建議時，不會影響到安全分數。 

在此更新中，有三個建議已移出原先放在其中的控制項，並新增至此最佳做法控制項。 我們已經採取此步驟，是因為我們判斷這三個建議的風險低於一開始所考慮的風險。

此外，也引進了兩個新的建議，並將其新增至這個控制項。

這三個已移動的建議如下：

- **應在您訂閱上具有讀取權限的帳戶上啟用 MFA** (原本在「啟用 MFA」控制項中)
- **具有讀取權限的外部帳戶應從您的訂閱**中移除 (原本是在「管理存取權和權限」控制項中)
- **您的訂閱最多可指定 3 位擁有者** (原本是在「管理存取權和權限」控制項中)

新增至控制項的兩個新建議如下：

- **Windows 虛擬機器上應該安裝來賓設定擴充功能 (預覽)** - 使用 [Azure 原則來賓設定](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)在虛擬機器中提供伺服器和應用程式設定的可見度 (僅限 Windows)。

- **您的機器上應該啟用 Windows Defender 惡意探索防護 (預覽)** - Windows Defender 惡意探索防護會運用 Azure 原則來賓設定代理程式的功能。 「惡意探索防護」有四個元件，設計用來鎖定裝置，使其免於遭受惡意程式碼攻擊的各種攻擊和封鎖行為，同時讓企業能夠平衡本身的安全性風險和生產力需求 (僅限 Windows)。

若要深入瞭解 Windows Defender 惡意探索防護，請參閱[建立及部署惡意探索防護原則](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)。

在[增強型安全分數 (預覽)](secure-score-security-controls.md) 中深入了解安全性控制項。



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>具有自訂中繼資料的自訂原則現已正式推出

資訊安全中心建議體驗、安全分數和法規合規性標準儀表板中現在包含自訂原則現。 這項功能現已正式推出，可讓您在資訊安全中心中擴充組織的安全性評估涵蓋範圍。 

在 Azure 原則中建立自訂方案、新增原則並將其上線至 Azure 資訊安全中心，並將其視覺化為建議。

我們現在也新增了編輯自訂建議中繼資料的選項。 中繼資料選項包括嚴重性、補救步驟、威脅資訊等等。  

深入瞭解[使用詳細資料來增強您的自訂建議](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)。



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>損毀傾印分析功能移轉至無檔案攻擊偵測 

我們會將 Windows 損毀傾印分析 (CDA) 偵測功能整合到[無檔案攻擊偵測](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers)。 無檔案攻擊偵測分析會針對 Windows 機器引進下列安全性警示的改良版本：探索到程式碼插入、偵測到偽裝的 Windows 模組、發現的程式碼，以及偵測到可疑的程式碼片段。

此轉換的一些優點：

- **主動式和即時的惡意程式碼偵測** - 涉及等待損毀然後執行分析以尋找惡意成品所用的 CDA 方法。 使用無檔案攻擊偵測可在執行中時，為記憶體內部威脅提供主動式識別。 

- **擴充的警示** - 來自無檔案攻擊偵測的安全性警示包含無法從 CDA 取得的擴充，例如使用中的網路連線資訊。 

- **警示匯總** - 當 CDA 在單一損毀傾印中偵測到多個攻擊模式時，它會觸發多個安全性警示。 無檔案攻擊偵測會將所有已識別的攻擊模式從相同的程式合併到單一警示，而不需要相互關聯多個警示。

- **降低 Log Analytics 工作區的需求** - 包含潛在敏感性資料的損毀傾印不會再上傳至您的 Log Analytics 工作區。



## <a name="april-2020"></a>2020 年 4 月

4 月的更新包括：
- [動態合規性套件現已正式推出](#dynamic-compliance-packages-are-now-generally-available)
- [Azure 資訊安全中心免費層中現在已包含身分識別建議](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>動態合規性套件現已正式推出

Azure 資訊安全中心的法規合規性儀表板現在包含**動態相容性套件** (現已正式運作)，以追蹤額外的產業和法規標準。

您可以從 [資訊安全中心安全性原則] 頁面，將動態相容性套件新增至您的訂閱或管理群組。 當您上架標準或基準測試時，標準會出現在您的法規合規性儀表板中，並將所有相關聯的相容性資料對應為評量。 任何已上架之標準的摘要報告都可供下載。

現在，您可以加入標準，例如：

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official 與 UK NHS**
- **加拿大聯邦 PBMM**
- **Azure CIS 1.1.0 (新)** (這是更完整的 Azure CIS 1.1.0 標記法)

此外，我們最近新增了 **Azure 安全性基準測試**，這是 Microsoft 針對以通用合規性架構為基礎的安全性和合規性最佳做法所撰寫的 Azure 特定指導方針。 當儀表板可供使用時，將會支援其他標準。  
 
深入了解[如何在您的法規合規性儀表板中自訂一組標準](update-regulatory-compliance-packages.md)。


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Azure 資訊安全中心免費層中現在已包含身分識別建議

Azure 資訊安全中心免費層的身分識別和存取安全性建議現已正式推出。 這是讓雲端安全性狀態管理 (CSPM) 功能免費的一部份。 到目前為止，這些建議僅適用於標準定價層。

身分識別與存取建議的範例包括：

- 「應在您訂閱上具有擁有者權限的帳戶上啟用多重要素驗證。」
- 「應針對您的訂閱指定最多 3 位擁有者。」
- 「已取代帳戶應該從您的訂閱中移除。」

如果您擁有免費定價層的訂閱，其安全分數會受到這項變更的影響，因為這些訂閱永遠不會評估其身分識別和存取安全性。

深入瞭解[身分識別與存取建議](recommendations-reference.md#recs-identity)。

深入瞭解[監視身分識別及存取](security-center-identity-access.md)。