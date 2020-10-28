---
title: 使用 Azure AD Identity Governance 來檢查和移除不再具有資源存取權的外部使用者
description: 使用存取評論來擴充夥伴組織成員的移除存取權
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: barclayn
ms.openlocfilehash: 19f88da6a678221cde66bf61668d16ba9ab998a4
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677315"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>使用 Azure Active Directory (Azure AD) 身分識別治理來檢查和移除不再具有資源存取權的外部使用者

本文說明可讓您找出並選取外部身分識別的功能和方法，讓您可以在不再需要的情況下，從 Azure AD 中檢查並移除它們。 雲端可讓您比以往更輕鬆地與內部或外部使用者共同作業。 在使用 Office 365 時，組織會開始查看外部身分識別的激增 (包括來賓) ，因為使用者會在資料、檔或數位工作區（例如小組）之間共同作業。 組織需要平衡、啟用共同作業，並符合安全性和治理需求。 這些工作的一部分應該包括評估和清除外部使用者，這些使用者已獲邀共同作業至您的租使用者、來自于夥伴組織，並在不再需要時從您的 Azure AD 中移除。

>[!NOTE]
>需要已付費的有效 Azure AD Premium P2、Enterprise Mobility + Security E5 或試用版授權，才能使用 Azure AD 存取權檢閱。 如需詳細資訊，請參閱 [Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)。

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>為什麼要從您租使用者中的外部組織審查使用者？

在大部分的組織中，終端使用者會起始邀請商務夥伴和廠商進行共同作業的流程。 需要共同作業驅動組織，以提供資源擁有者和使用者可定期評估及證明外部使用者的方式。 將新的共同作業夥伴上線的程式通常是規劃和考慮的，但有許多共同作業並沒有清楚的結束日期，則當使用者不再需要存取權時，不一定會很明顯。 此外，身分識別生命週期管理可推動企業以保持 Azure AD 乾淨，並移除不再需要存取組織資源的使用者。 針對目錄中的夥伴和廠商只保留相關的身分識別參考，有助於降低員工的風險，並不慎選取並授與應移除的外部使用者存取權。 本檔將逐步引導您完成數個選項，範圍從建議的主動式建議到回應式和清除活動，以管理外部身分識別。

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>使用權利管理來授與及撤銷存取權

權利管理功能可讓外部身分識別的 [自動化生命週期](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) 具有資源的存取權。 藉由建立可透過權利管理來管理存取權的流程和程式，以及透過存取套件發佈資源，讓外部使用者存取資源變得更不復雜，因此難以解決問題。 當您在 Azure AD 中透過 [權利管理存取套件](entitlement-management-overview.md) 來管理存取權時，您的組織可以集中定義和管理使用者的存取權，以及來自夥伴組織的使用者。 權利管理會使用存取套件的核准和指派，來追蹤外部使用者要求和指派存取的位置。 如果外部使用者遺失所有指派，權利管理可以從租使用者自動移除這些外部使用者。 

## <a name="find-guests-not-invited-through-entitlement-management"></a>尋找未受權利管理邀請的來賓

當員工獲得授權可與外部使用者共同作業時，他們可能會邀請組織外部的任意數量的使用者。 找出外部夥伴並將其分組至公司對齊的動態群組，並加以檢查可能不可行，因為可能有太多不同的個人公司需要評論，或是組織沒有任何擁有者或贊助者。 Microsoft 提供範例 PowerShell 腳本，可協助您分析在租使用者中使用外部身分識別的方式。 腳本會列舉外部身分識別，並將其分類。 腳本可協助您找出並清除可能不再需要的外部身分識別。 在腳本的輸出中，腳本範例支援自動建立安全性群組，其中包含已識別的無群組外部夥伴–可供進一步分析和使用 Azure AD 存取權評論。
此腳本可在 [GitHub](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)上取得。 在腳本執行完成之後，它會產生 HTML 輸出檔，該檔案會列出外部身分識別：

- 租使用者中不再具有任何群組成員資格
- 在租使用者中具有特殊許可權角色的指派
- 對租使用者中的應用程式進行指派

輸出也包含每個外部識別的個別網域。 

>[!NOTE]
>上述腳本是一個範例腳本，可檢查 Azure AD 中的群組成員資格、角色指派和應用程式指派。 外部使用者可能會在 Azure AD 以外的其他應用程式中收到其他指派，例如 SharePoint (直接成員資格指派) 或 Azure RBAC 或 Azure DevOps。

## <a name="review-resources-used-by-external-identities"></a>檢查外部身分識別所使用的資源

如果您有使用資源（例如，小組或其他尚未受權利管理管理的應用程式）的外部身分識別，您可能會想要定期查看這些資源的存取權。 Azure AD [存取權評論](create-access-review.md) 可讓您藉由讓資源擁有者、外部身分識別或您信任的其他委派人員證明您是否需要繼續存取，來審核外部身分識別的存取權。 存取評論的目標是資源，並建立僅限可存取資源或來賓使用者的每一位使用者的評論活動。 審核者接著會看到所需的使用者清單，也就是所有使用者，包括貴組織的員工或外部身分識別。

![使用群組來檢查存取權](media/access-reviews-external-users/group-members.png)

建立資源擁有者驅動的評論文化特性有助於管理外部身分識別的存取權。 資源擁有者（負責存取所擁有資訊的存取、可用性和安全性），在大部分情況下，您的最佳物件是推動其資源存取的決策，並比起中央 IT 或管理許多外部的贊助者更接近存取這些資源的使用者。

## <a name="create-access-reviews-for-external-identities"></a>建立外部身分識別的存取權審核

如果使用者無法再使用您的組織，就可以移除您租使用者中任何資源的存取權。 在您封鎖和刪除這些外部身分識別之前，您可能會想要與這些外部使用者聯繫，並確定您未忽略其仍需要的專案或長期存取權。 當您建立一個群組，其中包含所有外部身分識別，因為您找到的成員無法存取您租使用者中的任何資源，所以您可以使用 [存取審核]，讓所有外部的自我證明是否仍需要或具有存取權，或未來仍需要存取權。 作為評論的一部分，存取評論中的審核建立者可以使用 **需要核准的原因** 來要求外部使用者提供持續存取的理由，讓您可以瞭解他們在您的租使用者中仍需要存取的位置和方式。 此外，您可以啟用 [設定 **檢閱者電子郵件的其他內容** ] 功能，讓使用者知道他們將會遺失存取權（如果他們沒有回應），如果他們仍需要存取，則需要理由。 如果您想要繼續進行，讓存取權審核 **停用並刪除** 外部身分識別，則如果它們無法回應或提供有效的持續存取原因，您可以使用 [停用] 和 [刪除] 選項，如下一節所述。

![將評論範圍限制為僅限來賓使用者](media/access-reviews-external-users/guest-users-only.png)

當審核完成時，[ **結果** ] 頁面會顯示每個外部身分識別所提供的回應總覽。 您可以選擇自動套用結果，並讓存取權審核停用並將其刪除。 或者，您可以查看所提供的回應，並決定是否要移除使用者的存取權，或使用這些回應來取得其他資訊，然後再進行決策。 如果某些使用者仍可存取您尚未審核的資源，您可以在探索過程中使用此評論，並豐富您的下一個審核和證明週期。

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews-preview"></a>使用 Azure AD 存取權審核來停用及刪除外部身分識別 (預覽) 

除了從群組或應用程式之類的資源移除不必要的外部身分識別之外，Azure AD 存取權審核可以封鎖外部身分識別，使其無法登入您的租使用者，並在30天后刪除租使用者中的外部身分識別。 一旦您選取 **[封鎖使用者登入30天]，然後從租使用者中移除使用者** ，審核將會保持「套用」狀態30天。 在這段期間內，目前的評論底下的設定、結果、審核者或審核記錄將無法查看或設定。 

![完成設定時](media/access-reviews-external-users/upon-completion-settings.png)

建立新的存取權審查時，在「完成設定」一節中， **若要在拒絕的使用者上套用動作** ，您可以定義 **封鎖使用者登入30天，然後從租使用者中移除使用者** 。
此設定目前為預覽狀態，可讓您識別、封鎖和刪除 Azure AD 租使用者中的外部身分識別。 無論資源存取或群組成員資格為何，都將封鎖並刪除審核者已審核並拒絕繼續存取的外部身分識別。 這項設定最適合在您驗證外部使用者已不再擁有資源存取權，且可以安全地從您的租使用者中移除，或如果您想要確保其被移除（不論其持續存取權為何）的最後一個步驟。 [停用和刪除] 功能會先封鎖外部使用者，讓他們能夠登入您的租使用者並存取資源。 在這個階段中不會撤銷資源存取權，如果您想要 reinstantiate 外部使用者，可以重新設定其登入的能力。 如果沒有進一步的動作，系統會在30天后將封鎖的外部身分識別從目錄中刪除，並移除帳戶以及其存取權。

## <a name="next-steps"></a>後續步驟

- [存取審查 - 圖形 API](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [權利管理 - 圖形 API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)