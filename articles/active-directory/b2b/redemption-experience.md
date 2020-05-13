---
title: B2B 共同作業中的邀請兌換-Azure AD
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
ms.openlocfilehash: 2bbfd0027f9de93ee939815853f5141044404c53
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199498"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 共同作業邀請兌換

本文說明來賓使用者可以如何存取您的資源，以及他們會遇到的同意程式。 如果您將邀請電子郵件傳送給來賓，邀請會包含一個連結，讓來賓可以兌換以存取您的應用程式或入口網站。 邀請電子郵件只是來賓可存取您資源的其中一種方式。 或者，您可以將來賓新增至您的目錄，並為他們提供您想要共用之入口網站或應用程式的直接連結。 無論使用的方法為何，都會引導來賓完成第一次的同意程式。 此程式可確保您的來賓同意隱私權條款，並接受您所設定[的任何使用條款](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)。

當您將來賓使用者新增到您的目錄時，來賓使用者帳戶的同意狀態（可在 PowerShell 中查看）最初會設定為**PendingAcceptance**。 這項設定會持續，直到來賓接受您的邀請並同意您的隱私權原則和使用條款。 之後，同意狀態會變更為 [已**接受**]，而同意頁面就不會再呈現給來賓。

   > [!IMPORTANT]
   > 自**2021 年3月31日起**，Microsoft 將不再支援兌換邀請，方法是建立適用于 B2B 共同作業案例的非受控 Azure AD 帳戶和租使用者。 在準備過程中，我們鼓勵客戶選擇[電子郵件一次性密碼驗證](one-time-passcode.md)。 我們歡迎您提供此公開預覽功能的意見反應，而且很高興能建立更多共同作業的方式。

## <a name="redemption-through-the-invitation-email"></a>透過邀請電子郵件兌換

當您[使用 Azure 入口網站](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)將來賓使用者新增至目錄時，系統會將邀請電子郵件傳送至程式中的來賓。 當您[使用 PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)將來賓使用者新增至您的目錄時，也可以選擇傳送邀請電子郵件。 以下說明當他們兌換電子郵件中的連結時，來賓的體驗。

1. 來賓會收到從**Microsoft 邀請**傳送的[邀請電子郵件](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)。
2. 來賓會在電子郵件中選取 [**接受邀請**]。
3. 來賓會使用自己的認證來登入您的目錄。 如果來賓沒有可與您的目錄同盟的帳戶，且未啟用[電子郵件單次密碼（OTP）](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)功能，系統會提示來賓建立個人[MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)或[Azure AD 自助帳戶](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)。 如需詳細資訊，請參閱[邀請兌換流程](#invitation-redemption-flow)。
4. 來賓會引導您完成以下所述的[同意體驗](#consent-experience-for-the-guest)。

## <a name="redemption-through-a-direct-link"></a>透過直接連結兌換

除了邀請電子郵件以外，您還可以為來賓提供應用程式或入口網站的直接連結。 您必須先透過[Azure 入口網站](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)或[PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)，將來賓使用者新增至您的目錄。 接著，您可以使用任何[可自訂的方式，將應用程式部署給使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)，包括直接登入連結。 當來賓使用直接連結而不是邀請電子郵件時，他們仍會引導您進行第一次的同意體驗。

> [!IMPORTANT]
> 直接連結必須是租使用者專用的。 換句話說，它必須包含租使用者識別碼或已驗證的網域，才能在您的租使用者（共用應用程式所在的位置）中驗證來賓。 類似的一般 URL https://myapps.microsoft.com 不適用於來賓，因為它會重新導向至其主租使用者進行驗證。 以下是一些直接連結與租使用者內容的範例：
 > - 應用程式存取面板： https://myapps.microsoft.com/?tenantid=&lt ; 租使用者識別碼&gt; 
 > - 已驗證網域的應用程式存取面板： https://myapps.microsoft.com/&lt ; 已驗證的網域&gt;
 > - Azure 入口網站： https://portal.azure.com/&lt ; 租使用者識別碼&gt;
 > - 個別應用程式：請參閱如何使用[直接登入連結](../manage-apps/end-user-experiences.md#direct-sign-on-links)

在某些情況下，建議您透過直接連結來使用邀請電子郵件。 如果這些特殊案例對您的組織來說很重要，建議您使用仍然會傳送邀請電子郵件的方法來邀請使用者：
 - 使用者在同盟組織中沒有 Azure AD 帳戶、MSA 或電子郵件帳戶。 除非您是使用單次密碼功能，否則來賓必須兌換邀請電子郵件，以引導您完成建立 MSA 的步驟。
 - 受邀的使用者物件有時可能因為與連絡人物件 (例如 Outlook 連絡人物件) 衝突，而沒有電子郵件地址。 在此情況下，使用者必須按一下邀請電子郵件中的兌換 URL。
 - 使用者可以使用受邀的電子郵件地址別名登入。 （別名是與電子郵件帳戶相關聯的其他電子郵件地址）。在此情況下，使用者必須按一下邀請電子郵件中的兌換 URL。

## <a name="invitation-redemption-flow"></a>邀請兌換流程

當使用者按一下[邀請電子郵件](invitation-email-elements.md)中的 [**接受邀請**] 連結時，Azure AD 會根據兌換流程自動贖回邀請，如下所示：

![顯示兌換流程圖的螢幕擷取畫面](media/redemption-experience/invitation-redemption-flow.png)

**如果使用者的使用者主體名稱（UPN）與現有的 Azure AD 和個人 MSA 帳戶相符，則會提示使用者選擇他們想要兌換的帳戶。*

1. Azure AD 會執行以使用者為基礎的探索，以判斷使用者是否存在於[現有的 Azure AD 租](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b#easily-add-guest-users-in-the-azure-ad-portal)使用者中。

2. 如果系統管理員已啟用[直接同盟](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)，Azure AD 會檢查使用者的網域尾碼是否符合已設定之 SAML/WS-送出識別提供者的網域，並將使用者重新導向至預先設定的身分識別提供者。

3. 如果系統管理員已啟用[Google 同盟](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)，Azure AD 會檢查使用者的網域尾碼是否為 gmail.com 或 googlemail.com，並將使用者重新導向至 Google。

4. 兌換程式會檢查使用者是否有現有的個人[Microsoft 帳戶（MSA）](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。

5. 一旦識別出使用者的**主目錄**，就會將使用者傳送至對應的識別提供者以進行登入。  

6. 如果步驟1到4找不到受邀使用者的主目錄，則 Azure AD 會判斷邀請的租使用者是否已啟用來賓的[電子郵件單次密碼（OTP）](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)功能。

7. 如果[已啟用來賓的電子郵件一次性密碼](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode)，則會透過受邀的電子郵件將密碼傳送給使用者。 使用者將會在 Azure AD 登入頁面中，取得並輸入此密碼。

8. 如果已停用來賓的電子郵件單次密碼，Azure AD 會檢查網域尾碼，以判斷它是否屬於取用者帳戶。 若是如此，系統會提示使用者建立個人[Microsoft 帳戶](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。 如果不是，系統會提示使用者建立[Azure AD 自助帳戶](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)。

9. Azure AD 嘗試藉由驗證電子郵件的存取權，來建立[Azure AD 自助帳戶](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)。 藉由將程式碼傳送至電子郵件，並讓使用者抓取並提交給 Azure AD，來驗證帳戶是否已完成。 不過，如果受邀使用者的租使用者為同盟，或受邀使用者的租使用者中的 AllowEmailVerifiedUsers 欄位設定為 false，則使用者將無法完成兌換，而且流程會產生錯誤。 如需詳細資訊，請參閱針對 B2B 共同作業[進行疑難排解 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/b2b/troubleshoot#the-user-that-i-invited-is-receiving-an-error-during-redemption)。

10. 系統會提示使用者建立個人[Microsoft 帳戶（MSA）](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。

11. 向正確的身分識別提供者驗證之後，使用者會被重新導向至 Azure AD 以完成[同意體驗](https://docs.microsoft.com/azure/active-directory/b2b/redemption-experience#consent-experience-for-the-guest)。  

若為即時（JIT）兌換次數，其中的兌換是透過租使用者應用程式連結，則無法使用步驟8到10。 如果使用者到達步驟6，而且 [電子郵件單次密碼] 功能未啟用，則使用者會收到錯誤訊息，且無法兌換邀請。 為避免此錯誤，系統管理員應該[啟用電子郵件單次密碼](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode)，或確保使用者按一下邀請連結。

## <a name="consent-experience-for-the-guest"></a>來賓的同意體驗

當來賓第一次登入以存取夥伴組織中的資源時，會引導您完成下列頁面。 

1. [來賓] 審查**審查許可權**頁面，說明邀請組織的隱私權聲明。 使用者必須根據邀請組織的隱私權原則，**接受**其資訊的使用，才能繼續。

   ![顯示 [檢閱權限] 頁面的螢幕擷取畫面](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 有關身為租用戶管理員的您如何連結至貴組織隱私權聲明的詳細資訊，請參閱[做法：在 Azure Active Directory 中新增貴組織的隱私權資訊](https://aka.ms/adprivacystatement)。

2. 如果已設定使用規定，來賓會開啟並審查使用規定，然後選取 [**接受**]。 

   ![顯示新增使用規定的螢幕擷取畫面](media/redemption-experience/terms-of-use-accept.png) 

   您可以 >**使用規定**設定**組織關聯**性（或**外部**身分識別）中[的使用](../governance/active-directory-tou.md)規定。

3. 除非另有指定，否則會將來賓重新導向至 [應用程式存取面板]，其中會列出來賓可以存取的應用程式。

   ![顯示應用程式存取面板的螢幕擷取畫面](media/redemption-experience/myapps.png) 

在您的目錄中，來賓的 [**接受的邀請**] 值會變更為 **[是]**。 如果已建立 MSA，來賓的**來源**會顯示**Microsoft 帳戶**。 如需來賓使用者帳戶屬性的詳細資訊，請參閱[AZURE AD B2B 共同作業使用者的屬性](user-properties.md)。 

## <a name="next-steps"></a>後續步驟

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](add-users-administrator.md)
- [資訊工作者如何將 B2B 共同作業使用者新增到 Azure Active Directory？](add-users-information-worker.md)
- [使用 PowerShell 新增 Azure Active Directory B2B 共同作業使用者](customize-invitation-api.md#powershell)
- [以來賓使用者的身分從組織離職](leave-the-organization.md)
