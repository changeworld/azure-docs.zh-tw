---
title: B2B 來賓使用者的屬性 - Azure 活動目錄 |微軟文檔
description: Azure 活動目錄 B2B 來賓使用者屬性和狀態在邀請兌換之前和之後
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f5002e361653614c966dc43301afa83eb7b200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050803"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B 共同作業使用者的屬性

本文介紹 Azure 活動目錄 （Azure AD） 中 B2B 來賓使用者物件在邀請兌換之前和之後的屬性和狀態。 Azure AD 企業對企業 （B2B） 協作使用者是具有使用者類型 + 來賓的使用者。 此來賓使用者通常是來自合作夥伴組織的使用者，且依預設在邀請目錄中的權限有限。

根據邀請組織的需求，Azure AD B2B 共同作業使用者可以是下列帳戶狀態之一︰

- 狀態 1：位於 Azure AD 的外部執行個體並表示為提出邀請之組織中的來賓使用者。 在此情況下，B2B 使用者會使用屬於受邀租用戶的 Azure AD 帳戶登入。 如果夥伴組織未使用 Azure AD，仍會在 Azure AD 中建立來賓使用者。 他們必須兌換其邀請，而且 Azure AD 必須確認其電子郵件地址。 此排列也稱為 Just-In-Time (JIT) 租用或「熱門」租用。

   > [!IMPORTANT]
   > **從 2021 年 3 月 31 日起**，Microsoft 將不再支援為 B2B 協作方案創建非託管 Azure AD 帳戶和租戶來兌換邀請。 在準備中，我們鼓勵客戶選擇[電子郵件一次性密碼身份驗證](one-time-passcode.md)。 我們歡迎您對此公共預覽功能的回饋，並興奮地創建更多協作方式。

- 狀態 2：在 Microsoft 或其他帳戶中託管，並在主機組織中表示為來賓使用者。 在此情況下，來賓使用者會使用 Microsoft 帳戶或社交帳戶 (google.com 或類似帳戶) 來登入。 受邀使用者的身分識別會在供應項目兌換期間，建立為邀請方組織目錄中的 Microsoft 帳戶。

- 狀態 3：位於主機組織的內部部署 Active Directory 並與主機組織的 Azure AD 同步處理。 您可以使用 Azure AD Connect 將夥伴帳戶同步至雲端，以作為 UserType = Guest 的 Azure AD B2B 使用者。 請參閱[對本機管理的夥伴帳戶授與雲端資源的存取權](hybrid-on-premises-to-cloud.md)。

- 狀態 4：在主機組織的 Azure AD 中託管，包含使用者類型 = 主機組織管理的來賓和憑據。

  ![描述四個使用者狀態的圖表](media/user-properties/redemption-diagram.png)


現在，讓我們來看看 Azure AD B2B 共同作業使用者在 Azure AD 中的外觀。

### <a name="before-invitation-redemption"></a>邀請兌換之前

狀態 1 和狀態 2 帳戶是邀請來賓使用者使用自有認證來進行共同作業的結果。 當邀請初次傳送給來賓使用者時，您的目錄中就會建立帳戶。 此帳戶沒有任何與其相關聯的認證，因為驗證會由來賓使用者的識別提供者來執行。 您目錄中來賓使用者帳戶的**來源**屬性會設定為**受邀使用者**。 

![在報價兌換之前顯示使用者屬性的螢幕截圖](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>邀請兌換之後

在來賓使用者接受邀請之後，**來源**屬性會根據來賓使用者的識別提供者來進行更新。

針對狀態 1 的來賓使用者，**來源**是**外部 Azure Active Directory**。

![兌換供應項目之後的狀態 1 來賓使用者](media/user-properties/after-redemption-state1.png)

針對狀態 2 的來賓使用者，**來源**是 **Microsoft 帳戶**。

![兌換供應項目之後的狀態 2 來賓使用者](media/user-properties/after-redemption-state2.png)

針對狀態 3 和狀態 4 的來賓使用者，**來源**屬性會設定為 **Azure Active Directory** 或 **Windows Server Active Directory**，如下一節中所述。

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Azure AD B2B 共同作業使用者的金鑰屬性
### <a name="usertype"></a>UserType
此屬性會指出使用者與主機租用的關聯性。 此屬性可以包含兩個值：
- 成員︰此值表示主機組織的員工，以及由組織支薪的使用者。 例如，這位使用者要能夠存取僅供內部使用的網站。 這位使用者不會被認為是外部共同作業人員。

- 來賓：此值表示不被視為公司內部的使用者，例如外部協作者、合作夥伴或客戶。 這類使用者不會收到執行長的內部備忘，或收到公司權益等。

  > [!NOTE]
  > UserType 與使用者登入的方式、使用者的目錄角色等等無關。 此屬性只是表示使用者與主機組織的關聯性，並可讓組織強制執行此屬性的相依原則。

### <a name="source"></a>來源
此屬性指出使用者的登入方式。

- 受邀使用者︰此使用者已受邀請，但尚未兌換邀請。

- 外部 Azure 活動目錄：此使用者駐留在外部組織中，並使用屬於其他組織的 Azure AD 帳戶進行身份驗證。 這類型的登入對應於狀態 1。

- Microsoft 帳戶：此使用者位於 Microsoft 帳戶，並使用 Microsoft 帳戶進行驗證。 這類型的登入對應於狀態 2。

- Windows Server Active Directory︰此使用者已從屬於此組織的內部部署 Active Directory 登入。 這類型的登入對應於狀態 3。

- Azure Active Directory︰此使用者會使用屬於此組織的 Azure AD 帳戶進行驗證。 這類型的登入對應於狀態 4。
  > [!NOTE]
  > Source 和 UserType 是獨立的屬性。 Source 的值不代表特定的 UserType 值。

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Azure AD B2B 使用者是否可新增為成員而非來賓？
一般而言，Azure AD B2B 使用者和來賓使用者的意義相同。 因此，Azure AD B2B 共同作業使用者依預設是新增為具有 UserType = [來賓] 的使用者。 不過，在某些情況下，合作夥伴組織是主機組織也所屬之較大組織的成員。 若是如此，主機組織可能會將合作夥伴組織中的使用者視為成員而非來賓。 使用 Azure AD B2B 邀請管理員 API 來從夥伴組織新增或邀請使用者至主機組織作為成員。

## <a name="filter-for-guest-users-in-the-directory"></a>篩選目錄中的來賓使用者

![顯示來賓使用者篩選器的螢幕截圖](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>轉換 UserType
可能會使用 PowerShell 將 UserType 從 [成員] 轉換為 [來賓]，反之亦然。 不過，UserType 屬性會代表使用者與組織的關聯性。 因此，只有在使用者與組織的關聯性變更時，才應該變更此屬。 如果使用者的關聯性變更，是否應該變更使用者主體名稱 (UPN)？ 使用者是否應該繼續存取相同的資源？ 是否應指派信箱？ 我們不建議以 PowerShell 做為不可部分完成的活動來變更 UserType。 此外，為免使用 PowerShell 讓這個屬性成為不可變屬性，我們不建議採用此值的相依性。

## <a name="remove-guest-user-limitations"></a>移除來賓使用者限制
在某些情況下，您要提供來賓使用者更高的權限。 您可以將來賓使用者新增至任何角色，甚至移除目錄中預設的來賓使用者限制，為使用者提供與成員相同的權限。

可以關閉預設限制，如此公司目錄中的來賓使用者就有與成員使用者相同的權限。

![在使用者設置中顯示"外部使用者"選項的螢幕截圖](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>能否在 Exchange 全域通訊清單中顯示來賓使用者？
是。 根據預設，來賓物件不會在貴組織的全域通訊清單中顯示，但您可以使用 Azure Active Directory PowerShell 讓其顯示。 如需詳細資訊，請參閱[管理 Office 365 群組中的來賓存取](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#add-guests-to-the-global-address-list) \(機器翻譯\) 中的＜能否在全域通訊清單中顯示來賓物件？＞****。 

## <a name="next-steps"></a>後續步驟

* [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
* [B2B 共同作業使用者權杖](user-token.md)
* [B2B 共同作業使用者宣告對應](claims-mapping.md)
