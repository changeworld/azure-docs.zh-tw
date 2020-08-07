---
title: B2B 來賓使用者的屬性-Azure Active Directory |Microsoft Docs
description: Azure Active Directory B2B 來賓使用者屬性和邀請兌換前後的狀態
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7271c4de6d5c186c9e561aa37a140eaa04cbc0a
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908137"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B 共同作業使用者的屬性

本文說明 Azure Active Directory (Azure AD) 邀請兌換前後的 B2B 來賓使用者物件的屬性和狀態。 Azure AD 的企業對企業 (B2B) 協同作業使用者是 UserType = Guest 的使用者。 此來賓使用者通常是來自合作夥伴組織的使用者，且依預設在邀請目錄中的權限有限。

根據邀請組織的需求，Azure AD B2B 共同作業使用者可以是下列帳戶狀態之一︰

- 狀態 1：位於 Azure AD 的外部執行個體並表示為提出邀請之組織中的來賓使用者。 在此情況下，B2B 使用者會使用屬於受邀租用戶的 Azure AD 帳戶登入。 如果夥伴組織未使用 Azure AD，仍會在 Azure AD 中建立來賓使用者。 他們必須兌換其邀請，而且 Azure AD 必須確認其電子郵件地址。 此排列也稱為 Just-In-Time (JIT) 租用或「熱門」租用。

   > [!IMPORTANT]
   > **自 2021 年 3 月 31 日起**，Microsoft 將不再支援兌換邀請，而是建立適用於 B2B 共同作業案例的非受控 Azure AD 帳戶和租用戶。 在準備過程中，我們鼓勵客戶選擇使用[電子郵件一次性密碼驗證](one-time-passcode.md)。 我們歡迎您提供此公開預覽功能的意見反應，而且期待能建立更多共同作業的方式。

- 狀態2：位於 Microsoft 或其他帳戶中，並表示為主機組織中的來賓使用者。 在此情況下，來賓使用者會使用 Microsoft 帳戶或社交帳戶 (google.com 或類似帳戶) 來登入。 受邀使用者的身分識別會在供應項目兌換期間，建立為邀請方組織目錄中的 Microsoft 帳戶。

- 狀態 3：位於主機組織的內部部署 Active Directory 並與主機組織的 Azure AD 同步處理。 您可以使用 Azure AD Connect 將夥伴帳戶同步至雲端，以作為 UserType = Guest 的 Azure AD B2B 使用者。 請參閱[對本機管理的夥伴帳戶授與雲端資源的存取權](hybrid-on-premises-to-cloud.md)。

- 狀態4：使用 UserType = 來賓和主機組織所管理的認證，在主組織的 Azure AD 中。

  ![描述四個使用者狀態的圖表](media/user-properties/redemption-diagram.png)


現在，讓我們來看看 Azure AD B2B 共同作業使用者在 Azure AD 中的外觀。

### <a name="before-invitation-redemption"></a>邀請兌換之前

狀態 1 和狀態 2 帳戶是邀請來賓使用者使用自有認證來進行共同作業的結果。 當邀請初次傳送給來賓使用者時，您的目錄中就會建立帳戶。 此帳戶沒有任何與其相關聯的認證，因為驗證會由來賓使用者的識別提供者來執行。 您目錄中來賓使用者帳戶的**來源**屬性會設定為**受邀使用者**。 

![在供應專案兌換之前顯示使用者屬性的螢幕擷取畫面](media/user-properties/before-redemption.png)

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

- Guest：此值表示不被視為公司內部的使用者，例如外部共同作業者、合作夥伴或客戶。 這類使用者不會收到執行長的內部備忘，或收到公司權益等。

  > [!NOTE]
  > UserType 與使用者登入的方式、使用者的目錄角色等等無關。 此屬性只是表示使用者與主機組織的關聯性，並可讓組織強制執行此屬性的相依原則。

### <a name="source"></a>來源
此屬性指出使用者的登入方式。

- 受邀使用者︰此使用者已受邀請，但尚未兌換邀請。

- 外部 Azure Active Directory：此使用者位於外部組織，並使用屬於其他組織的 Azure AD 帳戶進行驗證。 這類型的登入對應於狀態 1。

- Microsoft 帳戶：此使用者位於 Microsoft 帳戶，並使用 Microsoft 帳戶進行驗證。 這類型的登入對應於狀態 2。

- Windows Server Active Directory︰此使用者已從屬於此組織的內部部署 Active Directory 登入。 這類型的登入對應於狀態 3。

- Azure Active Directory︰此使用者會使用屬於此組織的 Azure AD 帳戶進行驗證。 這類型的登入對應於狀態 4。
  > [!NOTE]
  > Source 和 UserType 是獨立的屬性。 Source 的值不代表特定的 UserType 值。

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Azure AD B2B 使用者是否可新增為成員而非來賓？
一般而言，Azure AD B2B 使用者和來賓使用者的意義相同。 因此，Azure AD B2B 共同作業使用者依預設是新增為具有 UserType = [來賓] 的使用者。 不過，在某些情況下，合作夥伴組織是主機組織也所屬之較大組織的成員。 若是如此，主機組織可能會將合作夥伴組織中的使用者視為成員而非來賓。 使用 Azure AD B2B 邀請管理員 API 來從夥伴組織新增或邀請使用者至主機組織作為成員。

## <a name="filter-for-guest-users-in-the-directory"></a>篩選目錄中的來賓使用者

![顯示來賓使用者篩選的螢幕擷取畫面](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>轉換 UserType
可能會使用 PowerShell 將 UserType 從 [成員] 轉換為 [來賓]，反之亦然。 不過，UserType 屬性會代表使用者與組織的關聯性。 因此，只有在使用者與組織的關聯性變更時，才應該變更此屬。 如果使用者的關聯性變更，是否應該變更使用者主體名稱 (UPN)？ 使用者是否應該繼續存取相同的資源？ 是否應指派信箱？ 我們不建議以 PowerShell 做為不可部分完成的活動來變更 UserType。 此外，為免使用 PowerShell 讓這個屬性成為不可變屬性，我們不建議採用此值的相依性。

## <a name="remove-guest-user-limitations"></a>移除來賓使用者限制
在某些情況下，您要提供來賓使用者更高的權限。 您可以將來賓使用者新增至任何角色，甚至移除目錄中預設的來賓使用者限制，為使用者提供與成員相同的權限。

可以關閉預設限制，如此公司目錄中的來賓使用者就有與成員使用者相同的權限。

![顯示 [使用者設定] 中 [外部使用者] 選項的螢幕擷取畫面](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>能否在 Exchange 全域通訊清單中顯示來賓使用者？
是。 根據預設，來賓物件不會在貴組織的全域通訊清單中顯示，但您可以使用 Azure Active Directory PowerShell 讓其顯示。 如需詳細資訊，請參閱[管理 Office 365 群組中的來賓存取](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups) \(機器翻譯\) 中的＜能否在全域通訊清單中顯示來賓物件？＞****。

## <a name="can-i-update-a-guest-users-email-address"></a>我可以更新來賓使用者的電子郵件地址嗎？

如果來賓使用者接受您的邀請，且後續變更其電子郵件地址，新的電子郵件不會自動同步至您目錄中的來賓使用者物件。 Mail 屬性是透過[MICROSOFT GRAPH API](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)建立的。 您可以透過 Exchange 系統管理中心或[Exchange Online PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/set-mailuser?view=exchange-ps)更新 mail 屬性，而變更會反映在 Azure AD 來賓使用者物件中。

## <a name="next-steps"></a>後續步驟

* [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
* [B2B 共同作業使用者權杖](user-token.md)
* [B2B 共同作業使用者宣告對應](claims-mapping.md)
