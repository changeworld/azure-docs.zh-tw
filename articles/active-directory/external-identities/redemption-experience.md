---
title: B2B 共同作業中的邀請兌換 - Azure AD
description: 描述使用者的 Azure AD B2B 共同作業邀請兌換經驗，包括隱私權條款的協議。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2427d974f96c0905ea2eb33daea7c89de277ec9
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441805"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 共同作業邀請兌換

本文說明來賓使用者如何存取您的資源，以及他們將會遇到的同意程序。 如果您將邀請電子郵件傳送給來賓，邀請會包含一個連結，讓來賓可兌換以存取您的應用程式或入口網站。 邀請電子郵件只是來賓可存取您資源的其中一種方式。 或者，您可將來賓新增至您的目錄，並為他們提供所要共用入口網站或應用程式的直接連結。 無論來賓使用何種方法，系統都會引導他們進行第一次的同意程序。 此程式可確保您的來賓同意隱私權條款，並接受您已設定的任何[使用規定](../conditional-access/terms-of-use.md)。

當您將來賓使用者新增到您的目錄時，來賓使用者帳戶的同意狀態 (可在 PowerShell 中檢視) 最初會設定為 **PendingAcceptance**。 此設定會保留到來賓接受您的邀請並同意您的隱私權原則和使用條款為止。 之後，同意狀態會變更為 [已接受]，而不再對來賓呈現同意頁面。

   > [!IMPORTANT]
   > **自 2021 年 3 月 31 日起**，Microsoft 將不再支援兌換邀請，而是建立適用於 B2B 共同作業案例的非受控 Azure AD 帳戶和租用戶。 在準備過程中，我們鼓勵客戶選擇使用[電子郵件一次性密碼驗證](one-time-passcode.md)。 我們歡迎您提供此公開預覽功能的意見反應，而且期待能建立更多共同作業的方式。

## <a name="redemption-through-the-invitation-email"></a>透過邀請電子郵件兌換

當您[使用 Azure 入口網站](./b2b-quickstart-add-guest-users-portal.md)將來賓使用者新增至您的目錄時，系統會將邀請電子郵件傳送給處理中的來賓。 當您[使用 PowerShell](./b2b-quickstart-invite-powershell.md)將來賓使用者新增到您的目錄時，也可選擇傳送邀請電子郵件。 以下說明來賓在兌換電子郵件中的連結時的體驗。

1. 來賓會收到從 **Microsoft Invitations** 傳送的[邀請電子郵件](./invitation-email-elements.md)。
2. 來賓會在電子郵件中選取 [接受邀請]。
3. 來賓會使用自己的認證來登入您的目錄。 如果來賓沒有可與您的目錄同盟的帳戶，且未啟用[電子郵件一次性密碼 (OTP)](./one-time-passcode.md) 功能，則系統會提示來賓建立個人 [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) 或 [Azure AD 自助式帳戶](../users-groups-roles/directory-self-service-signup.md)。 如需詳細資訊，請參閱[邀請兌換流程](#invitation-redemption-flow)。
4. 系統會引導來賓進行[同意體驗](#consent-experience-for-the-guest)，如下所述。

## <a name="redemption-through-a-direct-link"></a>透過直接連結兌換

除了邀請電子郵件以外，您也可以為來賓提供應用程式或入口網站的直接連結。 您必須先透過 [Azure 入口網站](./b2b-quickstart-add-guest-users-portal.md)或 [PowerShell](./b2b-quickstart-invite-powershell.md)，將來賓使用者新增至您的目錄。 接著，您可使用任何[可自訂的方式將應用程式部署至使用者](../manage-apps/end-user-experiences.md)，包括直接登入連結。 當來賓使用直接連結而不是邀請電子郵件時，系統仍會引導他們進行第一次的同意體驗。

> [!IMPORTANT]
> 直接連結必須是租用戶專用的。 換句話說，其必須包含租用戶識別碼或已驗證的網域，才能在您的租用戶 (共用應用程式所在的位置) 中驗證來賓。 一般 URL (例如 https://myapps.microsoft.com ) 不適用於來賓，因為其會重新導向至其主租用戶進行驗證。 以下是一些具有租用戶內容的直接連結範例：
 > - 應用程式存取面板：`https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - 已驗證網域的應用程式存取面板：`https://myapps.microsoft.com/<;verified domain>`
 > - Azure 入口網站：`https://portal.azure.com/<tenant id>`
 > - 個別應用程式：請參閱如何使用[直接登入連結](../manage-apps/end-user-experiences.md#direct-sign-on-links)

在某些情況下，建議您透過直接連結來使用邀請電子郵件。 如果這些特殊情況對您的組織很重要，建議您使用仍會傳送邀請電子郵件的方法來邀請使用者：
 - 使用者在同盟組織中沒有 Azure AD 帳戶、MSA 或電子郵件帳戶。 除非您使用一次性密碼功能，否則來賓必須兌換邀請電子郵件，系統才會引導他們完成建立 MSA 的步驟。
 - 受邀的使用者物件有時可能因為與連絡人物件 (例如 Outlook 連絡人物件) 衝突，而沒有電子郵件地址。 在此情況下，使用者必須按一下邀請電子郵件中的兌換 URL。
 - 使用者可以使用受邀的電子郵件地址別名登入。 (別名是與電子郵件帳戶相關聯的其他電子郵件位址。)在此情況下，使用者必須按一下邀請電子郵件中的兌換 URL。

## <a name="invitation-redemption-flow"></a>邀請兌換流程

當使用者按一下[邀請電子郵件](invitation-email-elements.md)中的 [接受邀請] 連結時，Azure AD 會根據如下所示的兌換流程自動兌換邀請：

![顯示兌換流程圖的螢幕擷取畫面](media/redemption-experience/invitation-redemption-flow.png)

**如果使用者的使用者主體名稱 (UPN) 與現有 Azure AD 和個人 MSA 帳戶相符，則會提示使用者選擇他們想要進行兌換的帳戶。*

1. Azure AD 會執行以使用者為基礎的探索，以判斷使用者是否存在於 [現有的 Azure AD 租用戶](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal)中。

2. 如果系統管理員已啟用[直接同盟](./direct-federation.md)，Azure AD 會檢查使用者的網域尾碼是否符合所設定 SAML/WS-同盟識別提供者的網域，並將使用者重新導向至預先設定的識別提供者。

3. 如果系統管理員已啟用 [Google 同盟](./google-federation.md)，Azure AD 會檢查使用者的網域尾碼是否為 gmail.com 或 googlemail.com，並將使用者重新導向至 Google。

4. 兌換程序會檢查使用者是否有現有的個人 [Microsoft 帳戶 (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。

5. 一旦識別出使用者的**主目錄**，使用者就會被傳送至對應的識別提供者以進行登入。  

6. 如果步驟 1 到 4 找不到受邀使用者的主目錄，則 Azure AD 會判斷邀請租用戶是否已啟用來賓的[電子郵件一次性密碼 (OTP)](./one-time-passcode.md) 功能。

7. 如果[已啟用來賓的電子郵件一次性密碼](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)，則會透過受邀的電子郵件將密碼傳送給使用者。 使用者將會在 Azure AD 登入頁面中，擷取並輸入此密碼。

8. 如果已停用來賓的電子郵件一次性密碼，Azure AD 會檢查網域尾碼，以判斷其是否屬於取用者帳戶。 若是如此，系統會提示使用者建立個人 [Microsoft 帳戶](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。 若非如此，系統會提示使用者建立 [Azure AD 自助式帳戶](../users-groups-roles/directory-self-service-signup.md)。

9. Azure AD 藉由驗證電子郵件的存取權，嘗試建立 [Azure AD 自助式帳戶](../users-groups-roles/directory-self-service-signup.md)。 將程式碼傳送至電子郵件，並且讓使用者擷取並提交給 Azure AD，即可驗證帳戶是否已完成。 不過，如果受邀使用者的租用戶已同盟，或 AllowEmailVerifiedUsers 欄位在受邀使用者的租用戶中設定為 False，則使用者無法完成兌換，且流程會產生錯誤。 如需詳細資訊，請參閱 [Azure Active Directory B2B 共同作業疑難排解](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption)。

10. 系統會提示使用者建立個人 [Microsoft 帳戶 (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。

11. 向適當的識別提供者進行驗證後，使用者會被重新導向至 Azure AD 以進行[同意體驗](#consent-experience-for-the-guest)。  

若為 Just-In-Time (JIT) 兌換 (透過租用戶應用程式連結兌換)，則無法使用步驟 8 到 10。 如果使用者到達步驟 6，且未啟用電子郵件一次性密碼功能，則使用者會收到錯誤訊息，且無法兌換邀請。 若要避免此錯誤，系統管理員應該[啟用電子郵件一次性密碼](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)或確保使用者按一下邀請連結。

## <a name="consent-experience-for-the-guest"></a>來賓的同意體驗

當來賓第一次登入以存取合作夥伴組織中的資源時，系統會引導他們進行下列頁面。 

1. 來賓會檢閱 [審查權限] 頁面，其中描述邀請組織的隱私權聲明。 使用者必須根據邀請組織的隱私權原則 [接受] 其資訊的使用方式，才能繼續執行。

   ![顯示 [檢閱權限] 頁面的螢幕擷取畫面](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 有關身為租用戶管理員的您如何連結至貴組織隱私權聲明的詳細資訊，請參閱[操作說明：在 Azure Active Directory 中新增貴組織的隱私權資訊](../fundamentals/active-directory-properties-area.md)。

2. 如果已設定使用規定，則來賓會開啟並檢閱使用規定，然後選取 [接受]。 

   ![顯示新增使用規定的螢幕擷取畫面](media/redemption-experience/terms-of-use-accept.png) 

   您可以在 [外部身分識別] > [使用規定] 中，設定[使用規定](../conditional-access/terms-of-use.md)。

3. 除非另有指定，否則會將來賓重新導向至應用程式存取面板，其中會列出來賓可以存取的應用程式。

   ![顯示應用程式存取面板的螢幕擷取畫面](media/redemption-experience/myapps.png) 

在您的目錄中，來賓的 [邀請已接受] 值會變更為 [是]。 如果已建立 MSA，來賓的 [來源] 會顯示 [Microsoft 帳戶]。 如需來賓使用者帳戶屬性的詳細資訊，請參閱 [Azure AD B2B 共同作業使用者的屬性](user-properties.md)。 

## <a name="next-steps"></a>後續步驟

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](add-users-administrator.md)
- [資訊工作者如何將 B2B 共同作業使用者新增到 Azure Active Directory？](add-users-information-worker.md)
- [使用 PowerShell 新增 Azure Active Directory B2B 共同作業使用者](customize-invitation-api.md#powershell)
- [以來賓使用者的身分從組織離職](leave-the-organization.md)