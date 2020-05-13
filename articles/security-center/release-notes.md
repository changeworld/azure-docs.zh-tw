---
title: Azure 資訊安全中心的版本資訊
description: Azure 資訊安全中心中新增和變更內容的描述。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: bfe1e5d6a0c4171a262b36387f02be356fb1d72d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210892"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 資訊安全中心的新功能

Azure 安全性正進行開發，並持續獲得改善。 為了讓您隨時掌握最新的發展，此頁面會提供下列資訊：

- 新功能
- 錯誤修正
- 已被取代的功能

此頁面會定期更新，因此請經常重新流覽。 如果您要尋找超過六個月的專案，您會在[Azure 資訊安全中心的新功能](release-notes-archive.md)中找到它們。


## <a name="may-2020"></a>2020 年 5 月

### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>即時（JIT）虛擬機器（VM）存取的變更

資訊安全中心包含可保護 Vm 管理埠的選用功能。 這可讓您防禦最常見的暴力密碼破解攻擊形式。

此更新會對這項功能進行下列變更：

- 建議您在 VM 上啟用 JIT，已重新命名。 先前的「即時網路存取控制應該套用在虛擬機器上」，現在是：「虛擬機器的管理埠應該使用即時網路存取控制來保護」。

- 建議已設定為只有在有開啟的管理埠時才會觸發。

[深入瞭解 JIT 存取功能](security-center-just-in-time.md)。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>自訂建議已移至個別的安全性控制

增強型安全分數所引進的其中一個安全性控制項是「實施安全性最佳做法」。 針對您的訂用帳戶所建立的任何自訂建議都會自動放在該控制項中。 

為了讓您更輕鬆地找到您的自訂建議，我們已將其移至專用的安全性控制「自訂建議」。 此控制項不會影響您的安全分數。

[在 Azure 資訊安全中心中深入瞭解增強式安全分數（預覽）](secure-score-security-controls.md)中的安全性控制項。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>在控制項中或以一般清單的方式，切換加入以查看建議

安全性控制是相關安全性建議的邏輯群組。 它們會反映您的易受攻擊面。 控制項是一組安全性建議，其中包含可協助您執行這些建議的指示。

若要立即查看您的組織如何保護每個個別的受攻擊面，請參閱每個安全性控制的分數。

根據預設，您的建議會顯示在 [安全性] 控制項中。 在此更新中，您也可以將其顯示為清單。 若要以受影響資源的健全狀況狀態排序的簡單列表來查看它們，請使用新的切換 [群組依據控制項]。 切換會在入口網站中的清單上方。

安全性控制措施和此切換是新安全分數體驗的一部分。 請記得在入口網站中傳送您的意見反應給我們。

[在 Azure 資訊安全中心中深入瞭解增強式安全分數（預覽）](secure-score-security-controls.md)中的安全性控制項。


### <a name="account-security-recommendations-moved-to-security-best-practices-security-control"></a>帳戶安全性建議已移至「安全性最佳做法」安全性控制

增強型安全分數所引進的其中一個安全性控制項是「安全性最佳做法」。 當此控制項中有建議時，不會影響到安全分數。 

在此更新中，有三個建議已移出原先放在其中的控制項，以及這個最佳做法控制項。 我們已經採取此步驟，是因為我們判斷這三個建議的風險低於一開始所考慮的風險。

建議如下：

- 在訂用帳戶上具有讀取權限的帳戶（原本是在「啟用 MFA」控制項中），應啟用 MFA
- 具有讀取權限的外部帳戶應從您的訂用帳戶中移除（原本是在「管理存取權和許可權」控制項中）
- 您的訂用帳戶應指定最多3個擁有者（原本是在「管理存取權和許可權」控制項中）

[在 Azure 資訊安全中心中深入瞭解增強式安全分數（預覽）](secure-score-security-controls.md)中的安全性控制項。


### <a name="custom-policies-with-custom-metadata-generally-available"></a>具有自訂中繼資料的自訂原則正式推出

自訂原則現在是資訊安全中心建議經驗、安全分數和法規合規性標準儀表板的一部分。 這項功能現已正式推出，可讓您在資訊安全中心中擴充組織的安全性評估涵蓋範圍。 

在 Azure 原則中建立自訂方案、新增原則並將其上線至 Azure 資訊安全中心，並將其視覺化為建議。

我們現在也新增了編輯自訂建議中繼資料的選項。 中繼資料選項包括嚴重性、補救步驟、威脅資訊等等。  

[深入瞭解如何使用詳細資訊來增強您的自訂建議](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information)。


## <a name="april-2020"></a>2020 年 4 月

### <a name="dynamic-compliance-packages-now-generally-available"></a>動態合規性套件現已正式推出

Azure 資訊安全中心的法規合規性儀表板現在包含**動態合規性套件**（現已正式運作），以追蹤其他產業和法規標準。

您可以從 [資訊安全中心安全性原則] 頁面，將動態相容性套件新增至您的訂用帳戶或管理群組。 當您上架標準或基準測試時，標準會出現在您的法規合規性儀表板中，並將所有相關聯的相容性資料對應為評量。 任何已上架之標準的摘要報告都可供下載。

現在，您可以加入標準，例如：

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **英國官方和英國 NHS**
- **加拿大聯邦 PBMM**
- **AZURE cis 1.1.0 （新）** （這是 Azure cis 1.1.0 的更完整標記法）

此外，我們最近新增了**Azure 安全性基準測試**，這是 Microsoft 針對以通用合規性架構為基礎的安全性和合規性最佳作法所撰寫的 azure 特定指導方針。 當儀表板可供使用時，將會支援其他標準。  
 
[深入瞭解在您的法規合規性儀表板中自訂一組標準](update-regulatory-compliance-packages.md)。


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Azure 資訊安全中心免費層中現在已包含身分識別建議

Azure 資訊安全中心免費層的身分識別和存取安全性建議現已正式推出。 這是讓雲端安全性狀態管理（CSPM）功能免費的一部分。 到目前為止，這些建議僅適用于標準定價層。

身分識別和存取建議的範例包括：

- 「應在訂用帳戶上具有擁有者許可權的帳戶上啟用多重要素驗證」。
- 「應為您的訂用帳戶指定最多三個擁有者」。
- 「應從您的訂用帳戶移除已淘汰的帳戶」。

如果您擁有免費定價層的訂用帳戶，其安全分數會受到這項變更的影響，因為這些訂用帳戶永遠不會評估其身分識別和存取安全性。

[深入瞭解身分識別和存取建議](recommendations-reference.md#recs-identity)。
[深入瞭解監視身分識別和存取](security-center-identity-access.md)。


## <a name="march-2020"></a>2020 年 3 月

### <a name="workflow-automation-is-now-generally-available"></a>工作流程自動化現已正式推出

Azure 資訊安全中心的工作流程自動化功能現已正式推出。 使用它來自動觸發安全性警示和建議的 Logic Apps。 此外，您還可以使用手動觸發程式來取得警示，以及具有 [快速修正] 選項的所有建議。

每個安全性程式都包含事件回應的多個工作流程。 這些流程可能包括通知相關的專案關係人、啟動變更管理程式，以及套用特定的補救步驟。 安全性專家建議您盡可能地自動化這些程式的許多步驟。 自動化會藉由確保按照您預先定義的需求快速、一致地執行程式步驟，來減少額外負荷並改善您的安全性。

如需執行工作流程之自動和手動資訊安全中心功能的詳細資訊，請參閱[工作流程自動化](workflow-automation.md)。

若要瞭解如何建立 Logic Apps，請參閱[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure 資訊安全中心與 Windows 管理中心整合

現在可以將您的內部部署 Windows 伺服器直接從 Windows 系統管理中心移至 Azure 資訊安全中心。 資訊安全中心接著會變成您的單一玻璃窗格，以查看所有 Windows 系統管理中心資源的安全性資訊，包括內部部署伺服器、虛擬機器和其他 PaaS 工作負載。

將伺服器從 Windows 系統管理中心移至 Azure 資訊安全中心之後，您將能夠：

- 在 Windows 系統管理中心的資訊安全中心延伸模組中，查看安全性警示和建議。
- 在 Azure 入口網站（或透過 API）中的資訊安全中心，查看安全性狀態，並取出 Windows 管理中心受管理伺服器的其他詳細資訊。

深入瞭解如何將[Azure 資訊安全中心與 Windows 系統管理中心整合](windows-admin-center-integration.md)。


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes Service 的保護

Azure 資訊安全中心正在擴充其容器安全性功能，以保護 Azure Kubernetes Service （AKS）。

熱門的開放原始碼平臺 Kubernetes 已廣泛採用，現在已成為容器協調流程的業界標準。 儘管這種廣泛的實行，仍然缺乏瞭解如何保護 Kubernetes 環境。 保護容器化應用程式的受攻擊面需要專業知識，以確保能夠安全地設定基礎結構，並持續監視潛在威脅。

資訊安全中心防衛包括：

- **探索和可見度**-在資訊安全中心註冊的訂用帳戶內，持續探索受管理的 AKS 實例。
- **安全性建議**-可採取動作的建議，以協助您符合 AKS 的安全性最佳作法。 這些建議會包含在您的安全分數中，以確保它們會被視為組織安全性狀態的一部分。 如需 AKS 相關的建議，您可能會看到「應該使用角色型存取控制來限制對 Kubernetes 服務叢集的存取」。
- **威脅防護**-透過持續分析 AKS 部署，資訊安全中心在主機和 AKS 叢集層級偵測到威脅和惡意活動的警示。

[深入瞭解 Azure Kubernetes Services 與資訊安全中心整合](azure-kubernetes-service-integration.md)。
[深入瞭解資訊安全中心中的容器安全性功能](container-security.md)。


### <a name="improved-just-in-time-experience"></a>改良的即時體驗

適用于 Azure 資訊安全中心的即時工具的功能、操作和 UI，可保護您的管理埠，如下所示： 

- **理由欄位**-當透過 Azure 入口網站的即時頁面要求存取虛擬機器（VM）時，可以使用新的選擇性欄位來輸入要求的理由。 輸入此欄位的資訊可在活動記錄檔中追蹤。 
- **自動清除多餘的即時（JIT）規則**-每當您更新 JIT 原則時，就會自動執行清除工具來檢查整個規則集的有效性。 此工具會尋找您的原則中的規則與 NSG 中的規則之間的不符之處。 如果清除工具發現不相符的問題，它會判斷原因，而當安全地移除不需要的內建規則。 清理程式永遠不會刪除您已建立的規則。 

[深入瞭解 JIT 存取功能](security-center-just-in-time.md)。


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Web 應用程式的兩個安全性建議已被取代

與 web 應用程式相關的兩個安全性建議會被取代： 

- 應強化 IaaS Nsg 上 web 應用程式的規則。
    （相關原則： IaaS 上 web 應用程式的 Nsg 規則應該要強化）

- 應用程式服務的存取權應該受到限制。
    （相關原則：應該限制應用程式服務的存取權 [預覽]）

這些建議不會再出現在資訊安全中心的建議清單中。 相關的原則將不再包含在名為「資訊安全中心預設」的方案中。

[深入瞭解安全性建議](recommendations-reference.md)。

## <a name="february-2020"></a>2020 年 2 月

### <a name="fileless-attack-detection-for-linux-is-now-in-preview"></a>適用于 Linux 的 Fileless 攻擊偵測現供預覽

當攻擊者增加採用 stealthier 方法以避免偵測時，除了 Windows 之外，Azure 資訊安全中心也會擴充 Linux 的 fileless 攻擊偵測。 Fileless 攻擊會利用軟體弱點、將惡意承載插入良性系統進程，並在記憶體中隱藏。 這些技術：

- 最小化或排除磁片上的惡意程式碼追蹤
- 藉由以磁片為基礎的惡意程式碼掃描解決方案，大幅降低偵測的機會

若要對付這項威脅，Azure 資訊安全中心在2018年10月發行 Windows 的 fileless 攻擊偵測，而且現在也已在 Linux 上擴充 fileless 攻擊偵測。 


## <a name="january-2020"></a>2020 年 1 月

### <a name="enhanced-secure-score"></a>增強的安全分數

Azure 資訊安全中心的安全分數功能增強版本現已提供預覽。 在此版本中，會將多個建議分組為安全性控制措施，以更清楚地反映易受攻擊面（例如，限制對管理埠的存取）。

請熟悉預覽階段中的安全分數變更，並決定可協助您進一步保護環境的其他補救。

若要深入瞭解，請[流覽 Azure 資訊安全中心中的增強型安全分數（預覽）](secure-score-security-controls.md)。