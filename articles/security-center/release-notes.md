---
title: Azure 資訊安全中心版本資訊
description: Azure 資訊安全中心新增項目和變更內容的相關描述。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: 6909bcbc67680f9205af8a79782907d4671d668b
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860710"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 資訊安全中心的新功能

Azure 安全性持續再開發改良。 為了讓您隨時掌握最新的開發訊息，此頁面提供下列相關資訊：

- 新功能
- 錯誤修正
- 已被取代的功能

此頁面會定期更新，因此請時常瀏覽。 如果想要尋找超過 6 個月的項目，請前往[Azure 資訊安全中心內新功能的封存](release-notes-archive.md)。


## <a name="may-2020"></a>2020 年 5 月


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

深入瞭解[資訊安全中心的虛擬機器整合式弱點評估](built-in-vulnerability-assessment.md)。



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Just-In-Time (JIT) 虛擬機器 (VM) 存取的變更

資訊安全中心包含可保護 VM 管理連接埠的選用功能。 這可讓您防禦最常見的暴力密碼破解攻擊形式。

此更新會對這項功能進行下列變更：

- 建議您在 VM 上啟用 JIT 的建議已重新命名。 以前是「Just-In-Time 網路存取控制應套用在虛擬機器上」，現在是：「應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠」。

- 建議已設定為只有在有開啟的管理連接埠時才會觸發。

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


### <a name="expanded-security-control-implement-security-best-practices"></a>擴充的安全性控制「實作安全性最佳做法」 

增強型安全分數所引進的其中一個安全性控制項是「實作安全性最佳做法」。 當此控制項中有建議時，不會影響到安全分數。 

在此更新中，有三個建議已移出原先放在其中的控制項，並新增至此最佳做法控制項。 我們已經採取此步驟，是因為我們判斷這三個建議的風險低於一開始所考慮的風險。

此外，也引進了兩個新的建議，並將其新增至這個控制項。

這三個已移動的建議如下：

- **應在您訂閱上具有讀取權限的帳戶上啟用 MFA** (原本在「啟用 MFA」控制項中)
- **具有讀取權限的外部帳戶應從您的訂閱**中移除 (原本是在「管理存取權和權限」控制項中)
- **您的訂閱最多可指定 3 位擁有者** (原本是在「管理存取權和權限」控制項中)

新增至控制項的兩個新建議如下：

- **[預覽] 應該安裝來賓設定代理程式** - 使用 [Azure 原則來賓設定](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)在虛擬機器中提供伺服器和應用程式設定的可見度 (僅限 Windows)。

- **[預覽] 應該啟用 Windows 惡意探索防護** - Windows Defender 惡意探索防護會運用 Azure 原則來賓設定代理程式。 「惡意探索防護」有四個元件，設計用來鎖定裝置，使其免於遭受惡意程式碼攻擊的各種攻擊和封鎖行為，同時讓企業能夠平衡本身的安全性風險和生產力需求 (僅限 Windows)。

若要深入瞭解 Windows Defender 惡意探索防護，請參閱[建立及部署惡意探索防護原則](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)。

深入瞭解[在 Azure 資訊安全中心之內的增強型安全分數 (預覽)](secure-score-security-controls.md) 中出現的安全性控制項。


### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>具有自訂中繼資料的自訂原則現已正式推出

資訊安全中心建議體驗、安全分數和法規合規性標準儀表板中現在包含自訂原則現。 這項功能現已正式推出，可讓您在資訊安全中心中擴充組織的安全性評估涵蓋範圍。 

在 Azure 原則中建立自訂方案、新增原則並將其上線至 Azure 資訊安全中心，並將其視覺化為建議。

我們現在也新增了編輯自訂建議中繼資料的選項。 中繼資料選項包括嚴重性、補救步驟、威脅資訊等等。  

深入瞭解[使用詳細資料來增強您的自訂建議](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information)。


### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>損毀傾印分析功能移轉至無檔案攻擊偵測 

我們會將 Windows 損毀傾印分析 (CDA) 偵測功能整合到[無檔案攻擊偵測](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless)。 無檔案攻擊偵測分析會針對 Windows 機器引進下列安全性警示的改良版本：探索到程式碼插入、偵測到偽裝的 Windows 模組、發現的程式碼，以及偵測到可疑的程式碼片段。

此轉換的一些優點：

- **主動式和及時的惡意程式碼偵測** - 涉及等待損毀然後執行分析以尋找作用中的惡意程式碼所用的的 CDA 方法。 使用無檔案攻擊偵測可在執行中時，為記憶體內部威脅提供主動式識別。 

- **擴充的警示** - 來自無檔案攻擊偵測的安全性警示包含無法從 CDA 取得的擴充，例如使用中的網路連線資訊。 

- **警示匯總** - 當 CDA 在單一損毀傾印中偵測到多個攻擊模式時，它會觸發多個安全性警示。 無檔案攻擊偵測會將所有已識別的攻擊模式從相同的程式合併到單一警示，而不需要相互關聯多個警示。

- **降低 Log Analytics 工作區的需求** - 包含潛在敏感性資料的損毀傾印不會再上傳至您的 Log Analytics 工作區。



## <a name="april-2020"></a>2020 年 4 月

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


## <a name="march-2020"></a>2020 年 3 月

### <a name="workflow-automation-is-now-generally-available"></a>工作流程自動化現已正式推出

Azure 資訊安全中心的工作流程自動化功能現已正式推出。 可使用此功能自動對安全性警示和建議觸發 Logic Apps。 此外，您還可以使用手動觸發程式來取得警示，以及具有快速修正選項的所有建議。

每個安全性程式都包含事件回應的多個工作流程。 這些流程可能包括通知相關的專案利害關係人、啟動變更管理程式，以及套用特定的補救步驟。 安全性專家建議您盡可能自動化這些程式的許多步驟。 自動化會藉由確保按照您預先定義的需求快速、一致地執行程式步驟，來減少額外負荷並改善您的安全性。

如需執行工作流程之自動和手動資訊安全中心功能的詳細資訊，請參閱[工作流程自動化](workflow-automation.md)。

深入瞭解[建立邏輯應用程式](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure 資訊安全中心與 Windows 管理中心整合

現在可以將您的內部部署 Windows 伺服器直接從 Windows 系統管理中心移至 Azure 資訊安全中心。 資訊安全中心接著會變成您的單一窗格，可供查看所有 Windows 系統管理中心資源的安全性資訊，包括內部部署伺服器、虛擬機器和其他 PaaS 工作負載。

將伺服器從 Windows 系統管理中心移至 Azure 資訊安全中心之後，您將能夠：

- 在 Windows 系統管理中心的資訊安全中心延伸模組中，查看安全性警示和建議。
- 在 Azure 入口網站 (或透過 API) 中的資訊安全中心，查看安全性狀態，並擷取 Windows 管理中心受控伺服器的其他詳細資訊。

深入瞭解[如何整合 Azure 資訊安全中心與 Windows 管理中心](windows-admin-center-integration.md)。


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes Service 的防護

Azure 資訊安全中心正在擴充其容器安全性功能，以保護 Azure Kubernetes Service (AKS)。

熱門的開放原始碼平台 Kubernetes 已獲得廣泛採用，現在已成為容器協調流程的業界標準。 雖然這種實作相當廣泛，仍然不瞭解如何保護 Kubernetes 環境。 保護容器化應用程式的受攻擊面需要專業知識，以確保能夠安全地設定基礎結構，並持續監視潛在威脅。

資訊安全中心防禦包括：

- **探索與可見度** - 在向資訊安全中心註冊的訂閱內，持續探索受控 AKS 執行個體。
- **安全性建議** - 可採取動作的建議，以協助您符合 AKS 的安全性最佳做法。 這些建議會包含在您的安全分數中，以確保這些建議會被視為組織安全性狀態的一部份。 如需 AKS 相關的建議，您可以參閱「應使用角色型存取控制來限制對 Kubernetes Service 叢集的存取」。
- **威脅防護** - 透過 AKS 部署的持續分析，資訊安全中心在主機和 AKS 叢集層級偵測到威脅和惡意活動的警示。

深入瞭解 [Azure Kubernetes Service 與資訊安全中心整合](azure-kubernetes-service-integration.md)。

深入瞭解[資訊安全中心內的容器安全性功能](container-security.md)。


### <a name="improved-just-in-time-experience"></a>改良的 Just-In-Time 體驗

適用於 Azure 資訊安全中心的 Just-In-Time 工具保護您的管理埠所用的功能、操作和 UI 經過強化，如下所示： 

- **理由欄位** - 透過 Azure 入口網站的 Just-In-Time 頁面要求存取虛擬機器 (VM) 時，可以使用新的選用欄位來輸入要求的理由。 輸入此欄位的資訊可在活動記錄檔中追蹤。 
- **自動清除多餘的 Just-In-Time 規則** - 每當您更新 JIT 原則時，就會自動執行清除工具來檢查整個規則集的有效性。 此工具會尋找您的原則中的規則與 NSG 中的規則之間的不符之處。 如果清除工具發現不相符的問題，它會判斷原因，而當安全地移除不需要的內建規則。 清理程式永遠不會刪除您已建立的規則。 

深入瞭解 [JIT 存取功能](security-center-just-in-time.md)。


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Web 應用程式的兩個安全性建議已被取代

與 Web 應用程式相關的兩個安全性建議會被取代： 

- 應強化 IaaS NSG 上 Web 應用程式的規則。
    (相關原則：應在 IaaS 上強化 Web 應用程式的 NSG 規則)

- 應限制對應用程式服務的存取。
    (相關原則：應限制存取應用程式服務 [預覽])

這些建議不會再出現在資訊安全中心的建議清單中。 相關的原則將不再包含在名為「資訊安全中心預設」的方案中。

深入瞭解[安全性建議](recommendations-reference.md)。



## <a name="february-2020"></a>2020 年 2 月

### <a name="fileless-attack-detection-for-linux-preview"></a>適用於 Linux 的無檔案攻擊偵測 (預覽)

當攻擊者增加採用 stealthier 方法以避免偵測時，除了 Windows 之外，Azure 資訊安全中心也會擴充 Linux 的無檔案攻擊偵測。 無檔案攻擊會運用軟體弱點，並將惡意承載插入良性系統程序，然後在記憶體中隱藏。 這些技術：

- 最小化或排除磁碟上的惡意程式碼追蹤
- 藉由以磁碟為基礎的惡意程式碼掃描解決方案，大幅降低偵測的機會

若要對付這項威脅，Azure 資訊安全中心在 2018 年 10 月發行 Windows 的無檔案攻擊偵測，而且現在也已在 Linux 上擴充無檔案攻擊偵測。 


## <a name="january-2020"></a>2020 年 1 月

### <a name="enhanced-secure-score-preview"></a>增強的安全分數 (預覽)

Azure 資訊安全中心的安全分數功能增強版本現已提供預覽。 在此版本中，會將多個建議分組為安全性控制措施，以便更清楚地反映易受攻擊面 (例如，限制對管理連接埠的存取)。

請熟悉預覽階段中的安全分數變更，並決定有助於您進一步保護環境的其他補救。

深入瞭解[在 Azure 資訊安全中心之內的增強型安全分數 (預覽)](secure-score-security-controls.md)。