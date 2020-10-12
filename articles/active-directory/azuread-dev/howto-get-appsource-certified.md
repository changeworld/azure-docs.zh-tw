---
title: 如何讓 AppSource 取得 Azure Active Directory (AD) 認證 | Microsoft Docs
description: 有關如何讓應用程式 AppSource 取得 Azure Active Directory 認證的詳細資料。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 85c4488c4e8e1e2f9591b7d734c522c34ea4ba2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88116308"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>如何讓 AppSource 取得 Azure Active Directory 認證

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/) 是企業用戶探索、嘗試，及管理企業營運 SaaS 應用程式 (獨立 SaaS 和現有 Microsoft SaaS 產品的附加元件) 的目的地。

若要列出 AppSource 上的獨立 SaaS 應用程式，您的應用程式必須接受來自具備 Azure Active Directory (Azure AD) 之任何公司或組織的公司帳戶的單一登入。 登入流程必須使用 [OpenID Connect](v1-protocols-openid-connect-code.md) 或 [OAuth 2.0](v1-protocols-oauth-code.md) 通訊協定。 AppSource 認證不接受 SAML 整合。

## <a name="guides-and-code-samples"></a>指南與程式碼範例

如果您想要瞭解如何使用 Open ID connect 來整合應用程式與 Azure AD，請遵循《 [Azure Active Directory 開發人員指南》](v1-overview.md#get-started "為開發人員提供 Azure AD 的開始")中的指南和程式碼範例。

## <a name="multi-tenant-applications"></a>多租用戶應用程式

「多租用戶應用程式」** 是一個應用程式，可接受來自具備 Azure AD 之公司或組織的使用者單一登入，而不需個別的執行個體、設定或部署。 AppSource 建議應用程式實行多重租用，以啟用*單鍵*免費試用體驗。

若要在您的應用程式上啟用多重租用，請依照下列步驟執行：
1. 在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)中，將應用程式註冊資訊上的 `Multi-Tenanted` 屬性設為 `Yes`。 預設會將在 Azure 入口網站中建立的應用程式設為[單一租用戶](#single-tenant-applications)**。
1. 更新您的程式碼，以將要求傳送給 `common` 端點。 若要這樣做，請將端點從 `https://login.microsoftonline.com/{yourtenant}` 更新為 `https://login.microsoftonline.com/common*`。
1. 針對某些平台 (例如 ASP.NET)，您還需要更新程式碼以接受多個簽發者。

如需多重租用的詳細資訊，請參閱[如何使用多租用戶應用程式模式登入任何 Azure Active Directory (Azure AD) 使用者](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

### <a name="single-tenant-applications"></a>單一租用戶應用程式

「單一租用戶應用程式」** 是一個應用程式，僅接受來自所定義之 Azure AD 執行個體的使用者登入。 將每個使用者以來賓帳戶的身分新增至應用程式註冊所在的 Azure AD 執行個體之後，外部使用者 (包括來自其他組織的公司或學校帳戶，或個人帳戶) 就可以登入單一租用戶應用程式。 

您可以透過 [Azure AD B2B 共同作業](../external-identities/what-is-b2b.md)將使用者以來賓帳戶的身分新增至 Azure AD，而且能[以程式設計方式](../../active-directory-b2c/code-samples.md)執行此動作。 使用 B2B 時，使用者可以建立不需邀請即可登入的自助入口網站。 如需詳細資訊，請參閱 [Azure AD B2B 共同作業註冊的自助入口網站](../external-identities/self-service-portal.md)。

單一租用戶應用程式可以啟用「與我連絡」** 體驗，但如果您想要啟用 AppSource 建議的單鍵/免費試用體驗，可改為在您的應用程式上啟用多重租用。

## <a name="appsource-trial-experiences"></a>AppSource 試用體驗

### <a name="free-trial-customer-led-trial-experience"></a>免費試用 (客戶導向的試用體驗)

客戶導向的試用是 AppSource 建議的體驗，因為它可讓您單鍵存取至您的應用程式。 下列範例顯示此體驗的樣子：

<table >
<tr>    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt="Shows Free trial for customer-led trial experience."/><ul><li>使用者會在 AppSource 網站中尋找您的應用程式</li><li>選取 [免費試用] 選項</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt="Shows how user is redirected to a URL in your web site."/><ul><li>AppSource 將使用者重新導向至網站中的 URL</li><li>您的網站會自動啟動<i>單一登入</i>流程 (在頁面載入時)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt="Shows the Microsoft sign-in page."/><ul><li>使用者已重新導向至 Microsoft 登入頁面</li><li>使用者提供認證以登入</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt="Example: Consent page for an application."/><ul><li>使用者同意您的應用程式</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site."/><ul><li>登入完成，而且使用者已重新導向回到您的網站</li><li>使用者啟動免費試用</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>與我連絡 (合作夥伴導向的試用體驗)

您可以在需要手動或長期操作來佈建使用者/公司時，使用合作夥伴試用體驗，例如，您的應用程式需要佈建虛擬機器、資料庫執行個體，或需要很多時間完成的作業。 在此情況下，當使用者選取 [要求試用]**** 按鈕並填妥表單之後，AppSource 就會將使用者的連絡資訊傳送給您。 當您接收到此資訊之後，接著要佈建環境，並將如何存取試用體驗的指示傳送給使用者：<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt="Shows Contact me for partner-led trial experience"/><ul><li>使用者會在 AppSource 網站中尋找您的應用程式</li><li>選取 [Contact Me] 選項</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt="Shows an example form with contact info"/><ul><li>將連絡人資訊填入表單</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt="Shows placeholder for user information"/></td>
            <td>您會收到使用者資訊</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt="Shows placeholder for setup environment info"/></td>
            <td>設定環境</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt="Shows placeholder for trial info"/></td>
            <td>連絡使用者試用資訊</td>
        </tr>
        </table><br/><br/>
        <ul><li>您收到使用者資訊並設定試用執行個體</li><li>將存取您應用程式的超連結傳送給使用者</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt="Shows the application sign-in screen"/><ul><li>使用者存取您的應用程式，並完成單一登入流程</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt="Shows an example consent page for an application"/><ul><li>使用者同意您的應用程式</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site"/><ul><li>登入完成，而且使用者已重新導向回到您的網站</li><li>使用者啟動免費試用</li></ul></td>  
</tr>
</table>

### <a name="more-information"></a>詳細資訊

如需 AppSource 試用體驗的詳細資訊，請參閱[這段影片](https://aka.ms/trialexperienceforwebapps)。 

## <a name="next-steps"></a>後續步驟

- 如需建置支援 Azure AD 登入之應用程式的詳細資訊，請參閱 [Azure AD 的驗證案例](./v1-authentication-scenarios.md)。
- 如需如何列出 AppSource 中的 SaaS 應用程式的資訊，請參閱 [AppSource 合作夥伴資訊](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>取得支援

對於 Azure AD 整合，我們使用 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) \(英文\) 搭配社群來提供支援。

我們強烈建議您先在 Stack Overflow 上詢問您的問題，並瀏覽現有的問題，以查看先前是否有人已提出您的問題。 請確定您的問題或意見已標記[ `[azure-active-directory]` 和 `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->