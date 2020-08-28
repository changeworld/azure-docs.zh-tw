---
title: 部署 Privileged Identity Management (PIM) -Azure AD |Microsoft Docs
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
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bfe0fee14ed463e265dc4e7e4177c702b051c81
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050194"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>部署 Azure AD Privileged Identity Management (PIM)

本文將逐步指南說明如何規劃在 Azure Active Directory (Azure AD) 組織中部署 Privileged Identity Management (PIM) 。 您會盡可能將高許可權角色中的使用者重新指派給較不強大的內建或自訂角色，並規劃最具特殊許可權角色的及時角色指派。 在本文中，我們會針對部署規劃和實行進行建議。

> [!TIP]
> 在本文中，您將會看到標示為的專案：
>
> ： heavy_check_mark： **Microsoft 建議**
>
> 這些是一般建議，您應該只在適用于您特定的企業需求時才執行這些建議。

## <a name="licensing-requirements"></a>授權需求

若要使用 Privileged Identity Management，您的目錄必須具有下列其中一項付費或試用版授權。 如需詳細資訊，請參閱 [使用 Privileged Identity Management 的授權需求](subscription-requirements.md)。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 教育版 A5
- Microsoft 365 企業版 E5

## <a name="how-pim-works"></a>PIM 的運作方式

本節將針對 Privileged Identity Management 程式相關部分的規劃目的提供評論。 如需詳細資訊，請參閱 [什麼是 Azure AD Privileged Identity Management？](pim-configure.md)

1. 開始使用 Privileged Identity Management，讓使用者符合特殊許可權角色的資格。
1. 當符合資格的使用者需要使用其特殊許可權角色時，他們會使用 Privileged Identity Management 來啟動角色。
1. 您可以在下列設定中要求使用者：

    - 使用 Multi-Factor Authentication
    - 要求核准以進行啟用
    - 提供啟用的商業理由

1. 使用者成功啟動其角色之後，就會擁有設定期間的角色許可權。
1. 系統管理員可以在審核記錄中查看所有 Privileged Identity Management 活動的歷程記錄。 他們也可以進一步保護其 Azure AD 組織，並使用 Privileged Identity Management 功能（例如存取評論和警示）來符合合規性。

## <a name="roles-that-can-be-managed-by-pim"></a>PIM 所能管理的角色

**Azure AD 角色** 全都位於 Azure Active Directory 的 (，例如全域管理員、Exchange 系統管理員和安全性系統管理員) 。 您可以在 [Azure Active Directory 中的系統管理員角色權限](../users-groups-roles/directory-assign-admin-roles.md)中深入了解這些角色及其功能。 如需協助以便判斷要對系統管理員指派哪些角色，請參閱[依工作區分的最低特殊權限角色](../users-groups-roles/roles-delegate-by-task.md)。

**Azure 角色** 是連結至 azure 資源、資源群組、訂用帳戶或管理群組的角色。 您可以使用 PIM 來提供內建 Azure 角色的即時存取權，例如擁有者、使用者存取系統管理員和參與者，以及 [自訂角色](../../role-based-access-control/custom-roles.md)。 如需 Azure 角色的詳細資訊，請參閱 [azure 角色型存取控制](../../role-based-access-control/overview.md)。

如需詳細資訊，請參閱 [Privileged Identity Management 中無法管理的角色](pim-roles.md)。

## <a name="deployment-plan"></a>部署計劃

在您的組織中部署 Privileged Identity Management 之前，請遵循指示並瞭解本節中的概念，以協助您建立針對組織的特殊許可權身分識別需求量身打造的方案。

### <a name="identify-your-stakeholders"></a>識別專案關係人

下一節可協助您識別參與專案的所有專案關係人，並且必須登出、評論或隨時掌握最新資訊。 它包含個別的表格，用於為 Azure 角色的 Azure AD 角色和 PIM 部署 PIM。 請將專案關係人新增至下列適用於貴組織的資料表。

- SO = 簽字同意此專案
- R = 審核此專案，並提供輸入
- I = 知悉此專案的進度

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>專案關係人： Azure AD 角色的 Privileged Identity Management

| 名稱 | 角色 | 動作 |
| --- | --- | --- |
| 名稱和電子郵件 | **身分識別架構設計師或 Azure 全域系統管理員**<br/>身分識別管理小組的代表，負責定義如何配合您組織中的核心身分識別管理基礎結構來調整這項變更。 | SO/R/I |
| 名稱和電子郵件 | **服務擁有者/部門經理**<br/>單一或一組服務的 IT 擁有者所推派的代表人員。 它們是制定決策和協助為團隊推出 Privileged Identity Management 的關鍵。 | SO/R/I |
| 名稱和電子郵件 | **安全性擁有者**<br/>安全性小組的代表，可登出方案是否符合您組織的安全性需求。 | SO/R |
| 名稱和電子郵件 | **IT 支援管理員/技術服務人員**<br/>IT 支援組織提供的代表，可根據技術服務人員的觀點，提供這項變更的可支援性意見反應。 | R/I |
| 試驗使用者的名稱和電子郵件 | **特殊權限角色使用者**<br/>作為特殊權限身分識別管理機制實作對象的一群使用者。 他們必須知道如何在 Privileged Identity Management 執行之後啟動其角色。 | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>專案關係人：適用于 Azure 角色的 Privileged Identity Management

| 名稱 | 角色 | 動作 |
| --- | --- | --- |
| 名稱和電子郵件 | **訂用帳戶/資源擁有者**<br/>您想要部署 Privileged Identity Management 的每個訂用帳戶或資源的 IT 擁有者代表 | SO/R/I |
| 名稱和電子郵件 | **安全性擁有者**<br/>安全性團隊所推派的代表人員，可簽字同意該規劃符合組織的安全性需求。 | SO/R |
| 名稱和電子郵件 | **IT 支援管理員/技術服務人員**<br/>IT 支援組織提供的代表，可根據技術服務人員的觀點，提供這項變更的可支援性意見反應。 | R/I |
| 試驗使用者的名稱和電子郵件 | **Azure 角色使用者**<br/>作為特殊權限身分識別管理機制實作對象的一群使用者。 他們必須知道如何在 Privileged Identity Management 執行之後啟動其角色。 | I |

### <a name="start-using-privileged-identity-management"></a>開始使用 Privileged Identity Management

在規劃過程中，您應該遵循我們的 [開始使用 Privileged Identity Management](pim-getting-started.md) 文章來準備 Privileged Identity Management。 Privileged Identity Management 可讓您存取一些設計來協助您部署的功能。

如果您的目標是要部署適用于 Azure 資源的 Privileged Identity Management，您應該遵循我們 [的探索 azure 資源以在 Privileged Identity Management 文章中進行管理](pim-resource-roles-discover-resources.md) 。 只有訂用帳戶和管理群組的擁有者可以 Privileged Identity Management 管理這些資源。 進行管理之後，所有層級（包括管理群組、訂用帳戶、資源群組和資源）的擁有者都可使用 PIM 功能。 如果您是全域系統管理員，嘗試部署 Azure 資源的 Privileged Identity Management，您可以提高 [存取權以管理所有 azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) 訂用帳戶，讓您能夠存取目錄中的所有 azure 資源以進行探索。 不過，我們建議您在使用 Privileged Identity Management 管理資源之前，先取得每個訂用帳戶擁有者的核准。

### <a name="enforce-principle-of-least-privilege"></a>強制執行最低權限原則

請務必確定您已在組織中針對您的 Azure AD 和 Azure 角色強制執行最低許可權原則。

#### <a name="plan-least-privilege-delegation"></a>規劃最低許可權委派

針對 Azure AD 角色，當大部分的系統管理員只需要一或兩個特定且功能較低的系統管理員角色時，組織通常會將全域管理員角色指派給許多系統管理員。 如果有大量的全域系統管理員或其他高許可權角色，很難追蹤您的特殊許可權角色指派。

請遵循下列步驟，為您的 Azure AD 角色執行最低許可權原則。

1. 閱讀及了解[可用的 Azure AD 系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md#available-roles)，來了解角色的細微性。 您和您的小組也應該參考 [Azure AD 中依識別工作區分的系統管理員角色](../users-groups-roles/roles-delegate-by-task.md)，文內會說明特定工作的最低特殊權限角色。

1. 列出組織中具有特殊權限角色的人員。 您可以使用 Privileged Identity Management [探索和深入解析 (預覽) ](pim-security-wizard.md) 來縮減您的曝光。

    ![探索和見解 (預覽) 頁面，以降低透過特殊許可權角色的風險](./media/pim-deployment-plan/new-preview-page.png)

1. 針對組織中的所有全域系統管理員，了解其為何需要該角色。 然後，將它們從全域管理員角色中移除，並在 Azure Active Directory 內以較低許可權指派內建角色或自訂角色。 僅供參考，Microsoft 目前只有10個具有全域管理員角色的系統管理員。 深入瞭解 [Microsoft 如何使用 Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)。

1. 對於其他所有 Azure AD 角色，請檢閱指派清單、識別不再需要該角色的系統管理員，並從其指派中將其移除。

若要將最後兩個步驟自動化，您可以在 Privileged Identity Management 中使用存取權審核。 遵循在 Privileged Identity Management 中 [開始存取 Azure AD 角色](pim-how-to-start-security-review.md)的步驟之後，您可以為每個具有一或多個成員的 Azure AD 角色設定存取權審核。

![建立 Azure AD 角色的存取權審核窗格](./media/pim-deployment-plan/create-access-review.png)

將審核者設定為 ** (自我) 的成員 **。 角色中的所有使用者都會收到一封電子郵件，要求他們確認是否需要存取權。 此外，在 [advanced settings] 中開啟 [ **需要核准的原因** ]，讓使用者必須陳述需要角色的原因。 根據此資訊，您可以從不必要的角色中移除使用者，或將其委派給更細微的系統管理員角色。

存取權檢閱會依靠電子郵件，來通知相關人員檢閱其對於角色的存取權。 如果您的特殊權限帳戶未與電子郵件連結，請務必在這些帳戶上填入次要電子郵件欄位。 如需詳細資訊，請參閱 [Azure AD 中的 proxyAddresses 屬性](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)。

#### <a name="plan-azure-resource-role-delegation"></a>規劃 Azure 資源角色委派

針對 Azure 訂用帳戶和資源，您可以設定類似的存取權檢閱程序來檢閱每個訂用帳戶或資源中的角色。 此程式的目標是要將附加至每個訂用帳戶或資源的擁有者和使用者存取系統管理員指派降至最低，並移除不必要的指派。 不過，組織通常會將這類工作委派給每個訂用帳戶或資源的擁有者，因為其對特定角色的了解更深 (特別是自訂角色)。

如果您是在組織中嘗試為 Azure 角色部署 PIM 的全域系統管理員角色，您可以提高 [存取權以管理所有 Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) 訂用帳戶，以取得每個訂用帳戶的存取權。 然後，您可以尋找每個訂用帳戶的擁有者，然後與其合作來移除不必要的指派，並將擁有者角色的指派數量減到最少。

擁有 Azure 訂用帳戶擁有者角色的使用者，也可以使用 [azure 資源的存取權審查](pim-resource-roles-start-access-review.md) 來審核和移除不必要的角色指派，類似于先前針對 Azure AD 角色所述的程式。

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>決定哪些角色指派應受保護 Privileged Identity Management

清除您組織中的特殊許可權角色指派之後，您必須決定要使用 Privileged Identity Management 保護哪些角色。

如果角色受 Privileged Identity Management 保護，指派給它的合格使用者必須提高許可權，才能使用角色所授與的許可權。 提高許可權程式也可能包含取得核准、使用多重要素驗證，以及提供其啟用的原因。 Privileged Identity Management 也可以透過通知和 Privileged Identity Management 和 Azure AD audit 事件記錄檔來追蹤提升的進度。

選擇要保護 Privileged Identity Management 的角色可能會很困難，而且每個組織都會有不同的角色。 本節提供 Azure AD 和 Azure 角色的最佳作法建議。

#### <a name="azure-ad-roles"></a>Azure AD 角色

保護具有最多許可權的 Azure AD 角色時，請務必設定優先順序。 根據所有 Privileged Identity Management 客戶的使用模式，Privileged Identity Management 所管理的前10個 Azure AD 角色為：

1. 全域管理員
1. 安全性系統管理員
1. 使用者管理員
1. Exchange 系統管理員
1. SharePoint 管理員
1. Intune 管理員
1. 安全性讀取者
1. 服務管理員
1. 計費管理員
1. 商務用 Skype 的管理員

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您使用 Privileged Identity Management 作為第一個步驟來管理所有全域系統管理員和安全性系統管理員，因為他們是在遭入侵時可能會造成損害的使用者。

請務必考慮您的組織最敏感的資料和許可權。 例如，某些組織可能會想要使用 Privileged Identity Management 來保護其 Power BI 系統管理員角色或其小組系統管理員角色，因為他們可以存取資料和變更核心工作流程。

如果有任何已指派來賓使用者的角色，就很容易受到攻擊。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您使用 Privileged Identity Management 來管理來賓使用者的所有角色，以降低與遭入侵之來賓使用者帳戶相關聯的風險。

讀者角色（像是目錄讀取者、訊息中心讀者和安全性讀取者）有時會被視為比其他角色更不重要，因為它們沒有寫入權限。 不過，我們有一些客戶也會保護這些角色，因為具有這些帳戶存取權的攻擊者可能可以讀取機密資料，例如個人資料。 決定是否要使用 Privileged Identity Management 管理組織中的讀取者角色時，請考慮這項風險。

#### <a name="azure-roles"></a>Azure 角色

決定哪些角色指派應使用適用于 Azure 資源的 Privileged Identity Management 管理時，您必須先找出最重要的組織訂用帳戶/資源。 舉例來說，這類訂用帳戶/資源有：

- 裝載最敏感資料的資源
- 核心的客戶面向應用程式所依賴的資源

如果您是全域系統管理員，但無法決定哪些訂用帳戶和資源是最重要的，您應該在您的組織中聯絡訂用帳戶擁有者，以收集每個訂用帳戶所管理的資源清單。 然後，請與訂用帳戶擁有者合作，根據嚴重性層級來分組資源， (低、中、高) 。 根據此嚴重性層級，以 Privileged Identity Management 為基礎管理資源的優先順序。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您與重要服務的訂用帳戶/資源擁有者合作，為敏感性訂用帳戶/資源內的所有角色設定 Privileged Identity Management 的工作流程。

適用于 Azure 資源的 Privileged Identity Management 支援時間綁定的服務帳戶。 在對待服務帳戶時，態度應與對待一般使用者帳戶時完全相同。

針對不是關鍵的訂用帳戶/資源，您不需要設定所有角色的 Privileged Identity Management。 不過，您仍然應該使用 Privileged Identity Management 來保護擁有者和使用者存取系統管理員角色。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議您使用** Privileged Identity Management 來管理所有訂用帳戶/資源的擁有者角色和使用者存取系統管理員角色。

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>決定是否要使用群組來指派角色

是否要將角色指派給群組而非個別使用者，是一項策略性決策。 在規劃時，請考慮在下列情況下，將角色指派給群組以管理角色指派：

- 有許多使用者指派給角色
- 您想要委派指派角色

#### <a name="many-users-are-assigned-to-a-role"></a>有許多使用者指派給角色

若要追蹤指派給角色的人員，並根據使用者的需求來管理指派，可能需要一些時間才能手動完成。 若要將群組指派給角色，請先建立可指派 [角色的群組](../users-groups-roles/roles-groups-create-eligible.md) ，然後將該群組指派為符合角色的資格。 此動作會將群組中的每個人都與符合可提升為角色的個別使用者進行相同的啟用程式。 群組成員會使用 Privileged Identity Management 啟用要求和核准程式來個別啟用對群組的指派。 群組不會啟動，只會啟用使用者的群組成員資格。

#### <a name="you-want-to-delegate-assigning-the-role"></a>您想要委派指派角色

群組擁有者可以管理群組的成員資格。 針對 Azure AD 角色指派的群組，只有特殊許可權角色管理員、全域管理員和群組擁有者可以管理群組成員資格。 藉由將新成員新增至群組，成員就可以存取指派給群組的角色，無論指派是否合格或作用中。 使用群組擁有者來委派管理指派角色的群組成員資格，以降低所需的許可權範圍。 如需在建立群組時將擁有者指派給群組的詳細資訊，請參閱 [Azure AD 中的建立角色](../users-groups-roles/roles-groups-create-eligible.md)可指派群組。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您透過 Privileged Identity Management，將 Azure AD 角色可指派的群組納入管理之下。 由 PIM 管理可指派角色的群組之後，就稱為「特殊許可權存取群組」。 使用 PIM 要求群組擁有者必須先啟用其擁有者角色指派，才能管理群組成員資格。 如需在 PIM 管理下將群組帶入的詳細資訊，請參閱 Privileged Identity Management 中將特殊許可權 [存取群組 (預覽) ](groups-discover-groups.md)。

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>決定哪些角色指派應該是永久的或符合資格的

一旦決定要由 Privileged Identity Management 管理的角色清單之後，您必須決定哪些使用者應該取得符合資格的角色，而不是永久有效的角色。 永久有效的角色是透過 Azure Active Directory 和 Azure 資源指派的一般角色，而符合資格的角色只能在 Privileged Identity Management 中指派。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您針對 Azure AD 角色和 Azure 角色，除了建議的 [兩個全形緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)（應該具有永久全域管理員角色）之外，還會有零個永久有效的指派。

雖然我們建議不要有永久性的系統管理員，但組織有時難以立即實現這一點要求。 以下是進行這項決策時所要考量的事項：

- 提高權限的頻率 - 如果使用者只需要一次特殊權限指派，就不應擁有永久性指派。 另一方面，如果使用者需要角色來進行日常工作，且使用 Privileged Identity Management 會大幅降低其生產力，則可以將它們視為永久角色。
- 您組織的特定案例-如果獲得合格角色的人員是從遙遠的小組或高階主管，到傳達和強制提升許可權程式的時間點，則可以考慮永久角色。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您為具有永久角色指派的使用者設定週期性存取權審核 (您是否有任何) 。 請至本部署規劃的最後一節來深入了解週期性的存取權檢閱

### <a name="draft-your-privileged-identity-management-settings"></a>草擬您的 Privileged Identity Management 設定

在您執行 Privileged Identity Management 解決方案之前，最好先針對您組織所使用的每個特殊許可權角色，草擬您的 Privileged Identity Management 設定。 本節提供特定角色 Privileged Identity Management 設定的一些範例 (這些角色僅供參考，而您的組織) 可能會有所不同。 在資料表後面，會有這些設定各自的詳細說明和 Microsoft 的建議。

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Azure AD 角色的 Privileged Identity Management 設定

| 角色 | 需要 MFA | 通知 | 事件票證 | 需要核准 | 核准者 | 啟動持續時間 | 永久性系統管理員 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 全域管理員 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 其他全域系統管理員 | 1 小時 | 緊急存取帳戶 |
| Exchange 系統管理員 | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | 無 | 2 小時 | 無 |
| 服務台系統管理員 | :x: | :x: | :heavy_check_mark: | :x: | 無 | 8 小時 | 無 |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Azure 角色的 Privileged Identity Management 設定

| 角色 | 需要 MFA | 通知 | 需要核准 | 核准者 | 啟動持續時間 | 有效系統管理員 | 有效到期日 | 合格到期日 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 重要訂用帳戶的擁有者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 訂用帳戶的其他擁有者 | 1 小時 | 無 | n/a | 3 個月 |
| 較不重要訂用帳戶的使用者存取系統管理員 | :heavy_check_mark: | :heavy_check_mark: | :x: | 無 | 1 小時 | 無 | n/a | 3 個月 |
| 虛擬機器參與者 | :x: | :heavy_check_mark: | :x: | 無 | 3 小時 | 無 | n/a | 6 個月 |

下表說明每個設定。

| 設定 | 描述 |
| --- | --- |
| 角色 | 要為其定義設定的角色名稱。 |
| 需要 MFA | 符合資格的使用者是否必須先執行 MFA 才能啟動該角色。<br/><br/> ： heavy_check_mark： **Microsoft 建議** 您針對所有系統管理員角色強制執行 MFA，特別是當角色具有來賓使用者時。 |
| 通知 | 如果設定為 true，則組織中的全域系統管理員、特殊權限角色系統管理員和安全性系統管理員會在符合資格的使用者啟動該角色時收到電子郵件通知。<br/><br/>**注意：** 某些組織沒有系結至其系統管理員帳戶的電子郵件地址，若要取得這些電子郵件通知，您應該設定替代的電子郵件地址，讓系統管理員能夠收到這些電子郵件。 |
| 事件票證 | 符合資格的使用者是否必須在啟動其角色時記錄下事件票證號碼。 此設定可協助組織透過內部事件號碼來識別每個啟動，以減少不必要的啟動。<br/><br/> ： heavy_check_mark： **Microsoft 建議** 利用事件票證號碼，將 Privileged Identity Management 系結至您的內部系統。 對於需要啟用內容的核准者而言，這個方法會很有用。 |
| 需要核准 | 符合資格的使用者是否必須先獲得核准才能啟動該角色。<br/><br/> ： heavy_check_mark： **Microsoft 建議** 您為具有最多許可權的角色設定核准。 根據所有 Privileged Identity Management 客戶的使用模式、全域管理員、使用者系統管理員、Exchange 系統管理員、安全性系統管理員和密碼管理員，都是具有核准設定的最常見角色。 |
| 核准者 | 如果需要核准才能啟動符合資格的角色，請列出應該核准要求的人員。 根據預設，Privileged Identity Management 會將核准者設定為具有特殊許可權角色系統管理員的所有使用者，不論它們是永久或符合資格。<br/><br/>**注意：** 如果使用者同時符合 Azure AD 角色和角色核准者的資格，他們將無法自行核准。<br/><br/> ： heavy_check_mark： **Microsoft 建議** 您選擇「核准者」成為最熟悉角色的使用者，以及其經常的使用者，而非「全域管理員」。 |
| 啟動持續時間 | 使用者可以在到期前以該角色啟動的時間長度。 |
| 永久性系統管理員 | 將成為該角色永久性 (永遠不必啟動) 系統管理員的使用者清單。<br/><br/> ： heavy_check_mark： **Microsoft 建議** 您對於所有角色（全域系統管理員除外）都有零的長期系統管理員。 請於本規劃的應該讓誰符合資格以及應該讓誰永久有效的章節中深入了解這方面的資訊。 |
| 有效系統管理員 | 針對 Azure 資源，有效的系統管理員是永遠不需要啟動就能使用該角色的使用者清單。 這份清單在 Azure AD 角色中不是永久的系統管理員，因為您可以設定使用者將遺失此角色的到期時間。 |
| 有效到期日 | Azure 角色的作用中角色指派會在設定的持續時間之後到期。 您可以選擇 15 天、1 個月、3 個月、6 個月、1 年或永久有效。 |
| 合格到期日 | 適用于 Azure 角色的合格角色指派會在此持續時間之後到期。 您可以選擇 15 天、1 個月、3 個月、6 個月、1 年或永久合格。 |

## <a name="implementation-plan"></a>執行計畫

有了適當的規劃後，您就可以據此成功地使用 Azure Active Directory 來部署應用程式。  此基礎可提供智慧型的安全性和整合，既能簡化登入，又能降低成功完成部署所需的時間。  兩者相結合，便可確保應用程式能夠輕鬆整合，同時減少使用者所遇到的停機時間。

### <a name="identify-test-users"></a>識別測試使用者

請使用本節，識別用以驗證此實作的一組使用者或使用者群組。 根據您在規劃章節所選取的設定，識別您想要針對每個角色來測試的使用者。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您讓每個 Azure AD 角色的服務擁有者成為測試使用者，讓他們可以熟悉此程式，並成為推出的內部 advocator。

在下表中，找出要驗證角色設定是否正常運作的測試使用者。

| 角色名稱 | 測試使用者 |
| --- | --- |
| &lt;角色名稱&gt; | &lt;要測試角色的使用者&gt; |
| &lt;角色名稱&gt; | &lt;要測試角色的使用者&gt; |

### <a name="test-implementation"></a>測試實作

既然您已經識別測試使用者，請使用此步驟來設定測試使用者的 Privileged Identity Management。 如果您的組織想要將 Privileged Identity Management 工作流程併入您自己的內部應用程式，而不是在 Azure 入口網站中使用 Privileged Identity Management，則 Privileged Identity Management 中的所有作業也可透過我們的 [圖形 API](/graph/api/resources/privilegedidentitymanagement-root)來支援。

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>設定 Azure AD 角色的 Privileged Identity Management

1. 根據您的計畫設定[Azure AD 角色設定](pim-how-to-change-default-settings.md)。

1. 流覽至 **Azure AD 角色**]，選取 [ **角色**]，然後選取您設定的角色。

1. 針對測試使用者群組，如果他們已經是永久系統管理員，您可以藉由在其資料列上選取三個點，藉由搜尋這些使用者並將其從永久轉換成符合資格。 如果這些系統管理員還未具有角色指派，則您可以[進行新的合格指派](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)。

1. 針對您想要測試的所有角色重複執行步驟 1 至 3。

1. 在設定好測試使用者後，請傳送如何[啟動其 Azure AD 角色](pim-how-to-activate-role.md)的連結給這些使用者。

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>設定 Azure 角色的 Privileged Identity Management

1. 為訂用帳戶或資源內要測試的角色[設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)。

1. 流覽至該訂用帳戶的 **Azure 資源** ，然後選取 [ **角色**]，選取您設定的角色。

1. 針對測試使用者群組，如果他們已經是有效系統管理員，則您可以藉由搜尋這些系統管理員來使其成為符合資格的系統管理員，然後[更新其角色指派](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)。 如果這些系統管理員還未具有該角色，則您可以[指派新的角色](pim-resource-roles-assign-roles.md#assign-a-role)。

1. 針對您想要測試的所有角色重複執行步驟 1 至 3。

1. 在設定好測試使用者後，請傳送如何[啟動其 Azure 資源角色](pim-resource-roles-activate-your-roles.md)的連結給這些使用者。

請使用這個階段來確認您為角色設定的所有組態是否可正常運作。 請使用下表來記載測試。 也請使用這個階段來將與受影響使用者的溝通方式最佳化。

| 角色 | 啟動期間的預期行為 | 實際結果 |
| --- | --- | --- |
| 全域管理員 | (1) 需要 MFA<br/>(2) 需要核准<br/>(3) 核准者收到通知並可核准<br/>(4) 角色在預設時間過後到期 |  |
| 訂用帳戶 X** 的擁有者 | (1) 需要 MFA<br/>(2) 符合資格的指派在所設定的期間過後到期 |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>將 Privileged Identity Management 傳達給受影響的專案關係人

部署 Privileged Identity Management 會為特殊許可權角色的使用者引進額外的步驟。 雖然 Privileged Identity Management 可大幅減少與特殊許可權身分識別相關聯的安全性問題，但在整個組織的部署之前，必須有效地傳達變更。 根據受影響的系統管理員數目，組織通常會選擇建立關於此變更的內部文件、影片或電子郵件。 這些溝通內容中經常包含：

- 何謂 PIM
- 對組織有何好處
- 誰會受到影響
- 何時會推廣 PIM
- 使用者必須另外執行哪些步驟才能啟動其角色
    - 您應該傳送文件的連結：
    - [啟動 Azure AD 角色](pim-how-to-activate-role.md)
    - [啟用 Azure 角色](pim-resource-roles-activate-your-roles.md)
- 可供解決 PIM 相關問題的連絡資訊或技術服務人員連結

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您設定技術支援小組的時間，以在您的組織有內部 IT 支援小組) 時，逐步完成 Privileged Identity Management 工作流程 (。 請向這些人提供適當的文件以及您的連絡資訊。

### <a name="move-to-production"></a>移動至生產環境

測試完成且成功之後，請針對您在 Privileged Identity Management 設定中定義的每個角色的所有使用者，重複執行測試階段中的所有步驟，以將 Privileged Identity Management 移至生產環境。 針對 Azure AD 角色的 Privileged Identity Management，組織通常會在測試和推出其他角色 Privileged Identity Management 之前，為全域系統管理員測試和推出 Privileged Identity Management。 針對 Azure 資源，組織通常會一次測試和推出 Privileged Identity Management 一個 Azure 訂用帳戶。

### <a name="if-a-rollback-is-needed"></a>如果需要復原

如果 Privileged Identity Management 在生產環境中無法正常運作，下列復原步驟可協助您在設定 Privileged Identity Management 之前，回復為已知的良好狀態：

#### <a name="azure-ad-roles"></a>Azure AD 角色

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 開啟 **Azure AD Privileged Identity Management**。
1. 選取 **Azure AD 角色** ]，然後選取 [ **角色**]。
1. 針對您已設定的每個角色，為具有合格指派的所有使用者選取省略號 (**...**) 。
1. 選取 [ **設為永久** ] 選項，讓角色指派永久。

#### <a name="azure-roles"></a>Azure 角色

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 開啟 **Azure AD Privileged Identity Management**。
1. 選取 [ **Azure 資源** ]，然後選取您想要復原的訂用帳戶或資源。
1. 選取 [角色]****。
1. 針對您已設定的每個角色，為具有合格指派的所有使用者選取省略號 (**...**) 。
1. 選取 [ **設為永久** ] 選項，讓角色指派永久。

## <a name="next-steps-after-deploying"></a>部署後的後續步驟

在生產環境中成功部署 Privileged Identity Management 是在保護貴組織的特殊許可權身分識別方面很重要的一大步。 部署 Privileged Identity Management 會隨附額外的 Privileged Identity Management 功能，供您用於安全性和合規性。

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>使用 Privileged Identity Management 警示來保護您的特殊許可權存取

如需使用 Privileged Identity Management 內建警示功能來保護組織安全的詳細資訊，請參閱 [安全性警示](pim-how-to-configure-security-alerts.md#security-alerts)。 這些警示包括：系統管理員未使用特殊許可權角色、角色指派于 Privileged Identity Management 之外、角色的啟用頻率過高及更多。 若要完整保護組織，請定期瀏覽警示清單並修正問題。 您可以透過下列方式檢視和修正警示：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 開啟 **Azure AD Privileged Identity Management**。
1. 選取 [ **Azure AD 角色** ]，然後選取 [ **警示**]。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您立即處理所有標示為高嚴重性的警示。 至於中度與低度的嚴重性警示，則請隨時了解情況，並在您認為有安全性威脅的時候進行變更。

如果有任何特定警示沒有用或不適用於貴組織，您一律可以在警示頁面上關閉警示。 您之後隨時可以在 Azure AD 的設定頁面中還原此關閉動作。

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>設定週期性的存取權檢閱以便定期稽核貴組織的特殊權限身分識別

若想詢問獲派特殊權限角色的使用者或要求特定檢閱者每位使用者是否需要特殊權限身分識別，最好的方法就是存取權檢閱。 如果您想要減少受攻擊面並持續符合規範，存取權檢閱非常實用。 如需有關開始存取權檢查的詳細資訊，請參閱 [Azure AD 角色存取權評論](pim-how-to-start-security-review.md) 和 [Azure 角色存取權評論](pim-resource-roles-start-access-review.md)。 某些組織必須執行定期的存取權檢閱，以便持續遵守法律及法規，至於其他人，若要在組織中全面強制執行最低權限原則，存取權檢閱會是最好的方式。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您為所有 Azure AD 和 Azure 角色設定每季的存取權評論。

在大多數情況下，Azure AD 角色的審核者是使用者本身，而 Azure 角色的審核者則是角色所在訂用帳戶的擁有者。 不過，擁有特殊權限帳戶的公司往往未與任何特定人員的電子郵件地址連結。 在這些情況下，就沒有人會讀取並檢閱該存取權。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您針對具有特殊許可權角色指派但未連結至定期檢查電子郵件地址的所有帳戶，新增次要電子郵件地址

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>充分利用稽核記錄來改善安全性與合規性

稽核記錄可讓您隨時掌握最新情況和並符合法規。 Privileged Identity Management 目前會在其審核記錄中儲存所有組織歷程記錄的30天歷程記錄，包括：

- 合格角色的啟動/停用
- Privileged Identity Management 內部和外部的角色指派活動
- 角色設定的變更
- 角色啟動 (設定了核准機制) 的要求/核准/拒絕活動
- 警示的更新

如果您是全域管理員或特殊許可權角色管理員，您可以存取 audit 記錄。 如需詳細資訊，請參閱 [Azure AD 角色的審核歷程記錄](pim-how-to-use-audit-log.md) 和 [Azure 角色的審核歷程記錄](azure-pim-resource-rbac.md)。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您至少讓一位系統管理員每週讀取所有 audit 事件，並每月匯出您的 audit 事件。

如果您想要將您的審核事件自動儲存一段較長的時間，Privileged Identity Management 的 audit 記錄會自動同步到 [Azure AD audit 記錄](../reports-monitoring/concept-audit-logs.md)檔中。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建議** 您設定 [azure 記錄監視](../reports-monitoring/concept-activity-logs-azure-monitor.md) ，以將審核事件封存在 azure 儲存體帳戶中，以獲得更高的安全性和合規性。
