---
title: Azure Active Directory 身分識別與存取管理作業參考指南
description: 此操作參考指南說明保護身分識別和存取管理作業時應採取的檢查和動作
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
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79298609"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Azure Active Directory 身分識別與存取管理作業參考指南

[Azure AD 作業參考指南](active-directory-ops-guide-intro.md)的這一節說明您應該考慮的檢查和動作，以保護和管理身分識別和其指派的生命週期。

> [!NOTE]
> 這些建議是在發行日期之後的最新版本，但可能會隨著時間而改變。 組織應持續評估其身分識別實務，因為 Microsoft 產品和服務會隨著時間而演進。

## <a name="key-operational-processes"></a>關鍵操作程式

### <a name="assign-owners-to-key-tasks"></a>將擁有者指派給主要工作

管理 Azure Active Directory 需要持續執行重要的作業工作，以及可能不屬於首度發行專案的進程。 您必須設定這些工作來維護您的環境，這仍然很重要。 主要工作和其建議的擁有者包括：

| Task | 擁有者 |
| :- | :- |
| 定義處理常式如何建立 Azure 訂用帳戶 | 因組織而異 |
| 決定誰取得 Enterprise Mobility + Security 授權 | IAM 作業小組 |
| 決定誰取得 Office 365 授權 | 生產力小組 |
| 決定誰取得其他授權，例如 Dynamics、VSO | 應用程式擁有者 |
| 指派授權 | IAM 作業小組 |
| 針對授權指派錯誤進行疑難排解和補救 | IAM 作業小組 |
| 在 Azure AD 中將身分識別布建至應用程式 | IAM 作業小組 |

當您檢查清單時，您可能會發現需要為遺漏擁有者的工作指派擁有者，或為擁有者未與上述建議一致的工作調整擁有權。

#### <a name="assigning-owners-recommended-reading"></a>指派擁有者建議閱讀

- [在 Azure Active Directory 中指派系統管理員角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的治理](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>內部部署身分識別同步處理

### <a name="identify-and-resolve-synchronization-issues"></a>識別並解決同步處理問題

Microsoft 建議您對內部部署環境中可能會導致雲端同步處理問題的問題有良好的基準和瞭解。 由於[IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)和[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health)之類的自動化工具可能會產生大量的誤報，因此建議您藉由清除發生錯誤的物件，來識別已尚待發揮超過100天的同步處理錯誤。 長期無法解析的同步處理錯誤可能會產生支援事件。 針對[同步處理期間的錯誤進行疑難排解](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors)提供了不同類型的同步錯誤的總覽，其中一些可能的案例會導致這些錯誤，以及可能的方法來修正錯誤。

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD Connect 同步設定

若要啟用所有混合式體驗、以裝置為基礎的安全性狀態，並與 Azure AD 整合，您必須同步處理員工用來登入其桌面的使用者帳戶。

如果您未同步處理樹系使用者登入，則應該將同步處理變更為來自適當的樹系。

#### <a name="synchronization-scope-and-object-filtering"></a>同步處理範圍和物件篩選

移除不需要同步處理之物件的已知值區具有下列操作優點：

- 較少的同步處理錯誤來源
- 更快的同步處理週期
- 較少「垃圾」從內部部署繼續執行，例如，對不在雲端中相關的內部部署服務帳戶的全域通訊清單的污染

> [!NOTE]
> 如果您發現要匯入許多未匯出至雲端的物件，您應該依 OU 或特定屬性進行篩選。

要排除的物件範例包括：

- 雲端應用程式未使用的服務帳戶
- 不打算在雲端案例中使用的群組，例如用來授與資源存取權的
- 屬於外部身分識別的使用者或連絡人，這些身分識別會以 Azure AD 表示 B2B 共同作業
- 員工不打算存取雲端應用程式的電腦帳戶，例如伺服器

> [!NOTE]
> 如果單一人為身分識別有多個從舊版網域遷移、合併或取得的帳戶進行布建，您應該只同步處理使用者每天所使用的帳戶，例如，他們用來登入其電腦的帳戶。

在理想的情況下，您會想要在減少要同步處理的物件數目和規則的複雜性之間達到平衡。 一般來說，OU/容器[篩選](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering)和簡單的屬性對應（cloudFiltered 屬性）之間的組合，是有效的篩選組合。

> [!IMPORTANT]
> 如果您在生產環境中使用群組篩選，則應該轉換成另一個篩選方法。

#### <a name="sync-failover-or-disaster-recovery"></a>同步容錯移轉或嚴重損壞修復

Azure AD Connect 在布建流程中扮演重要角色。 如果同步處理伺服器因任何原因而離線，內部部署的變更將無法在雲端中更新，而且可能會導致使用者存取問題。 因此，請務必定義容錯移轉策略，讓系統管理員能夠在同步伺服器離線後快速繼續同步處理。 這類策略可能會分成下列類別：

- **在預備模式中部署 Azure AD Connect 伺服器**-可讓系統管理員透過簡單的設定交換器，將預備伺服器「升級」到生產環境。
- **使用虛擬化**-如果 Azure AD connect 部署在虛擬機器（VM）中，系統管理員可以利用其虛擬化堆疊進行即時移轉，或快速重新部署 VM，因而繼續同步處理。

如果您的組織缺少適用于同步處理的嚴重損壞修復和容錯移轉策略，您就不應該想要在預備模式中部署 Azure AD Connect。 同樣地，如果您的生產與預備設定不相符，您應該重新基準 Azure AD Connect 預備模式，以符合生產設定，包括軟體版本和設定。

![Azure AD Connect 預備模式設定的螢幕擷取畫面](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>保持最新狀態

Microsoft 會定期 Azure AD Connect 更新。 隨時保持最新的優勢，以利用每個新版本所提供的效能改進、bug 修正和新功能。

如果您的 Azure AD Connect 版本超過六個月，您應該升級至最新版本。

#### <a name="source-anchor"></a>來源錨點

使用**consistencyguid**作為[來源錨點](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)可讓您更輕鬆地跨樹系和網域遷移物件，這在 AD 網域合併/清除、合併、收購和 divestitures 中很常見。

如果您目前使用**ObjectGuid**作為來源錨點，建議您切換為使用**ConsistencyGuid**。

#### <a name="custom-rules"></a>自訂規則

Azure AD Connect 自訂規則提供控制內部部署物件和雲端物件之間屬性流程的能力。 不過，超或濫用自訂規則可能會導致下列風險：

- 疑難排解複雜度
- 跨物件執行複雜作業時的效能降低
- 實際執行伺服器與預備伺服器之間設定的分歧機率較高
- 如果在優先順序大於100（由內建規則所使用）中建立自訂規則，則升級 Azure AD Connect 時的額外負荷

如果您使用過於複雜的規則，您應該調查複雜性的原因，並找出簡化的機會。 同樣地，如果您已建立優先順序值超過100的自訂規則，您應該修正這些規則，使其不會有風險或與預設集合衝突。

誤用自訂規則的範例包括：

- **補償目錄中**的中途資料-在此情況下，建議與 AD 小組的擁有者合作，並將目錄中的資料清除為補救工作，並調整進程以避免 reintroduction 不正確的資料。
- **個別使用者的一次性修復**-一般會尋找特殊案例極端值的規則，通常是因為特定使用者發生問題。
- **Overcomplicated "CloudFiltering"** -雖然減少物件數目是不錯的作法，但使用許多同步處理規則建立和 Overcomplicated 同步處理範圍會有風險。 如果有複雜的邏輯可以包含/排除 OU 篩選以外的物件，建議您在同步處理外使用此邏輯，並以簡單的 "cloudFiltered" 屬性（可與簡單的同步處理規則）為物件加上標籤。

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD Connect 設定文件管理器

[ [Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter)設定] [檔] 是一種工具，可用來產生 Azure AD Connect 安裝的檔，讓您更瞭解同步處理設定、建立適當專案的信心，以及知道當您套用新的 Azure AD Connect 組建或設定或新增或更新的自訂同步處理規則時，所做的變更。 此工具的目前功能包括：

- Azure AD Connect 同步處理的完整設定檔。
- 在兩個 Azure AD Connect 同步伺服器的設定中，或從指定的設定基準進行變更的檔。
- 產生 PowerShell 部署腳本，以將同步處理規則差異或自訂從一部伺服器遷移至另一部伺服器。

## <a name="assignment-to-apps-and-resources"></a>指派給應用程式和資源

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Microsoft 雲端服務的以群組為基礎的授權

Azure Active Directory 透過 Microsoft 雲端服務的[群組型授權](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)，簡化授權的管理。 如此一來，IAM 就能將這些群組的群組基礎結構和委派管理提供給組織中適當的小組。 有多種方式可以在 Azure AD 中設定群組的成員資格，包括：

- **從內部部署進行同步**處理-群組可以來自內部部署目錄，這對於已建立群組管理程式以在 office 365 中指派授權的組織而言非常適合。

- 根據使用者屬性（例如，部門等於「銷售」），可以在雲端中建立以**屬性為基礎的/動態**群組。 Azure AD 維護群組的成員，讓它與定義的運算式保持一致。 使用這種群組進行授權指派可啟用以屬性為基礎的授權指派，這適用于其目錄中具有高資料品質的組織。

- **委派擁有權**-群組可以在雲端中建立，而且可以指定擁有者。 如此一來，您就可以讓商務擁有者（例如，共同作業小組或 BI 小組）定義誰應該具有存取權。

如果您目前使用手動程式將授權和元件指派給使用者，我們建議您執行以群組為基礎的授權。 如果您目前的進程未監視授權錯誤或指派的內容與可用的版本，您應該為處理授權錯誤和監視授權指派的程式定義改善。

授權管理的另一個層面是服務方案的定義（授權的元件），應該根據組織中的工作功能來啟用。 將存取權授與不必要的服務方案，可能會導致使用者在 Office 入口網站中看到他們尚未訓練或不應該使用的工具。 它可以推動額外的服務台量、不必要的布建，以及讓您的合規性和治理有風險，例如，將商務用 OneDrive 布建給可能不允許共用內容的個人。

使用下列指導方針來定義使用者的服務方案：

- 系統管理員應該根據其角色來定義要提供給使用者的服務方案「配套」，例如，白色環工作者與樓層工作者。
- 依叢集建立群組，並將授權指派給服務方案。
- （選擇性）您可以定義屬性來保留使用者的封裝。

> [!IMPORTANT]
> Azure AD 中以群組為基礎的授權會引進授權錯誤狀態的使用者概念。 如果您注意到任何授權錯誤，則應該立即[找出並解決](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems)任何授權指派的問題。

![自動產生電腦螢幕描述的螢幕擷取畫面](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>生命週期管理

如果您目前使用的工具（例如[Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/)或協力廠商系統）依賴內部部署基礎結構，建議您從現有的工具卸載指派、執行以群組為基礎的授權，並根據[群組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups)定義群組生命週期管理。 同樣地，如果您現有的處理常式不會考慮離開組織的新員工或員工，您應該根據動態群組部署以群組為基礎的授權，並定義群組成員資格生命週期。 最後，如果以群組為基礎的授權是針對缺少生命週期管理的內部部署群組而部署，請考慮使用雲端群組來啟用功能，例如委派擁有權或以屬性為基礎的動態成員資格。

### <a name="assignment-of-apps-with-all-users-group"></a>指派具有「所有使用者」群組的應用程式

資源擁有者可能會認為 [**所有使用者**] 群組只包含**企業員工**，因為他們可能實際上同時包含**企業員工**和**來賓**。 因此，當您使用 [**所有使用者**] 群組進行應用程式指派，以及授與資源（例如 SharePoint 內容或應用程式）的存取權時，您應該特別注意。

> [!IMPORTANT]
> 如果已啟用 [**所有使用者**] 群組並用於條件式存取原則、應用程式或資源指派，如果您不想要讓它包含來賓使用者，請務必[保護群組](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups)。 此外，您應該藉由建立並指派給僅包含**企業員工**的群組，來修正授權指派。 另一方面，如果您發現 [**所有使用者**] 群組已啟用，但並未用來授與資源的存取權，請確定您組織的操作指引是刻意使用該群組（包括**企業員工**和**來賓**）。

### <a name="automated-user-provisioning-to-apps"></a>應用程式的自動化使用者布建

應用程式的[自動化使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建是在多個系統上建立一致的身分識別、解除布建和生命週期的最佳方式。

如果您目前是以臨機操作方式布建應用程式，或使用 CSV 檔案、JIT 或未解決生命週期管理的內部部署解決方案，建議您使用 Azure AD 針對支援的應用程式來布建[應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application)，並為 Azure AD 尚未支援的應用程式定義一致的模式。

![Azure AD 布建服務](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect 差異同步處理週期基準

請務必瞭解您組織中的變更量，並確定不會花太多時間來擁有可預測的同步處理時間。

[預設的差異同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler)處理頻率為30分鐘。 如果差異同步處理的持續時間超過30分鐘，或預備與生產環境的差異同步處理效能有顯著差異，您應該調查並檢查[影響 Azure AD Connect 效能的因素](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors)。

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect 疑難排解建議的閱讀

- [使用 IdFix 工具（Office 365）準備與 Office 365 同步處理的目錄屬性](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect：在同步處理期間針對錯誤進行疑難排解](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>摘要

安全身分識別基礎結構有五個層面。 這份清單可協助您快速尋找並採取必要的動作，以保護及管理您組織中身分識別及其權利的生命週期。

- 將擁有者指派給主要工作。
- 尋找並解決同步處理問題。
- 定義嚴重損壞修復的容錯移轉策略。
- 簡化授權和應用程式指派的管理。
- 自動化使用者布建至應用程式。

## <a name="next-steps"></a>後續步驟

開始使用[驗證管理檢查和動作](active-directory-ops-guide-auth.md)。
