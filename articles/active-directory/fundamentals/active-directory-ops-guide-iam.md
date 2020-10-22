---
title: Azure Active Directory 身分識別與存取管理作業參考指南
description: 此操作參考指南說明保護身分識別和存取管理作業所應採取的檢查和動作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 66bce573be5a31641bdff809b8e9a79b617a703a
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370996"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Azure Active Directory 身分識別與存取管理作業參考指南

[Azure AD 作業參考指南](active-directory-ops-guide-intro.md)的這一節說明您應考慮的檢查和動作，以保護和管理身分識別和其指派的生命週期。

> [!NOTE]
> 這些建議是從發行日期開始的最新狀態，但可能會隨著時間而改變。 組織應持續評估其身分識別實務，因為 Microsoft 產品和服務會隨著時間而演進。

## <a name="key-operational-processes"></a>重要的操作流程

### <a name="assign-owners-to-key-tasks"></a>將擁有者指派給重要工作

管理 Azure Active Directory 需要持續執行重要的作業工作，以及可能不屬於首度發行專案的進程。 您必須設定這些工作以維護您的環境。 主要工作和其建議的擁有者包括：

| Task | 擁有者 |
| :- | :- |
| 定義如何建立 Azure 訂用帳戶的流程 | 因組織而異 |
| 決定誰獲得 Enterprise Mobility + Security 授權 | IAM 作業小組 |
| 決定誰獲得 Microsoft 365 授權 | 生產力小組 |
| 決定誰取得其他授權，例如 Dynamics、Visual Studio Codespaces | 應用程式擁有者 |
| 指派授權 | IAM 作業小組 |
| 疑難排解和補救授權指派錯誤 | IAM 作業小組 |
| 在 Azure AD 中將身分識別布建至應用程式 | IAM 作業小組 |

當您複習清單時，可能會發現您需要為缺少擁有者的工作指派擁有者，或針對擁有者未與上述建議一致的工作，調整其擁有權。

#### <a name="assigning-owners-recommended-reading"></a>指派擁有者建議的讀取

- [在 Azure Active Directory 中指派系統管理員角色](../roles/permissions-reference.md)
- [Azure 中的治理](../../governance/index.yml)

## <a name="on-premises-identity-synchronization"></a>內部部署身分識別同步處理

### <a name="identify-and-resolve-synchronization-issues"></a>找出並解決同步處理問題

Microsoft 建議您對內部部署環境中可能會導致雲端同步處理問題的問題，有良好的基準與瞭解。 由於 [IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) 和 [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#why-use-azure-ad-connect-health) 等自動化工具可能會產生大量的誤報，因此我們建議您在發生錯誤時清除這些物件，以找出已尚待發揮超過100天的同步處理錯誤。 長期未解決的同步處理錯誤可能會產生支援事件。 針對[同步處理期間的錯誤進行疑難排解](../hybrid/tshoot-connect-sync-errors.md)，可概述不同類型的同步處理錯誤、一些可能會導致這些錯誤的案例，以及修正錯誤的可能方法。

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD Connect 同步處理設定

若要啟用所有混合式體驗、以裝置為基礎的安全性狀態，以及與 Azure AD 的整合，您必須同步處理您的員工用來登入其桌上型電腦的使用者帳戶。

如果您未同步處理使用者登入的樹系，則應該將同步處理變更為來自適當的樹系。

#### <a name="synchronization-scope-and-object-filtering"></a>同步處理範圍和物件篩選

移除不需要同步處理之物件的已知 bucket，具有下列作業優點：

- 較少的同步處理錯誤來源
- 更快速的同步處理週期
- 從內部部署執行較少的「垃圾」，例如，與雲端中不相關的內部部署服務帳戶的全域通訊清單污染

> [!NOTE]
> 如果您發現您正在匯入許多未匯出至雲端的物件，您應該依 OU 或特定屬性進行篩選。

要排除的物件範例包括：

- 雲端應用程式未使用的服務帳戶
- 不打算在雲端案例中使用的群組，例如用來授與資源存取權的群組
- 要以 Azure AD 表示的外部身分識別的使用者或連絡人 B2B 共同作業
- 員工不打算從中存取雲端應用程式的電腦帳戶，例如伺服器

> [!NOTE]
> 如果單一人類身分識別具有多個帳戶，而這些帳戶來自于傳統的網域遷移、合併或取得之類的帳戶，您應該只同步處理使用者每日使用的帳戶，例如他們用來登入電腦的帳戶。

在理想的情況下，您會想要在減少要同步處理的物件數目和規則中的複雜度之間達到平衡。 一般來說，OU/容器 [篩選](../hybrid/how-to-connect-sync-configure-filtering.md) 和簡單的屬性對應（cloudFiltered 屬性）之間的組合是有效的篩選組合。

> [!IMPORTANT]
> 如果您在生產環境中使用群組篩選，則應轉換成另一種篩選方法。

#### <a name="sync-failover-or-disaster-recovery"></a>同步容錯移轉或嚴重損壞修復

Azure AD Connect 在布建過程中扮演著重要的角色。 如果同步處理伺服器因為任何原因而離線，則無法在雲端中更新內部部署的變更，而且可能會導致使用者存取問題。 因此，請務必定義容錯移轉策略，讓系統管理員能夠在同步伺服器離線之後快速繼續同步處理。 這類策略可以分為下列類別：

- **在預備模式中部署 Azure AD Connect Server (s) ** -可讓系統管理員透過簡單的設定參數將預備伺服器「升級」至生產環境。
- **使用虛擬化** -如果 Azure AD connect 部署在虛擬機器 (VM) 中，系統管理員可以利用其虛擬化堆疊來即時移轉或快速重新部署 VM，因此會繼續同步處理。

如果您的組織缺少進行同步處理的嚴重損壞修復和容錯移轉策略，您就不需要在預備模式中部署 Azure AD Connect。 同樣地，如果您的生產和預備設定之間有不相符的情況，您應該重新基準 Azure AD Connect 預備模式，以符合生產環境設定，包括軟體版本和設定。

![Azure AD Connect 預備模式設定的螢幕擷取畫面](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>保持最新狀態

Microsoft 會定期 Azure AD Connect 更新。 保持最新版本，以利用每個新版本所提供的效能改進、bug 修正及新功能。

如果您的 Azure AD Connect 版本超過六個月，您應該升級至最新版本。

#### <a name="source-anchor"></a>來源錨點

使用 **consistencyguid** 作為 [來源錨點](../hybrid/plan-connect-design-concepts.md) ，可讓您更輕鬆地跨樹系和網域來遷移物件，這在 AD 網域匯總/清除、合併、收購及 divestitures 中很常見。

如果您目前使用 **ObjectGuid** 作為來源錨點，建議您切換為使用 **ConsistencyGuid**。

#### <a name="custom-rules"></a>自訂規則

Azure AD Connect 自訂規則可讓您控制內部部署物件與雲端物件之間的屬性流程。 不過，過度使用或濫用自訂規則可能會導致下列風險：

- 疑難排解複雜度
- 跨物件執行複雜的作業時效能降低
- 實際執行伺服器與預備伺服器之間的設定分歧率較高
- 升級時的額外負荷 Azure AD Connect 如果在內建規則所使用的優先順序大於 100 (中建立自訂規則) 

如果您使用過於複雜的規則，您應該調查複雜性的原因，並找出簡化的機會。 同樣地，如果您已建立優先順序值超過100的自訂規則，您應該修正這些規則，使其不會有風險或與預設的設定衝突。

誤用自訂規則的範例包括：

- **補償目錄中** 的中途資料-在此情況下，建議您與 AD 小組的擁有者合作，並清除目錄中的資料作為補救工作，並調整進程以避免 reintroduction 不正確的資料。
- **個別使用者的一次性補救** -常見的情況是找出特殊案例極端值的規則，通常是因為特定使用者有問題。
- **Overcomplicated "CloudFiltering"** -雖然減少物件的數目是很好的作法，但是使用許多同步處理規則來建立和 Overcomplicated 同步範圍的風險。 如果有複雜的邏輯可包含/排除 OU 篩選以外的物件，建議您在同步之外處理此邏輯，並以簡單的 "cloudFiltered" 屬性（可透過簡單的同步處理規則）來為物件加上標籤。

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD Connect 設定文件管理器

您可以使用 [ [Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter) 設定] 工具集來產生 Azure AD Connect 安裝的檔，讓您更瞭解同步處理設定、建立正確的許可權，以及知道當您套用新的組建或設定 Azure AD Connect 或新增或更新的自訂同步處理規則時所變更的專案。 此工具的目前功能包括：

- Azure AD Connect 同步的完整設定檔。
- 說明兩個 Azure AD Connect 同步伺服器的設定中的任何變更，或從指定設定基準進行變更的檔。
- 產生 PowerShell 部署腳本，以將同步處理規則差異或自訂從一部伺服器遷移至另一部伺服器。

## <a name="assignment-to-apps-and-resources"></a>指派給應用程式和資源

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Microsoft 雲端服務以群組為基礎的授權

Azure Active Directory 透過 Microsoft 雲端服務以 [群組為基礎的授權](./active-directory-licensing-whatis-azure-portal.md) ，簡化授權的管理。 如此一來，IAM 可將這些群組的群組基礎結構和委派管理提供給組織中適當的小組。 在 Azure AD 中設定群組成員資格的方式有很多種，包括：

- **從內部部署環境同步** 處理的群組可以來自內部部署目錄，這可能適用于已建立群組管理程式的組織，可以擴充以在 Microsoft 365 中指派授權。

- 以使用者屬性為基礎的運算式（例如部門等於 "sales"），可以在雲端中建立以**屬性為基礎的/動態**群組。 Azure AD 維護群組的成員，使其與定義的運算式保持一致。 使用這種類型的群組進行授權指派，可啟用以屬性為基礎的授權指派，適合在其目錄中具有高資料品質的組織使用。

- **委派的擁有權** -您可以在雲端中建立群組，並可指定擁有者。 如此一來，您就可以讓商務擁有者（例如，共同作業小組或 BI 團隊）能夠定義誰應該擁有存取權。

如果您目前使用手動程式將授權和元件指派給使用者，我們建議您執行以群組為基礎的授權。 如果您目前的進程未監視授權錯誤，或指派了哪些與可用的功能，您就應該定義解決授權錯誤和監視授權指派的程式改進。

授權管理的另一個層面是 (授權) 元件的定義，應根據組織中的工作功能來啟用。 將存取權授與不必要的服務方案，可能會導致使用者在 Office 入口網站中看到它們尚未經過定型或不應使用的工具。 它可以推動其他服務中心磁片區、不必要的布建，並將您的合規性和治理視為風險，例如，將商務用 OneDrive 布建給可能不允許共用內容的個人。

使用下列指導方針，將服務方案定義給使用者：

- 系統管理員應定義服務方案的「配套」，以根據使用者的角色提供給使用者，例如，以白色環的背景工作角色與樓層工作者。
- 依叢集建立群組，並將授權指派給服務方案。
- （選擇性）您可以定義屬性來保留使用者的封裝。

> [!IMPORTANT]
> Azure AD 中以群組為基礎的授權引進了授權錯誤狀態的使用者概念。 如果您發現任何授權錯誤，則應該立即 [找出並解決](../enterprise-users/licensing-groups-resolve-problems.md) 任何授權指派的問題。

![自動產生電腦螢幕描述的螢幕擷取畫面](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>生命週期管理

如果您目前使用依賴內部部署基礎結構的工具，例如 [Microsoft Identity Manager](/microsoft-identity-manager/) 或協力廠商系統，我們建議您從現有的工具卸載指派、執行群組型授權，並根據 [群組](../enterprise-users/licensing-group-advanced.md#use-group-based-licensing-with-dynamic-groups)定義群組生命週期管理。 同樣地，如果您現有的程式未考慮離開組織的新員工或員工，您應該根據動態群組部署以群組為基礎的授權，並定義群組成員資格生命週期。 最後，如果以群組為基礎的授權是針對缺少生命週期管理的內部部署群組進行部署，請考慮使用雲端群組來啟用委派擁有權或以屬性為基礎的動態成員資格等功能。

### <a name="assignment-of-apps-with-all-users-group"></a>指派具有「所有使用者」群組的應用程式

資源擁有者可能會認為「 **所有使用者** 」群組只包含 **企業員工** ，但實際上可能同時包含 **企業員工** 和 **來賓**。 如此一來，當您使用 [ **所有使用者** ] 群組來指派應用程式，並授與資源（例如 SharePoint 內容或應用程式）的存取權時，您應該特別注意這一點。

> [!IMPORTANT]
> 如果已啟用 [ **所有使用者** ] 群組，並用於條件式存取原則、應用程式或資源指派，如果您不想讓群組包含來賓使用者，請務必 [保護該群組](../external-identities/use-dynamic-groups.md) 。 此外，您應該建立並指派給包含 **企業員工** 的群組，以修正您的授權指派。 相反地，如果您發現 [ **所有使用者** ] 群組已啟用，但未用來授與資源的存取權，請確定您組織的操作指引是刻意使用該群組 (，其中包含 **企業員工** 和 **來賓**) 。

### <a name="automated-user-provisioning-to-apps"></a>自動使用者布建至應用程式

[自動使用者](../app-provisioning/user-provisioning.md) 布建至應用程式是在多個系統之間建立一致的布建、解除布建和生命週期的最佳方式。

如果您目前以臨機操作方式布建應用程式，或是使用 CSV 檔案、JIT 或不能解決生命週期管理的內部部署解決方案，我們建議您針對支援的應用程式使用 Azure AD 來 [執行應用程式](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) 布建，並針對 Azure AD 尚未支援的應用程式定義一致模式。

![Azure AD 布建服務](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect delta sync cycle 基準

請務必瞭解您組織中的變更量，並確定不會花太多時間來進行可預測的同步處理時間。

[預設差異同步](../hybrid/how-to-connect-sync-feature-scheduler.md)處理頻率為30分鐘。 如果差異同步處理所花費的時間超過30分鐘，或預備和生產環境之間的差異同步處理效能有顯著的差異，您應該調查並查看 [影響 Azure AD Connect 效能的因素](../hybrid/plan-connect-performance-factors.md)。

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>建議閱讀的 Azure AD Connect 疑難排解

- [使用 IdFix 工具準備目錄屬性以便與 Microsoft 365 同步處理](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect：針對同步處理期間的錯誤進行疑難排解](../hybrid/tshoot-connect-sync-errors.md)

## <a name="summary"></a>摘要

安全的身分識別基礎結構有五個層面。 這份清單可協助您快速找出並採取必要的動作，以保護及管理您組織中的身分識別和其權利的生命週期。

- 將擁有者指派給主要工作。
- 找出並解決同步處理問題。
- 定義嚴重損壞修復的容錯移轉策略。
- 簡化授權和應用程式指派的管理。
- 自動將使用者布建到應用程式。

## <a name="next-steps"></a>後續步驟

開始使用 [驗證管理檢查和動作](active-directory-ops-guide-auth.md)。