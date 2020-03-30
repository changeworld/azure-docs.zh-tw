---
title: 部署特權標識管理 （PIM） - Azure AD |微軟文檔
description: 說明如何規劃 Azure AD Privileged Identity Management (PIM) 的部署。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/04/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8c77b3454026aa309d979bd938674e7c3ae7b6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025991"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>部署 Azure AD Privileged Identity Management (PIM)

此分步指南介紹如何在 Azure 活動目錄 （Azure AD） 組織中規劃特權標識管理 （PIM） 的部署。

> [!TIP]
> 在本文中，您將看到標記為以下項的專案：
> 
> ：heavy_check_mark：**微軟推薦**
> 
> 這些項目屬於一般性建議，請在其適用於貴企業的特定需求時才加以實作。

## <a name="learn-about-privileged-identity-management"></a>瞭解特權身份管理

Azure AD 特權標識管理可説明您跨 Azure AD、Azure 資源和其他 Microsoft 線上服務管理特權管理角色。 在分配和遺忘特權標識的世界中，特權標識管理提供即時訪問、請求審批工作流和完全集成的訪問審核等解決方案，以便您可以識別、發現和防止惡意特權角色的即時活動。 部署特權身份管理來管理整個組織的特權角色，將大大降低風險，同時展示有關特權角色活動的寶貴見解。

### <a name="business-value-of-privileged-identity-management"></a>特權身份管理的業務價值

**管理風險** - 藉由強制執行[最低權限存取](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)和 Just-In-Time 存取的原則來保護組織。 您可以藉由只將最少量的使用者永久指派為特殊權限角色，並強制執行要經過核准和通過 MFA 才可提高權限的機制，來大幅減少與組織中特殊權限存取相關的安全性風險。 強制執行最最低權限和 Just-In-Time 存取也可讓您檢視特殊權限角色的存取記錄，並追蹤所發生的安全性問題。

**解決合規性和治理**問題 - 部署特權標識管理可為持續的身份治理創建環境。 特權標識的及時提升為特權標識管理提供了一種跟蹤組織中的特權訪問活動的方法。 您也能夠檢視和收到組織內所有永久和合格角色指派的通知。 透過存取權檢閱，您可以定期稽核和移除不必要的特殊權限身分識別，並確保組織可符合最嚴格的身分識別、存取和安全性標準。

**降低成本**- 通過正確部署特權身份管理，消除效率低下、人為錯誤和安全問題，降低成本。 最終結果就是與特殊權限身分識別相關聯的網路犯罪件數減少了，因此不必付出高昂成本又不會於事後難以復原。 特權身份管理還有助於您的組織降低與審核訪問資訊相關的成本，使其在遵守法規和標準時。

如需詳細資訊，請參閱[什麼是 Azure AD Privileged Identity Management？](pim-configure.md)。

### <a name="licensing-requirements"></a>授權需求

要使用特權標識管理，您的目錄必須具有以下付費或試用許可證之一：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

有關詳細資訊，請參閱[使用特權標識管理的許可證要求](subscription-requirements.md)。

### <a name="key-terminology"></a>關鍵術語

| 詞彙或概念 | 描述 |
| --- | --- |
| 合格 | 需要使用者執行一或多個動作才能使用角色的角色指派。 如果使用者已有資格使用角色，即表示他們可以在需要執行特殊權限工作時啟用該角色。 使用者不論是具有永久角色指派還是合格角色指派，獲得的存取權並無差異。 唯一的差異在於有些使用者並不一直需要該存取權。 |
| 啟用 | 此程序會執行一或多個動作，讓使用者使用有資格使用的角色。 動作可能包含執行多重要素驗證 (MFA) 檢查、提供業務理由，或是向指定的核准者要求核准。 |
| Just-In-Time (JIT) 存取 | 一種模型，使用者會在其中獲得臨時權限以執行特殊權限的工作，這可防止惡意或未經授權的使用者在權限過期後取得存取權。 只有當使用者需要時才會獲得存取權。 |
| 最低權限存取的原則 | 建議的安全性做法，每位使用者只會獲得所需的最低權限，以便完成他們獲得授權而可執行的工作。 這種做法只需要最少量的全域管理員，並會改為針對特定案例使用特定的管理員角色。 |

如需詳細資訊，請參閱[術語](pim-configure.md#terminology)。

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>特權身份管理工作的高級概述

1. 設置特權標識管理，以便使用者有資格擔任特權角色。
1. 當符合條件的使用者需要使用其特權角色時，他們啟動特權標識管理中的角色。
1. 根據為角色配置的特權標識管理設置，使用者必須完成某些步驟（例如執行多重要素驗證、獲得批准或指定原因）。
1. 使用者成功啟動其角色後，便可在預先設定好的期間內獲得該角色。
1. 管理員可以在稽核記錄中查看所有特權標識管理活動的歷史記錄。 他們還可以使用特權標識管理功能（如訪問審核和警報）進一步保護其 Azure AD 組織並滿足合規性要求。

如需詳細資訊，請參閱[什麼是 Azure AD Privileged Identity Management？](pim-configure.md)。

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>可由特權身份管理管理的角色

**Azure AD 角色**– 這些角色都在 Azure 活動目錄中（如全域管理員、Exchange 管理員和安全管理員）。 您可以在 [Azure Active Directory 中的系統管理員角色權限](../users-groups-roles/directory-assign-admin-roles.md)中深入了解這些角色及其功能。 如需協助以便判斷要對系統管理員指派哪些角色，請參閱[依工作區分的最低特殊權限角色](../users-groups-roles/roles-delegate-by-task.md)。

**Azure 資源角色** - 這些角色會連結至 Azure 資源、資源群組、訂用帳戶或管理群組。 特權標識管理提供對內置角色（如擁有者、使用者訪問管理員和參與者）以及[自訂角色](../../role-based-access-control/custom-roles.md)的即時訪問。 如需 Azure 資源角色的詳細資訊，請參閱[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)。

有關詳細資訊，請參閱[在特權標識管理中無法管理的角色](pim-roles.md)。

## <a name="plan-your-deployment"></a>規劃您的部署

本節重點介紹在組織中部署特權標識管理之前需要執行哪些操作。 請務必遵循文中指示來進行，並了解本節中的概念，因為其可引導您建立專為組織的特殊權限身分識別所打造的最佳規劃。

### <a name="identify-your-stakeholders"></a>識別專案關係人

下面這節可協助您識別專案所涉及，且需要簽字、審核或掌握最新消息的所有專案關係人。 它包括用於為 Azure AD 角色部署特權標識管理的單獨表，以及 Azure 資源角色的特權標識管理。 請將專案關係人新增至下列適用於貴組織的資料表。

- SO = 簽字同意此專案
- R = 審核此專案，並提供輸入
- I = 知悉此專案的進度

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>專案關係人：Azure AD 角色的特權標識管理

| 名稱 | 角色 | 動作 |
| --- | --- | --- |
| 名稱和電子郵件 | **身分識別架構設計師或 Azure 全域系統管理員**<br/>身分識別管理小組所推派的代表人員，負責定義這項變更要如何與組織的核心身分識別管理基礎結構相配合。 | SO/R/I |
| 名稱和電子郵件 | **服務擁有者/部門經理**<br/>單一或一組服務的 IT 擁有者所推派的代表人員。 他們是決策的關鍵，並説明為團隊推出特權身份管理。 | SO/R/I |
| 名稱和電子郵件 | **安全性擁有者**<br/>安全性團隊所推派的代表人員，可簽字同意該規劃符合組織的安全性需求。 | SO/R |
| 名稱和電子郵件 | **IT 支援管理員/技術服務人員**<br/>IT 支援組織所推派的代表人員，可從技術服務人員的觀點就是否能支援這項變更來提出看法。 | R/I |
| 試驗使用者的名稱和電子郵件 | **特殊權限角色使用者**<br/>作為特殊權限身分識別管理機制實作對象的一群使用者。 在實施特權標識管理後，他們需要瞭解如何啟動其角色。 | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>專案關係人：Azure 資源角色的特權標識管理

| 名稱 | 角色 | 動作 |
| --- | --- | --- |
| 名稱和電子郵件 | **訂用帳戶/資源擁有者**<br/>要為 其部署特權標識管理的每個訂閱或資源的 IT 擁有者的代表 | SO/R/I |
| 名稱和電子郵件 | **安全性擁有者**<br/>安全性團隊所推派的代表人員，可簽字同意該規劃符合組織的安全性需求。 | SO/R |
| 名稱和電子郵件 | **IT 支援管理員/技術服務人員**<br/>IT 支援組織所推派的代表人員，可從技術服務人員的觀點就是否能支援這項變更來提出看法。 | R/I |
| 試驗使用者的名稱和電子郵件 | **RBAC 角色使用者**<br/>作為特殊權限身分識別管理機制實作對象的一群使用者。 在實施特權標識管理後，他們需要瞭解如何啟動其角色。 | I |

### <a name="enable-privileged-identity-management"></a>啟用特權標識管理

作為規劃過程的一部分，您必須首先同意並啟用特權身份管理，以下操作我們[首先使用特權標識管理](pim-getting-started.md)一文。 啟用特權標識管理使您能夠訪問某些專為説明部署而設計的功能。

如果目標是為 Azure 資源部署特權標識管理，則應按照我們的發現[Azure 資源在特權標識管理一文中進行管理](pim-resource-roles-discover-resources.md)。 只有訂閱和管理組的擁有者才能發現這些資源並將其添加到特權標識管理中。 板載後，PIM 功能可用於所有級別的擁有者，包括管理組、訂閱、資源組和資源。 如果您是試圖為 Azure 資源部署特權標識管理的全域管理員，則可以[提升存取權限以管理所有 Azure 訂閱](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)，以便自己訪問目錄中的所有 Azure 資源以進行發現。 但是，我們建議您在使用特權標識管理管理其資源之前獲得每個訂閱擁有者的批准。

### <a name="enforce-principle-of-least-privilege"></a>強制執行最低權限原則

請務必確定您已在組織中針對 Azure AD 和 Azure 資源角色強制執行最低權限原則。

#### <a name="azure-ad-roles"></a>Azure AD 角色

針對 Azure AD 角色，組織通常會指派全域系統管理員角色給大量的系統管理員，但大部分的系統管理員其實只需要一兩個特定的系統管理員角色。 特殊權限角色的指派往往也沒人管理。

> [!NOTE]
> 委派角色時的常見陷阱有：
>
> - 負責 Exchange 的系統管理員獲得全域系統管理員角色，但他們其實只需要 Exchange 系統管理員角色就能執行日常工作。
> - 因為 Office 系統管理員同時需要安全性系統管理員和 SharePoint 系統管理員角色，而對其指派全域系統管理員角色，但只委派一個角色會更容易些。
> - 系統管理員很久以前獲派安全性系統管理員角色以便執行工作，但卻從未移除。

請遵循下列步驟來為 Azure AD 角色強制執行最低權限原則。

1. 閱讀及了解[可用的 Azure AD 系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md#available-roles)，來了解角色的細微性。 您和您的小組也應該參考 [Azure AD 中依識別工作區分的系統管理員角色](../users-groups-roles/roles-delegate-by-task.md)，文內會說明特定工作的最低特殊權限角色。

1. 列出組織中具有特殊權限角色的人員。 您可以使用[特權標識管理嚮導](pim-security-wizard.md#run-the-wizard)訪問如下所示的頁面。

    ![發現特權角色窗格，顯示誰具有特權角色](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. 針對組織中的所有全域系統管理員，了解其為何需要該角色。 根據閱讀以前的文檔，如果人員的工作可以由一個或多個細微性管理員角色執行，則應將其從全域管理員角色中刪除，並在 Azure 活動目錄內相應地分配（作為參考：Microsoft 目前只有大約 10 名具有全域管理員角色的管理員。 詳細瞭解[微軟如何使用特權身份管理](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)。

1. 對於其他所有 Azure AD 角色，請檢閱指派清單、識別不再需要該角色的系統管理員，並從其指派中將其移除。

要自動執行最後兩個步驟，可以使用特權標識管理中的訪問審核。 按照在[特權標識管理中啟動 Azure AD 角色的訪問審閱](pim-how-to-start-security-review.md)步驟，可以為每個具有一個或多個成員的 Azure AD 角色設置訪問審閱。

![為 Azure AD 角色創建訪問審閱窗格](./media/pim-deployment-plan/create-access-review.png)

請將檢閱者設定為 [成員 (本身)]****。 這會對屬於該角色的所有成員傳送電子郵件，讓這些成員確認其是否需要此存取權。 也請開啟進階設定中的 [需要核准的原因]****，讓使用者可以陳述其為何需要該角色。 根據這項資訊，您就能夠將使用者從不必要的角色中移除，並對其委派更細微的系統管理員角色 (就全域系統管理員來說)。

存取權檢閱會依靠電子郵件，來通知相關人員檢閱其對於角色的存取權。 如果您的特殊權限帳戶未與電子郵件連結，請務必在這些帳戶上填入次要電子郵件欄位。 如需詳細資訊，請參閱 [Azure AD 中的 proxyAddresses 屬性](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)。

#### <a name="azure-resource-roles"></a>Azure 資源角色

針對 Azure 訂用帳戶和資源，您可以設定類似的存取權檢閱程序來檢閱每個訂用帳戶或資源中的角色。 此程序的目標是要讓連結至每個訂用帳戶或資源的擁有者和使用者存取系統管理員指派減到最少，以及要移除不必要的指派。 不過，組織通常會將這類工作委派給每個訂用帳戶或資源的擁有者，因為其對特定角色的了解更深 (特別是自訂角色)。

如果您是具有全域管理員角色的 IT 管理員，嘗試為組織中的 Azure 資源部署特權標識管理，則可以[提升存取權限以管理所有 Azure 訂閱](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)以訪問每個訂閱。 然後，您可以尋找每個訂用帳戶的擁有者，然後與其合作來移除不必要的指派，並將擁有者角色的指派數量減到最少。

具有 Azure 訂用帳戶擁有者角色的使用者也可利用 [Azure 資源的存取權檢閱](pim-resource-roles-start-access-review.md)，來稽核和移除不必要的角色指派，方法類似稍早針對 Azure AD 角色所述的程序。

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>決定哪些角色指派應受特權身份管理保護

清理組織中的特權角色指派後，您需要決定使用特權標識管理保護哪些角色。

如果角色受特權標識管理保護，則分配給該角色的合格使用者必須提升才能使用角色授予的許可權。 提高權限的程序中也可包含取得核准、執行多重要素驗證和/或提供啟動原因的機制。 特權標識管理還可以通過通知和特權標識管理和 Azure AD 審核事件日誌跟蹤提升。

選擇使用特權標識管理保護的角色可能很困難，並且對於每個組織來說會有所不同。 本節會針對 Azure AD 和 Azure 資源角色提供我們的最佳做法建議。

#### <a name="azure-ad-roles"></a>Azure AD 角色

請務必設定在保護具有最多權限的 Azure AD 角色時所應有的優先順序。 根據所有特權標識管理客戶之間的使用模式，由特權標識管理管理的前 10 個 Azure AD 角色是：

1. 全域管理員
1. 安全性系統管理員
1. 使用者管理員
1. Exchange 系統管理員
1. SharePoint 管理員
1. Intune 系統管理員
1. 安全性讀取者
1. 服務管理員
1. 計費管理員
1. 商務用 Skype 的管理員

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您使用特權身份管理管理所有全域管理員和安全管理員作為第一步，因為他們在受到危害時可能危害最大。

請務必想好，對於貴組織來說最為敏感的資料和權限是什麼。 例如，某些組織可能希望使用特權標識管理保護其 Power BI 管理員角色或其團隊管理員角色，因為它們能夠訪問資料和/或更改核心工作流。

如果有任何指派了來賓使用者的角色，其特別容易受到攻擊。

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您使用特權標識管理管理來賓使用者管理所有角色，以減少與受攻擊的來賓使用者帳戶相關的風險。

一般認為，相較於其他角色，有時候目錄讀取者、訊息中心讀取者和安全性讀取者等讀取者角色的重要性較低，原因是這些讀取者角色沒有寫入權限。 但是，我們看到一些客戶也保護這些角色，因為已訪問這些帳戶的攻擊者可能能夠讀取敏感性資料，如個人資料。 在決定是否需要使用特權標識管理管理組織中的讀者角色時，應考慮這一點。

#### <a name="azure-resource-roles"></a>Azure 資源角色

在決定應使用 Azure 資源的特權標識管理管理哪些角色指派時，必須首先標識對組織最重要的訂閱/資源。 舉例來說，這類訂用帳戶/資源有：

- 裝載最敏感資料的資源
- 核心的客戶面向應用程式所依賴的資源

如果您是全域系統管理員，且無法決定哪些訂用帳戶/資源最為重要，請與組織中的訂用帳戶擁有者聯繫，以收集每個訂用帳戶所管理資源的清單。 然後，請與訂用帳戶擁有者合作，根據這些資源遭到入侵時的嚴重性層級 (低、中、高) 來將這些資源分組。 應基於此嚴重性級別優先管理具有特權標識管理的資源。

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您與關鍵服務的訂閱/資源擁有者合作，為敏感訂閱/資源中的所有角色設置特權標識管理工作流。

Azure 資源的特權標識管理支援有時限的服務帳戶。 在對待服務帳戶時，態度應與對待一般使用者帳戶時完全相同。

對於不太重要的訂閱/資源，您無需為所有角色設置特權標識管理。 但是，您仍應使用特權標識管理保護擁有者和使用者訪問管理員角色。

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您使用特權標識管理管理所有訂閱/資源的擁有者角色和使用者訪問管理員角色。

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>決定哪些角色指派應該是永久的或符合資格的

確定由特權標識管理管理的角色清單後，必須決定哪些使用者應獲得符合條件的角色與永久活動角色。 永久活動的角色是通過 Azure 活動目錄和 Azure 資源配置的正常角色，而符合條件的角色只能在特權標識管理中分配。

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**對 Azure AD 角色和 Azure 資源角色具有零永久活動分配，但建議的[兩個中斷玻璃緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)應具有永久全域管理員角色。

雖然我們建議不要有永久性的系統管理員，但組織有時難以立即實現這一點要求。 以下是進行這項決策時所要考量的事項：

- 提高權限的頻率 - 如果使用者只需要一次特殊權限指派，就不應擁有永久性指派。 另一方面，如果使用者需要其日常工作的角色，並且使用特權標識管理將大大降低其工作效率，則可以考慮為永久角色。
- 組織特有情況 - 如果獲得合格角色的人員來自位處遠方的小組或其身分是高階主管，而讓溝通和強制執行提高權限的程序變得困難，則可以考慮讓其擁有永久性角色。

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您為具有永久角色指派的使用者設置定期訪問審核（如果您有任何）。 請至本部署規劃的最後一節來深入了解週期性的存取權檢閱

### <a name="draft-your-privileged-identity-management-settings"></a>起草您的特權身份管理設置

在實施特權身份管理解決方案之前，最好為組織使用的每個特權角色起草特權標識管理設置。 本節提供了特定角色的特權標識管理設置的一些示例（這些設置僅供參考，並且可能針對您的組織不同）。 在資料表後面，會有這些設定各自的詳細說明和 Microsoft 的建議。

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Azure AD 角色的特權標識管理設置

| 角色 | 需要 MFA | 通知 | 事件票證 | 需要核准 | 核准者 | 啟動持續時間 | 永久性系統管理員 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 全域管理員 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 其他全域系統管理員 | 1 小時 | 緊急存取帳戶 |
| Exchange 系統管理員 | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | None | 2 小時 | None |
| 服務台系統管理員 | :x: | :x: | :heavy_check_mark: | :x: | None | 8 小時 | None |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Azure 資源角色的特權標識管理設置

| 角色 | 需要 MFA | 通知 | 需要核准 | 核准者 | 啟動持續時間 | 有效系統管理員 | 有效到期日 | 合格到期日 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 重要訂用帳戶的擁有者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 訂用帳戶的其他擁有者 | 1 小時 | None | n/a | 3 個月 |
| 較不重要訂用帳戶的使用者存取系統管理員 | :heavy_check_mark: | :heavy_check_mark: | :x: | None | 1 小時 | None | n/a | 3 個月 |
| 虛擬機器參與者 | :x: | :heavy_check_mark: | :x: | None | 3 小時 | None | n/a | 6 個月 |

下表說明每個設定。

| 設定 | 描述 |
| --- | --- |
| 角色 | 要為其定義設定的角色名稱。 |
| 需要 MFA | 符合資格的使用者是否必須先執行 MFA 才能啟動該角色。<br/><br/> ：heavy_check_mark：Microsoft**建議**您對所有管理員角色強制實施 MFA，尤其是在角色具有來賓使用者時。 |
| 通知 | 如果設定為 true，則組織中的全域系統管理員、特殊權限角色系統管理員和安全性系統管理員會在符合資格的使用者啟動該角色時收到電子郵件通知。<br/><br/>**注：** 某些組織沒有與其管理員帳戶綁定的電子郵件地址，要獲取這些電子郵件通知，應著手設置備用電子郵件地址，以便管理員將收到這些電子郵件。 |
| 事件票證 | 符合資格的使用者是否必須在啟動其角色時記錄下事件票證號碼。 此設定可協助組織透過內部事件號碼來識別每個啟動，以減少不必要的啟動。<br/><br/> ：heavy_check_mark：Microsoft**建議**利用事件票證編號將特權身份管理與內部系統聯繫起來。 這特別適用於需要知道啟動情境的核准者。 |
| 需要核准 | 符合資格的使用者是否必須先獲得核准才能啟動該角色。<br/><br/> ：heavy_check_mark：Microsoft**建議**您設置許可權最大的角色審批。 根據所有特權身份管理客戶的使用模式，全域管理員、使用者管理員、Exchange 管理員、安全管理員和密碼管理員是審批設置中最常見的角色。 |
| 核准者 | 如果需要核准才能啟動符合資格的角色，請列出應該核准要求的人員。 預設情況下，特權標識管理將核准者設為作為特權角色管理員的所有使用者，無論他們是永久性的還是符合資格的。<br/><br/>**注：** 如果使用者既有資格享受 Azure AD 角色，又有資格擔任該角色的核准者，則他們將無法自行批准。<br/><br/> ：heavy_check_mark：Microsoft**建議**您選擇核准者為那些最瞭解特定角色及其頻繁使用者的人，而不是全域管理員。 |
| 啟動持續時間 | 使用者可以在到期前以該角色啟動的時間長度。 |
| 永久性系統管理員 | 將成為該角色永久性 (永遠不必啟動) 系統管理員的使用者清單。<br/><br/> ：heavy_check_mark：Microsoft**建議**除全域管理員外的所有角色都擁有零常設管理員。 請於本規劃的應該讓誰符合資格以及應該讓誰永久有效的章節中深入了解這方面的資訊。 |
| 有效系統管理員 | 針對 Azure 資源，有效的系統管理員是永遠不需要啟動就能使用該角色的使用者清單。 這並不是像 Azure AD 角色中的永久性系統管理員，因為您可以設定到期時間來讓使用者到時候失去此角色。 |
| 有效到期日 | 在這個設定好的期間過後，Azure 資源角色的有效角色指派就會過期。 您可以選擇 15 天、1 個月、3 個月、6 個月、1 年或永久有效。 |
| 合格到期日 | 在這個設定好的期間過後，Azure 資源角色的合格角色指派就會過期。 您可以選擇 15 天、1 個月、3 個月、6 個月、1 年或永久合格。 |

## <a name="implement-your-solution"></a>實作您的解決方案

有了適當的規劃後，您就可以據此成功地使用 Azure Active Directory 來部署應用程式。  此基礎可提供智慧型的安全性和整合，既能簡化登入，又能降低成功完成部署所需的時間。  兩者相結合，便可確保應用程式能夠輕鬆整合，同時減少使用者所遇到的停機時間。

### <a name="identify-test-users"></a>識別測試使用者

請使用本節，識別用以驗證此實作的一組使用者或使用者群組。 根據您在規劃章節所選取的設定，識別您想要針對每個角色來測試的使用者。

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您將每個 Azure AD 角色的服務擁有者作為測試使用者，以便他們熟悉該過程並成為推出的內部宣導者。

在下表中，識別會確認每個角色的設定有效的測試使用者。

| 角色名稱 | 測試使用者 |
| --- | --- |
| &lt;角色名稱&gt; | &lt;要測試角色的使用者&gt; |
| &lt;角色名稱&gt; | &lt;要測試角色的使用者&gt; |

### <a name="test-implementation"></a>測試實作

現在，您已經確定了測試使用者，請使用此步驟為測試使用者配置特權標識管理。 如果您的組織希望將特權標識管理工作流合併到您自己的內部應用程式中，而不是在 Azure 門戶中使用特權標識管理，則特權標識管理中的所有操作也可通過[我們的圖形 API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root)提供支援。

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>為 Azure AD 角色配置特權標識管理

1. 根據計畫配置[Azure AD 角色設置](pim-how-to-change-default-settings.md)。

1. 瀏覽至 **Azure AD 角色**，按一下 [角色]****，然後選取您剛才設定的角色。

1. 針對測試使用者群組，如果他們已經是永久性系統管理員，則您可以藉由搜尋這些系統管理員，並將其從永久性轉換為符合資格 (藉由按一下其資料列上的三個點)，來使其成為符合資格的系統管理員。 如果這些系統管理員還未具有角色指派，則您可以[進行新的合格指派](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)。

1. 針對您想要測試的所有角色重複執行步驟 1 至 3。

1. 在設定好測試使用者後，請傳送如何[啟動其 Azure AD 角色](pim-how-to-activate-role.md)的連結給這些使用者。

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>為 Azure 資源角色配置特權標識管理

1. 為訂用帳戶或資源內要測試的角色[設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)。

1. 瀏覽至該訂用帳戶的 **Azure 資源**，然後按一下 [角色]****，並選取您剛才設定的角色。

1. 針對測試使用者群組，如果他們已經是有效系統管理員，則您可以藉由搜尋這些系統管理員來使其成為符合資格的系統管理員，然後[更新其角色指派](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)。 如果這些系統管理員還未具有該角色，則您可以[指派新的角色](pim-resource-roles-assign-roles.md#assign-a-role)。

1. 針對您想要測試的所有角色重複執行步驟 1 至 3。

1. 在設定好測試使用者後，請傳送如何[啟動其 Azure 資源角色](pim-resource-roles-activate-your-roles.md)的連結給這些使用者。

請使用這個階段來確認您為角色設定的所有組態是否可正常運作。 請使用下表來記載測試。 也請使用這個階段來將與受影響使用者的溝通方式最佳化。

| 角色 | 啟動期間的預期行為 | 實際結果 |
| --- | --- | --- |
| 全域管理員 | (1) 需要 MFA<br/>(2) 需要核准<br/>(3) 核准者收到通知並可核准<br/>(4) 角色在預設時間過後到期 |  |
| 訂用帳戶 X** 的擁有者 | (1) 需要 MFA<br/>(2) 符合資格的指派在所設定的期間過後到期 |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>將特權身份管理傳達給受影響的利益相關者

部署特權標識管理將為特權角色的使用者引入其他步驟。 儘管特權標識管理大大減少了與特權標識相關的安全問題，但需要在租戶範圍部署之前有效地傳達更改。 根據受影響的系統管理員數目，組織通常會選擇建立關於此變更的內部文件、影片或電子郵件。 這些溝通內容中經常包含：

- 何謂 PIM
- 對組織有何好處
- 誰會受到影響
- 何時會推廣 PIM
- 使用者必須另外執行哪些步驟才能啟動其角色
    - 您應該傳送文件的連結：
    - [啟動 Azure AD 角色](pim-how-to-activate-role.md)
    - [啟動 Azure 資源角色](pim-resource-roles-activate-your-roles.md)
- 可供解決 PIM 相關問題的連絡資訊或技術服務人員連結

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您在説明台/支援小組中設置時間，引導他們完成特權身份管理工作流（如果您的組織有內部 IT 支援小組）。 請向這些人提供適當的文件以及您的連絡資訊。

### <a name="move-to-production"></a>移動至生產環境

測試完成並成功後，通過在特權標識管理配置中為每個角色的所有使用者重複測試階段的所有步驟，將特權標識管理移動到生產。 對於 Azure AD 角色的特權標識管理，組織通常會在測試和推出其他角色的特權標識管理之前，測試和推出全域管理員的特權標識管理。 同時，對於 Azure 資源，組織通常一次測試並推出一個 Azure 訂閱的特權標識管理。

### <a name="in-the-case-a-rollback-is-needed"></a>在需要復原的情況下

如果特權標識管理未能在生產環境中根據需要工作，以下回滾步驟可以説明您在設置特權標識管理之前恢復到已知良好的狀態：

#### <a name="azure-ad-roles"></a>Azure AD 角色

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
1. 打開**Azure AD 特權標識管理**。
1. 按一下 [Azure AD 角色]****，然後按一下 [角色]****。
1. 針對您已設定的每個角色，對具有合格指派的所有使用者按一下省略符號 (**...**)。
1. 按一下 [設為永久]**** 選項讓角色指派具有永久性。

#### <a name="azure-resource-roles"></a>Azure 資源角色

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
1. 打開**Azure AD 特權標識管理**。
1. 按一下 [Azure 資源]****，然後按一下您想要復原的訂用帳戶或資源。
1. 按一下 [角色]****。
1. 針對您已設定的每個角色，對具有合格指派的所有使用者按一下省略符號 (**...**)。
1. 按一下 [設為永久]**** 選項讓角色指派具有永久性。

## <a name="next-steps-after-deploying"></a>部署後的後續步驟

在生產中成功部署特權標識管理是保護組織特權標識的重要一步。 隨著特權標識管理的部署，您還附帶了用於安全性和合規性的其他特權標識管理功能。

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>使用特權身份管理警報來保護您的特權存取權限

您應該利用特權標識管理的內置警報功能來更好地保護租戶。 如需詳細資訊，請參閱[安全性警訊](pim-how-to-configure-security-alerts.md#security-alerts)。 這些警報包括：管理員不使用特權角色，角色被分配在特權標識管理之外，角色被啟動得過於頻繁和更多。 若要完整保護組織，請定期瀏覽警示清單並修正問題。 您可以透過下列方式檢視和修正警示：

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
1. 打開**Azure AD 特權標識管理**。
1. 按一下 [Azure AD 角色]****，然後按一下 [警示]****。

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您立即處理標記為高嚴重性的所有警報。 至於中度與低度的嚴重性警示，則請隨時了解情況，並在您認為有安全性威脅的時候進行變更。

如果有任何特定警示沒有用或不適用於貴組織，您一律可以在警示頁面上關閉警示。 您之後隨時可以在 Azure AD 的設定頁面中還原此關閉動作。

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>設定週期性的存取權檢閱以便定期稽核貴組織的特殊權限身分識別

若想詢問獲派特殊權限角色的使用者或要求特定檢閱者每位使用者是否需要特殊權限身分識別，最好的方法就是存取權檢閱。 如果您想要減少受攻擊面並持續符合規範，存取權檢閱非常實用。 如需如何啟動存取權檢閱的詳細資訊，請參閱 [Azure AD 角色的存取權檢閱](pim-how-to-start-security-review.md)和 [Azure 資源角色的存取權檢閱](pim-resource-roles-start-access-review.md)。 某些組織必須執行定期的存取權檢閱，以便持續遵守法律及法規，至於其他人，若要在組織中全面強制執行最低權限原則，存取權檢閱會是最好的方式。

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您為所有 Azure AD 和 Azure 資源角色設置季度訪問審核。

在大部分情況下，Azure AD 角色的檢閱者是使用者本身，而 Azure 資源角色的檢閱者是該角色所在訂用帳戶的擁有者。 不過，擁有特殊權限帳戶的公司往往未與任何特定人員的電子郵件地址連結。 在這些情況下，就沒有人會讀取並檢閱該存取權。

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您為所有具有特權角色指派的帳戶添加輔助電子郵件地址，這些帳戶未連結到定期檢查的電子郵件地址

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>充分利用稽核記錄來改善安全性與合規性

稽核記錄可讓您隨時掌握最新情況和並符合法規。 特權身份管理目前在其稽核記錄中存儲組織所有歷史記錄的 30 天歷史記錄，包括：

- 合格角色的啟動/停用
- 特權身份管理內外的角色指派活動
- 角色設定的變更
- 角色啟動 (設定了核准機制) 的要求/核准/拒絕活動
- 警示的更新

如果您是全域系統管理員或特殊權限角色系統管理員，就可以存取這些稽核記錄。 如需詳細資訊，請參閱 [Azure AD 角色的稽核記錄](pim-how-to-use-audit-log.md)和 [Azure 資源角色的稽核記錄](azure-pim-resource-rbac.md)。

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您每週至少讓一名管理員通讀所有審核事件，並每月匯出您的審核事件。

如果要在較長時間內自動存儲審核事件，特權標識管理的稽核記錄將自動同步到[Azure AD 稽核記錄](../reports-monitoring/concept-audit-logs.md)中。

> [!TIP]
> ：heavy_check_mark：Microsoft**建議**您設置[Azure 日誌監視](../reports-monitoring/concept-activity-logs-azure-monitor.md)以在 Azure 存儲帳戶中存檔審核事件，以進行安全性和合規性。
