---
title: 規劃 Azure Active Directory 存取權審核部署
description: 成功存取審查部署的規劃指南
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/14/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: b218f4c94e33d4a91b3981c66d3ac052c6caf7e2
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318121"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>規劃 Azure Active Directory 存取審查部署

[Azure Active Directory (Azure AD) 的存取權評論](access-reviews-overview.md) ，藉由管理 [資源存取生命週期](identity-governance-overview.md)來協助您的組織讓網路更安全。 有了存取權評論，您可以：

* 排程定期審核或執行臨機操作評論，以查看誰可以存取特定資源，例如應用程式和群組

* 追蹤見解、合規性或原則原因的評論

* 將評論委派給特定的系統管理員、商務擁有者或使用者，這些使用者可以自行證明是否需要繼續存取

* 使用深入解析來有效率地判斷使用者是否應該繼續存取

* 將審核結果自動化，例如移除使用者的資源存取權

  ![顯示存取權審核流程的圖表。](./media/deploy-access-review/1-planning-review.png)

存取評論是 [Azure AD Identity Governance](identity-governance-overview.md) 的功能。 另一項功能是 [權利管理](entitlement-management-overview.md)、 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 和 [使用](../conditional-access/terms-of-use.md)規定。 它們一起可協助組織解決下列四個問題：

* 哪些使用者應該存取哪些資源？

* 使用者利用該存取權來做什麼？

* 管理存取權是否有有效的組織控制項？

* 稽核人員可以確認這些控制項有用嗎？

規劃存取權審查部署是確保您為組織中的使用者達成所需治理策略的必要項。

### <a name="key-benefits"></a>主要權益

啟用存取權評論的主要優點包括：

* **控制協同**作業。 存取評論可讓組織管理其使用者所需之所有資源的存取權。 當他們的使用者共用及共同作業時，組織可以確保資訊只在授權的使用者中。

* **管理風險**。 存取審查可提供組織一種方式來審查對資料和應用程式的存取，降低資料洩漏和資料溢出的風險。 這包括可定期審核外部夥伴存取公司資源的功能。 

* **解決合規性和治理**。 您可以使用存取權審核來管理和重新認證群組、應用程式和網站的存取生命週期。 您可以控制組織特定的合規性或風險相關應用程式的追蹤評論。 

* **降低成本**。 存取權評論是在雲端中建立的，並以原生方式搭配使用雲端資源（例如群組、應用程式和存取套件）。 使用存取權審核比建立您自己的工具或升級內部部署工具組更便宜。

### <a name="training-resources"></a>訓練資源

當您瞭解存取權評論時，下列影片可能會很有用：

* [Azure AD 中的存取權評論為何？](https://youtu.be/kDRjQQ22Wkk)

* [如何在 Azure AD 中建立存取權審核](https://youtu.be/6KB3TZ8Wi40)

* [如何在 Azure AD 中啟用存取權審核](https://youtu.be/X1SL2uubx9M)

* [如何使用我的存取權來檢查存取權](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>授權

您需要有效的 Azure AD Premium (P2) 授權給每個人，而非全域系統管理員或使用者系統管理員，他們會建立或執行存取權審核。 如需詳細資訊，請參閱 [存取評論授權需求](access-reviews-overview.md)。

您也可能需要其他身分識別治理功能，例如 [權利生命週期管理](entitlement-management-overview.md) 或特殊許可權身分識別管理。 在這種情況下，您可能也需要相關的授權。 如需詳細資訊，請參閱 [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="plan-the-access-reviews-deployment-project"></a>規劃存取權審核部署專案

請考慮您的組織需要判斷在您的環境中部署存取權審核的策略。

### <a name="engage-the-right-stakeholders"></a>包含正確的專案關係人

當技術專案失敗時，通常是因為對影響、結果與責任的預期不符所造成。 若要避免這些錯誤，請[確保您吸引合適的專案關係人](../fundamentals/active-directory-deployment-plans.md) \(英文\)，且專案角色是明確的。

針對存取權評論，您可能會在組織內包含下列小組的代表：

* **It 管理** 管理您的 it 基礎結構，並管理您的雲端投資和軟體即服務 (SaaS) 應用程式。 此小組將：

   * 檢查基礎結構和應用程式的特殊許可權存取，包括 Microsoft 365 和 Azure AD。

   * 在用來維護例外狀況清單或 IT 試驗專案的群組上，排程並執行存取權審核，以維護最新的存取清單。

   * 確定透過服務主體進行程式設計 (腳本) 對資源的存取受到管制和審核。

* **開發小組** 會為您的組織建立和維護應用程式。 此小組將：

   * 控制誰可以存取和管理 SaaS、PaaS 和 IaaS 資源中組成開發解決方案的元件。

   * 管理可存取應用程式和工具進行內部應用程式開發的群組。

   * 需要具有可存取生產軟體的特殊許可權身分識別，或為您的客戶託管的解決方案

* **業務單位** 會管理專案和應用程式。 此小組將： 

   * 對內部和外部使用者進行審核，並核准或拒絕對群組和應用程式的存取。

   * 排程並執行評論，以證明員工和外部身分識別（例如商務夥伴）的持續存取權。

* **公司治理** 可確保組織遵循內部原則並遵守法規。 此小組將：

   * 要求或排程新的存取權評論。

   * 評估檢查存取權的程式和程式，包括符合規範的檔和記錄。

   * 查看大部分重要資源過去評論的結果。

> [!NOTE]
> 針對需要手動評估的評論，請務必規劃適當的審核者，並審查符合您的原則和合規性需求的週期。 如果審核週期太過頻繁，或太少審核者，品質可能會遺失，或太多人可能會有存取權。 

### <a name="plan-communications"></a>規劃溝通

通訊對於任何新商務程式的成功都很重要。 主動與使用者溝通其體驗將如何及何時會變更，以及如何在遇到問題時獲得支援。 

#### <a name="communicate-changes-in-accountability"></a>傳達責任變更

存取審查支援對企業擁有者的持續存取責任轉移和採取行動。 將存取決策從 IT 驅動，以取得更精確的存取決策。 這是資源擁有者的責任和責任的文化變更。 主動傳達此變更，並確保資源擁有者已定型，而且能夠使用深入解析來做出良好的決策。

很明顯地，它會希望能掌控所有基礎結構相關的存取決策，以及特殊許可權的角色指派。 

#### <a name="customize-email-communication"></a>自訂電子郵件通訊

當您排定評論時，會提名將執行這一評論的使用者。 然後，這些審核者會收到指派給他們的新評論的電子郵件通知，以及在指派給他們的評論到期前的提醒。

系統管理員可以選擇在評論到期前的一半方向傳送此通知，或在到期前傳送一天。 

傳送給審核者的電子郵件可以自訂為包含自訂的簡短訊息，讓他們可以在評論上採取行動。 建議您使用其他文字來：

* 包含個人訊息給審核者，讓他們瞭解您的合規性或 IT 部門所傳送的訊息。

* 針對評論的期望以及其他參考或訓練材料，包含超連結或參考內部資訊。

* 包含有關[如何執行自我審核存取的](review-your-access.md)指示連結。 

  ![檢閱者電子郵件](./media/deploy-access-review/2-plan-reviewer-email.png)

選取 [開始審核] 時，會將審核者導向至 [myAccess 入口網站](https://myapplications.microsoft.com/) ，以進行群組和應用程式存取權審核。 入口網站會概述可存取他們所查看之資源的所有使用者，以及根據上次登入和存取訊號的系統建議。

### <a name="plan-a-pilot"></a>規劃試驗

我們鼓勵客戶一開始先試驗小型群組的存取權，並以非關鍵性的資源為目標。 試驗可協助您視需要調整流程和通訊，並提高使用者和審核者的能力，以符合安全性和合規性需求。

在您的試驗中，我們建議您：

* 從不自動套用結果的審核開始，而且您可以控制含意。

* 請確定所有使用者都有列在 Azure AD 中的有效電子郵件地址，而且他們會收到電子郵件通訊，以採取適當的動作。 

* 記錄在試驗過程中移除的任何存取權，以防您需要快速還原。

* 監視 audit 記錄檔，以確保所有事件都能正確地進行審核。

如需詳細資訊，請參閱 [試驗的最佳作法](../fundamentals/active-directory-deployment-plans.md)。

## <a name="introduction-to-access-reviews"></a>存取權評論簡介

本節介紹您在規劃評論之前應該知道的存取權審查概念。

### <a name="what-resource-types-can-be-reviewed"></a>可以審核哪些資源類型？

一旦您將組織的資源與 Azure AD (（例如使用者、應用程式和) 群組）整合之後，就可以進行管理和檢查。 

檢查的一般目標包括：

* [整合了 Azure AD 的應用程式，可進行單一登入](../manage-apps/what-is-application-management.md) (例如 SaaS、企業營運) 。

* 群組 [成員資格](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) (同步處理至 Azure AD，或在 Azure AD 或 Microsoft 365 中建立，包括 Microsoft 小組) 。

* 將資源群組、應用程式和網站群組 (群組、應用程式和網站的[存取套件](./entitlement-management-overview.md)，) 成單一套件來管理存取權。

* [Azure AD 角色和 Azure 資源角色，](../privileged-identity-management/pim-resource-roles-assign-roles.md) 如 Privileged Identity Management 中所定義。

### <a name="who-will-create-and-manage-access-reviews"></a>誰將建立及管理存取權評論？

建立、管理或讀取存取權檢查所需的系統管理角色，取決於所要檢查的資源類型。 下表代表每個資源類型所需的角色：

| 資源類型| 建立及管理存取權評論 (建立者) | 讀取存取權審核結果 |
| - | - | -|
| 群組或應用程式| 全域管理員 <p>使用者管理員| 建立者和安全性系統管理員 |
| Azure AD 中的特殊許可權角色| 全域管理員 <p>特殊權限角色管理員| 建立者 <p>安全性讀取者<p>安全性系統管理員 |
| Azure 中的特殊許可權角色 (資源) | 全域管理員<p>使用者管理員<p>資源擁有者| 建立者 |
| 存取套件| 全域管理員<p>存取套件的建立者| 僅限全域管理員 |


如需詳細資訊，請參閱 [Azure Active Directory 中的管理員角色權限](../users-groups-roles/directory-assign-admin-roles.md)。

### <a name="who-will-review-the-access-to-the-resource"></a>誰將檢查資源的存取權？

存取權審核的建立者會在建立時決定要執行審核的時間。 一旦啟動審核之後，就無法變更此設定。 審核者會以三個角色表示：

* 資源擁有者，也就是資源的商務擁有者。

* 一組個別選取的委派，如同存取權審核管理員所選取。

* 每個使用者都能自行證明其持續存取的需求。

建立存取權審查時，系統管理員可以選擇一或多個審核者。 所有審核者都可以開始並執行評論，選擇使用者以繼續存取資源或移除資源。 

### <a name="components-of-an-access-review"></a>存取權檢查的元件

在執行存取權審核之前，您應該先規劃與您組織相關的評論類型。 若要這樣做，您必須針對您想要審核的內容，以及要根據這些評論採取的動作進行商務決策。

若要建立存取權稽核原則，您必須具有下列資訊。

* ) 要檢查什麼資源 (？

* 正在審核其存取權。

* 進行審核的頻率為何？

* 誰將執行評論？

   * 他們將如何通知他們進行評論？

   * 要將哪些時程表強制進行審核？

* 應根據審核措施強制執行哪些自動動作？

   * 如果審核者沒有及時回應，會發生什麼事？

* 根據評論，將採取哪些手動動作作為結果？

* 應根據採取的動作傳送哪些通訊？


**範例存取審核方案**

| 元件| 值 |
| - | - |
| **要檢查的資源**| 存取 Microsoft Dynamics |
| **審查頻率**| 每月 |
| **誰執行審核**| Dynamics business group 方案經理 |
| **通知**| 在審核前24小時內的電子郵件給別名 Dynamics-Pms<p>包含鼓勵審核者的自訂訊息，以保護他們的購買 |
| **時間表**| 通知的48小時 |
|**自動動作**| 從安全性群組 dynamics 存取中移除使用者，以移除在90天內沒有互動式登入的任何帳戶存取權。 <p>*如果未在時間軸內檢查，請執行動作。* |
| **手動動作**| 審核者可能會在需要時，執行自動動作之前的移除核准。 |
| **通訊**| 傳送內部 (成員) 移除電子郵件的使用者，說明已移除電子郵件，以及如何重新取得存取權。 |


 

### <a name="automate-actions-based-on-access-reviews"></a>根據存取權審核來自動化動作

您可以設定 [自動將結果套用至資源] 選項，以選擇是否要讓存取移除自動化。

  ![規劃存取權評論](./media/deploy-access-review/3-automate-actions-settings.png)

一旦完成審核並結束，審核者未核准的使用者將會自動從資源中移除，或保留以繼續存取。 這可能表示移除其群組成員資格、其應用程式指派，或撤銷其許可權以提升為特殊許可權角色。

取得建議

這些建議會在審核者體驗中顯示為審核者，並指出人員上次登入租使用者的時間，或最後一次存取應用程式。 此資訊可協助審核者進行正確的存取決策。 選取 [取得建議] 將會遵循存取權審核的建議。 在存取權檢查結束時，系統會自動為審核者未回應的使用者套用這些建議。

建議是以存取權審查中的準則為基礎。 例如，如果您將審核設定為移除沒有互動式登入的90天的存取權，則會建議您移除符合該準則的所有使用者。 Microsoft 持續致力於強化建議。 

### <a name="review-guest-user-access"></a>檢查來賓使用者存取權

使用存取評論來檢查和清除來自外部組織的共同作業合作夥伴身分識別。 設定每個合作夥伴的評論可能滿足合規性需求。

您可以透過下列其中一個動作，將外部身分識別授與公司資源的存取權：

* 新增至群組 

* 受邀給小組 

* 指派給企業應用程式或存取套件

* 指派 Azure AD 或 Azure 訂用帳戶中的特殊許可權角色

請參閱 [範例腳本](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)。 腳本會顯示如何使用受邀加入租使用者的外部身分識別。 您可以在 Azure AD 中看到外部使用者的群組成員資格、角色指派和應用程式指派。 腳本不會顯示 Azure AD 以外的任何指派，例如對 Sharepoint 資源的直接許可權指派，而不使用群組。

建立群組或應用程式的存取權審查時，您可以選擇讓審核者專注于具有存取權的所有人，或僅限來賓使用者。 藉由選取 [僅來賓使用者]，審核者會從具有資源存取權的 Azure AD B2B 取得外部身分識別的集中清單。

 ![檢查來賓使用者](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> 這不會包含具有成員 userType 的外部成員。 這也不包含在 Azure AD B2B 共同作業之外受邀的使用者，例如可直接透過 SharePoint 存取共用內容的使用者。

## <a name="plan-access-reviews-for-access-packages"></a>規劃存取套件的存取權審核

[存取套件](entitlement-management-overview.md) 可以大幅簡化您的治理和存取稽核原則。 存取套件是所有資源的組合，其中包含使用者處理專案或執行其工作所需的存取權。 例如，您可能會想要建立存取套件，其中包含您組織中的開發人員所需的所有應用程式，或外部使用者應該擁有存取權的所有應用程式。 系統管理員或委派的存取套件管理員接著會將資源 (群組或應用程式) ，以及使用者對這些資源所需的角色進行分組。

[建立存取套件](entitlement-management-access-package-create.md)時，您可以建立一或多個存取原則，以設定使用者可要求存取套件的條件、核准程式的外觀，以及人員必須重新要求存取權的頻率。 存取權評論是在建立或編輯存取套件原則時設定。

開啟 [生命週期] 索引標籤，以向下滾動以存取評論。

 ![螢幕擷取畫面，顯示 [生命週期] 索引標籤中的 [編輯原則]。](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>規劃群組的存取權審核

除了存取套件之外，審核群組成員資格是管理存取權最有效的方式。 建議您透過 [安全性群組或 Microsoft 365 群組](../fundamentals/active-directory-manage-groups.md)指派資源的存取權，並將使用者新增至這些群組以取得存取權。

單一群組可以被授與所有適當資源的存取權。 您可以將群組存取權指派給個別資源，或指派給群組應用程式和其他資源的存取套件。 使用這個方法，您可以檢查群組的存取權，而不是個別存取每個應用程式的許可權。 

群組成員資格可透過下列方式進行審核： 

* 系統管理員

* 群組擁有者

* 選取的使用者，建立審核時的委派審核功能

* 群組的成員證明本身

### <a name="group-ownership"></a>群組所有權

我們建議群組擁有者審核成員資格，因為他們最好是知道誰需要存取權。 群組的擁有權與群組的類型不同：

在 Microsoft 365 中建立的群組，以及 Azure AD 有一或多個定義完善的擁有者。 在大部分的情況下，這些擁有者會為他們自己的群組建立完美的審核者，因為他們知道誰應該擁有存取權。 

例如，Microsoft 小組使用 Microsoft 365 群組作為基礎授權模型，將 SharePoint、Exchange、OneNote 或其他 Microsoft 365 服務中的資源存取權授與使用者。 小組的建立者會自動成為擁有者，且應負責證明至該群組的成員資格。 

在 Azure AD 入口網站中手動建立的群組，或透過 Microsoft Graph 的腳本，可能不一定會定義擁有者。 我們建議您在群組的「擁有者」區段中或透過 Graph，透過 Azure AD 入口網站來定義它們。

從內部部署同步處理的群組 Active Directory 不能擁有 Azure AD 的擁有者。 建立存取權審查時，您應該選取最適合決定其成員資格的個人。

> [!NOTE]
> 我們建議定義定義群組建立方式的商務原則，以確保明確地審核成員資格的群組擁有權和責任。 

### <a name="review-membership-of-exclusion-groups-in-ca-policies"></a>檢查 CA 原則中排除群組的成員資格 

有時候，條件式存取 (CA) 原則的設計目的是要讓您的網路保持安全，不應套用至所有使用者。 例如，只允許使用者在公司網路上登入的 CA 原則，可能不適用於大量傳輸的銷售團隊。 在這種情況下，銷售團隊成員會放入群組，而該群組將會從 CA 原則中排除。 

請定期檢查這類群組成員資格，因為排除的成員不符合需求時，會造成潛在的風險。

您可以 [使用 Azure AD 存取評論來管理從條件式存取原則中排除的使用者](conditional-access-exclusion.md)。

### <a name="review-external-users-group-memberships"></a>檢查外部使用者的群組成員資格

若要將手動工作和相關的潛在錯誤降至最低，請考慮使用 [動態群組](../users-groups-roles/groups-create-rule.md) ，根據使用者的屬性指派群組成員資格。 您可能會想要為外部使用者建立一或多個動態群組。 內部贊助者可以擔任群組中成員資格的審核者。 

注意：不會從租使用者中刪除從群組中移除的外部使用者。 

您可以透過手動方式或透過腳本來刪除租使用者中的租使用者。

### <a name="review-access-to-on-premises-groups"></a>檢查內部部署群組的存取權

存取權審核無法變更您從內部部署同步處理的群組成員資格與 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)。 這是因為授權來源是在內部部署。

您仍然可以使用存取權審核來排程和維護內部部署群組的定期評論。 審核者接著會在內部部署群組中採取動作。 此策略會將存取權評論保留為所有評論的工具。

您可以使用內部部署群組存取權審核的結果，並進一步處理這些結果，方法是：

* 從存取權審核下載 CSV 報表，並手動採取動作。

* 使用 Microsoft Graph 以程式設計方式存取已完成存取權檢查的結果和決策。

例如，若要存取 Windows AD 管理群組的結果，請使用此 [PowerShell 範例腳本](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)。 此腳本會概述必要的圖形呼叫，並匯出 Windows AD-PowerShell 命令以執行變更。

## <a name="plan-access-reviews-for-applications"></a>規劃應用程式的存取權審核 

當您檢查應用程式的存取權時，您會檢查員工的存取權，以及應用程式內資訊和資料的外部身分識別。 當您需要知道誰可以存取特定應用程式，而不是存取套件或群組時，請選擇查看應用程式。 

建議您在下列案例中規劃應用程式的評論：

* 使用者可直接存取 (群組或存取套件) 外部的應用程式。

* 應用程式會公開重要或敏感性資訊。

* 應用程式具有您必須證明的特定合規性需求。

* 您懷疑不適當的存取權。

若要建立應用程式的存取權審核，請設定 [需要使用者指派]？屬性設為 [是]。 如果設定為 [否]，您目錄中的所有使用者（包括外部身分識別）都可以存取應用程式，而您無法檢查應用程式的存取權。 

 ![規劃應用程式指派](./media/deploy-access-review/6-plan-applications-assignment-required.png)

然後，您必須指派您想要存取 [的使用者和群組](../manage-apps/assign-user-or-group-access-portal.md) 。 

### <a name="reviewers-for-an-application"></a>應用程式的審核者

存取權檢閱可針對群組成員或指派給應用程式的使用者執行。 Azure AD 中的應用程式不一定要有擁有者，這也是為什麼選取應用程式擁有者作為審核者的選項並不可能。 您可以進一步界定審查範圍，以只檢查指派給應用程式的來賓使用者，而不是檢查所有存取權。

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>規劃 Azure AD 和 Azure 資源角色的評論

[Privileged Identity Management (PIM) ](../privileged-identity-management/pim-configure.md) 可簡化企業在 Azure AD 中管理資源的特殊許可權存取的方式。 這會讓特殊許可權角色清單保持在 [Azure AD](../users-groups-roles/directory-assign-admin-roles.md) 和 [Azure 資源](../../role-based-access-control/built-in-roles.md) 中的較小，並提高目錄的整體安全性。

存取評論可讓審核者證明使用者是否仍然必須在角色中。 就像存取套件的存取權評論一樣，Azure AD 角色和 Azure 資源的評論也會整合到 PIM 系統管理員使用者體驗中。 建議您定期檢查下列角色指派：

* 全域管理員

* 使用者管理員

* 特殊權限驗證管理員

* 條件式存取系統管理員

* 安全性系統管理員

* 所有 Microsoft 365 和 Dynamics 服務管理角色

此處選取的角色包括永久和合格的角色。 

在 [檢閱者] 區段中，選取一或多個人員來檢閱所有使用者。 或者，您可以選擇讓成員檢閱自己的存取權。

 ![編輯原則](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>部署存取權評論

在您準備好策略和計畫來檢查與 Azure AD 整合之資源的存取權之後，請使用下列資源來部署和管理評論。

### <a name="review-access-packages"></a>審核存取套件

為了降低過時存取的風險，系統管理員可以定期審核對存取套件具有作用中指派的使用者。 遵循下列連結中的指示：

| 操作說明文章| 描述 |
| - | - |
| [建立存取權評論](entitlement-management-access-reviews-create.md)| 啟用存取套件的評論。 |
| [執行存取權審核](entitlement-management-access-reviews-review-access.md)| 針對指派給存取套件的其他使用者執行存取權審核。 |
| [ (s 的自我評論指派的存取套件) ](entitlement-management-access-reviews-self-review.md)|  (s) 的指派存取套件的自我評論 |


> [!NOTE]
> 不會立即從存取套件中移除自行審核並指出他們不再需要存取權的使用者。 當審核結束或系統管理員停止審核時，它們會從存取套件中移除。

### <a name="review-groups-and-apps"></a>審查群組和應用程式

員工和來賓的群組和應用程式存取需求可能會隨著時間而改變。 為了降低與過時存取權指派相關的風險，系統管理員可以建立群組成員或應用程式存取的存取權審核。 遵循下列連結中的指示：

| 操作說明文章| 描述 |
| - | - |
| [建立存取權評論](create-access-review.md)| 針對群組成員或應用程式存取建立一或多個存取權審核。 |
| [執行存取權審核](perform-access-review.md)| 針對具有應用程式存取權的群組成員或使用者執行存取權檢查。 |
| [自行審核您的存取權](review-your-access.md)| 成員審核群組或應用程式的存取權 |
| [完成存取權審核](complete-access-review.md)| 查看存取權檢查並套用結果 |
| [對內部部署群組採取動作](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| 對內部部署群組進行存取檢查時所要採取的範例 PowerShell 腳本。 |


### <a name="review-azure-ad-roles"></a>審核 Azure AD 角色

若要減少與過時角色指派相關聯的風險，您應該定期檢查特殊許可權 Azure AD 角色的存取權。

![顯示 Azure A D 角色 [審核成員資格] 清單的螢幕擷取畫面。](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

請遵循下列連結中的指示：

| 操作說明文章 | 描述 |
| - | - |
 [建立存取權評論](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 在 PIM 中建立特殊許可權 Azure AD 角色的存取權審核 |
| [自行審核您的存取權](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 如果您指派給系統管理角色，請核准或拒絕角色的存取權 |
| [完成存取權審核](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 查看存取權檢查並套用結果 |


### <a name="review-azure-resource-roles"></a>審核 Azure 資源角色

若要減少與過時角色指派相關聯的風險，您應該定期檢查特殊許可權 Azure 資源角色的存取權。 

![審查 azure ad 角色](./media/deploy-access-review/9-review-azure-roles-picker.png)

請遵循下列連結中的指示：

| 操作說明文章| 描述 |
| - | -|
| [建立存取權評論](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 在 PIM 中建立特殊許可權 Azure 資源角色的存取權審核 |
| [自行審核您的存取權](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 如果您指派給系統管理角色，請核准或拒絕角色的存取權 |
| [完成存取權審核](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 查看存取權檢查並套用結果 |


## <a name="use-the-access-reviews-api"></a>使用存取權審核 API

請參閱 [GRAPH API 方法](/graph/api/resources/accessreviews-root?view=graph-rest-beta) 和 [角色和應用程式許可權授權檢查](/graph/api/resources/accessreviews-root?view=graph-rest-beta) ，以與可檢閱 oss 資源互動並加以管理。 Microsoft Graph API 中的存取權審核方法適用于應用程式和使用者內容。 在應用程式內容中執行腳本時，用來執行 API (服務主體的帳戶) 必須被授與查詢存取權評論資訊的「AccessReview」許可權。

常用的存取權審核工作，可使用圖形 API 進行存取審核來自動化：

* 建立並開始存取權審核

* 在排程結束之前手動結束存取權審核

* 列出所有執行中的存取權評論及其狀態

* 請參閱審核系列的歷程記錄，以及每次評論所採取的決策和動作。

* 從存取權審查收集決策

* 從已完成的評論收集決策，而審核者所採取的決策與系統建議的不同。

針對自動化建立新的圖形 API 查詢時，我們建議使用 [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer)。 您可以先建立並探索圖形查詢，然後再將它們放入腳本和程式碼中。 這可協助您快速地逐一查看查詢，以取得您所需的結果，而不需變更腳本的程式碼。

## <a name="monitor-access-reviews"></a>監視存取權評論

存取審核活動會記錄下來，並可從 [Azure AD 的審核記錄](../reports-monitoring/concept-audit-logs.md)取得。 您可以篩選類別目錄、活動類型和日期範圍的審核資料。 以下是範例查詢：

| 類別| 原則 |
| - | - |
| 活動類型| 建立存取權檢閱 |
| | 更新存取權審核 |
| | 存取權審核已結束 |
| | 刪除存取權檢閱 |
| | 核准決策 |
| | 拒絕決策 |
| | 重設決策 |
| | 套用決策 |
| 日期範圍| 7 天 |


如需更高階的查詢和存取權檢查分析，以及追蹤變更和完成評論，建議您將 Azure AD 審核記錄匯出至 [Azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) 或 Azure 事件中樞。 當您儲存在 Azure Log Analytics 中時，您可以使用 [功能強大的分析語言](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) –並建立您自己的儀表板。

## <a name="next-steps"></a>後續步驟

深入瞭解下列相關技術。

* [什麼是 Azure AD 權利管理？](entitlement-management-overview.md)

* [什麼是 Azure AD Privileged Identity Management？](../privileged-identity-management/pim-configure.md)