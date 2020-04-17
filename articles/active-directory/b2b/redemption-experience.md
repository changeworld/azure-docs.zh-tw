---
title: B2B 協作中的邀請兌換 - Azure AD
description: 描述使用者的 Azure AD B2B 共同作業邀請兌換經驗，包括隱私權條款的協議。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0645807aa40557c163643f1393c310668518f9be
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535125"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 共同作業邀請兌換

本文介紹了來賓使用者訪問您的資源的方式以及他們遇到的同意過程。 如果您向來賓發送邀請電子郵件,邀請包括該來賓可以兌換的連結,以便訪問您的應用或門戶。 邀請電子郵件只是來賓訪問資源的方式之一。 作為替代方法,您可以將來賓添加到目錄,並為他們提供指向要共用的門戶或應用的直接連結。 無論他們使用哪種方法,客人都會通過首次同意程式。 此過程可確保您的客人同意隱私條款,並接受您設置的任何[使用條款](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)。

當您將來賓使用者添加到目錄時,來賓使用者帳戶具有同意狀態(在 PowerShell 中查看),最初設置為 **"掛起接受"。** 此設置將保留,直到客人接受您的邀請並同意您的隱私政策和使用條款。 之後,同意狀態更改為 **「已接受**」,並且同意頁不再呈現給來賓。

   > [!IMPORTANT]
   > **從 2021 年 3 月 31 日起**,Microsoft 將不再支援為 B2B 協作方案創建非託管 Azure AD 帳戶和租戶來兌換邀請。 在準備中,我們鼓勵客戶選擇[電子郵件一次性密碼身份驗證](one-time-passcode.md)。 我們歡迎您對此公共預覽功能的反饋,並興奮地創建更多協作方式。

## <a name="redemption-through-the-invitation-email"></a>透過邀請電子郵件兌換

使用[Azure 入口將來](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)賓使用者添加到目錄時,將在此過程中向來賓發送邀請電子郵件。 當您[使用 PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)將來賓使用者添加到目錄中時,您還可以選擇發送邀請電子郵件。 以下是客人在電子郵件中兌換連結時的體驗描述。

1. 來賓收到從**Microsoft 郵件**[送的邀請電子郵件](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)。
2. 以電子郵件中**開始**。
3. 如果來賓在聯合組織中沒有 Azure AD 帳戶、Microsoft 帳戶 (MSA) 或電子郵件帳戶,系統將提示他們創建 MSA(除非啟用了[一次性密碼](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)功能,不需要 MSA)。
4. 客人將體驗以下的[同意體驗](#consent-experience-for-the-guest)。

## <a name="redemption-through-a-direct-link"></a>透過直接連結兌換

作為邀請電子郵件的替代方法,您可以向來賓提供指向應用或門戶的直接連結。 首先需要透過[Azure 門戶](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)或[PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)將來賓使用者添加到目錄中。 然後,您可以使用任何[可自定義的方式將應用程式部署到使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences),包括直接登錄連結。 當來賓使用直接連結而不是邀請電子郵件時,他們仍將獲得首次同意體驗的指導。

> [!IMPORTANT]
> 直接連結必須特定於租戶。 換句話說,它必須包括租戶 ID 或已驗證的域,以便可以在共用應用所在的租戶中對來賓進行身份驗證。 類似https://myapps.microsoft.com這樣的通用 URL 對來賓不起作用,因為它將重定向到其主租戶進行身份驗證。 下面是一些與租戶上下文的直接連結的示例:
 > - 套用存取https://myapps.microsoft.com/?tenantid=&lt面板: ;租戶 ID&gt; 
 > - 已驗證網域的應用存取https://myapps.microsoft.com/&lt面板: ;已驗證網域&gt;
 > - Azure 門https://portal.azure.com/&lt戶 : ;租戶 ID&gt;
 > - 單套應用:瞭解如何使用[直接登入連結](../manage-apps/end-user-experiences.md#direct-sign-on-links)

在某些情況下,建議通過直接連結發送邀請電子郵件。 如果這些特殊情況對您的組織很重要,我們建議您使用仍發送邀請電子郵件的方法邀請使用者:
 - 使用者在聯合組織中沒有 Azure AD 帳戶、MSA 或電子郵件帳戶。 除非您使用的是一次性密碼功能,否則來賓需要兌換邀請電子郵件,以便指導您完成創建 MSA 的步驟。
 - 受邀的使用者物件有時可能因為與連絡人物件 (例如 Outlook 連絡人物件) 衝突，而沒有電子郵件地址。 在此情況下，使用者必須按一下邀請電子郵件中的兌換 URL。
 - 使用者可以使用受邀的電子郵件地址別名登入。 (別名是與電子郵件帳戶關聯的其他電子郵寄地址。在這種情況下,用戶必須單擊邀請電子郵件中的兌換 URL。

## <a name="invitation-redemption-flow"></a>邀請兌換流程

當使用者按一下[邀請電子郵件中](invitation-email-elements.md)的 **「接受邀請**」連結時,Azure AD 會根據兌換流程自動兌換邀請,如下所示:

![顯示兌換流程圖的螢幕截圖](media/redemption-experience/invitation-redemption-flow.png)

1. 兌換過程檢查使用者是否擁有現有的個人 Microsoft[帳戶 (MSA)。](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)

2. 如果管理員已啟用[直接聯合](direct-federation.md),Azure AD 將檢查使用者的網後綴是否與配置的 SAML/WS-Fed 標識提供者的網域匹配,並將使用者重定向到預先設定的標識提供者。

3. 如果管理員啟用了[Google 聯合身份驗證](google-federation.md),Azure AD 會檢查使用者的網功能變數名稱後綴是否gmail.com或googlemail.com,並將使用者重定向到 Google。

4. Azure AD 執行基於使用者的發現,以確定使用者是否存在[在現有 Azure AD 租戶](what-is-b2b.md#easily-add-guest-users-in-the-azure-ad-portal)中。

5. 識別使用者**主目錄**后,使用者將發送到相應的標識提供程式登錄。  

6. 如果步驟 1 到步驟 4 找不到受邀使用者主目錄,Azure AD 將確定邀請租戶是否已為來賓啟用[電子郵件一次性密碼 (OTP)](one-time-passcode.md)功能。

7. 如果[啟用了來賓的電子郵件一次性密碼](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode),則密碼將通過邀請的電子郵件發送給使用者。 使用者將在 Azure AD 登錄頁中檢索並輸入此密碼。

8. 如果禁用了來賓的電子郵件一次性密碼,Azure AD 會根據 Microsoft 維護的消費域清單檢查域後綴。 如果域與消費者域清單中的任何域匹配,系統將提示使用者創建個人 Microsoft 帳戶。 如果沒有,系統將提示使用者創建 Azure [AD 自助服務帳戶](../users-groups-roles/directory-self-service-signup.md)(病毒帳戶)。

9. Azure AD 嘗試透過驗證對電子郵件的存取來創建 Azure AD 自助服務帳戶(病毒帳戶)。 通過將代碼發送到電子郵件,並讓用戶檢索並提交到 Azure AD,驗證帳戶。 但是,如果受邀使用者的租戶是聯合的,或者如果"AllowEmail 驗證使用者"欄位在受邀使用者的租戶中設置為 false,則使用者無法完成兌換,並且流會導致錯誤。 有關詳細資訊,請參閱[對 Azure 活動目錄 B2B 協作進行故障排除](troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption)。

10. 系統會提示用戶創建個人 Microsoft 帳戶 (MSA)。

11. 向正確的識別提供者進行身份驗證後,使用者將被重定向到 Azure AD 以完成[同意體驗](redemption-experience.md#consent-experience-for-the-guest)。  

對於及時 (JIT) 兌換(如果兌換是通過租戶應用程式連結),則步驟 8 到 10 不可用。 如果使用者到達步驟 6 且未啟用電子郵件一次性密碼功能,則使用者將收到一條錯誤消息,無法兌換邀請。 為了防止這種情況,管理員應[啟用電子郵件一次性密碼](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)或確保使用者按一下邀請連結。

## <a name="consent-experience-for-the-guest"></a>客人的同意體驗

當來賓首次登錄以訪問合作夥伴組織中的資源時,他們會通過以下頁面進行引導。 

1. 來賓查看描述邀請組織隱私聲明的 **「審閱權限**」 頁。 用戶**必須接受根據**邀請組織的隱私政策繼續使用其資訊。

   ![顯示 [檢閱權限] 頁面的螢幕擷取畫面](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 有關身為租用戶管理員的您如何連結至貴組織隱私權聲明的詳細資訊，請參閱[做法：在 Azure Active Directory 中新增貴組織的隱私權資訊](https://aka.ms/adprivacystatement)。

2. 如果配置了使用條款,來賓將打開並查看使用條款,然後選擇"**接受**"。 

   ![顯示新增使用規定的螢幕擷取畫面](media/redemption-experience/terms-of-use-accept.png) 

   您可以在 **「管理** > **組織關係** > 」 使用**條款**「中設定 」[。](../governance/active-directory-tou.md)

3. 除非另有說明,否則來賓將重定向到"應用訪問"面板,該面板列出了來賓可以訪問的應用程式。

   ![顯示應用存取面板的螢幕擷取](media/redemption-experience/myapps.png) 

在目錄中,來賓接受**的邀請值**更改為 **"是**"。 如果建立 MSA,則來賓的**來源**將顯示**Microsoft 帳戶**。 有關來賓使用者帳戶屬性的詳細資訊,請參閱 Azure [AD B2B 協作使用者的屬性](user-properties.md)。 

## <a name="next-steps"></a>後續步驟

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](add-users-administrator.md)
- [資訊工作者如何將 B2B 共同作業使用者新增到 Azure Active Directory？](add-users-information-worker.md)
- [使用 PowerShell 新增 Azure Active Directory B2B 共同作業使用者](customize-invitation-api.md#powershell)
- [以來賓使用者的身分從組織離職](leave-the-organization.md)
