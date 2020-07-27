---
title: 取得 Azure Active Directory 的 AppSource 認證 |Microsoft Docs
description: 有關如何讓應用程式 AppSource 取得 Azure Active Directory 認證的詳細資料。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 18102b5501bcfca0aabc6609b8472aa45c23ec7f
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172566"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>取得 Azure Active Directory 的 AppSource 認證

[Microsoft AppSource](https://appsource.microsoft.com/) 是企業用戶探索、嘗試，及管理企業營運 SaaS 應用程式 (獨立 SaaS 和現有 Microsoft SaaS 產品的附加元件) 的目的地。

若要列出 AppSource 上的獨立 SaaS 應用程式，您的應用程式必須接受來自具備 Azure Active Directory (Azure AD) 之任何公司或組織的公司帳戶的單一登入。 登入流程必須使用 [OpenID Connect](v2-protocols-oidc.md) 或 [OAuth 2.0](v2-oauth2-auth-code-flow.md) 通訊協定。 AppSource 認證不接受 SAML 整合。

## <a name="multi-tenant-applications"></a>多租用戶應用程式

「多租用戶應用程式」** 是一個應用程式，可接受來自具備 Azure AD 之公司或組織的使用者單一登入，而不需個別的執行個體、設定或部署。 AppSource 建議應用程式實行多重租用，以啟用*單鍵*免費試用體驗。

若要在您的應用程式上啟用多重租用，請依照下列步驟執行：
1. 在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)中，將應用程式註冊資訊上的 `Multi-Tenanted` 屬性設為 `Yes`。 預設會將在 Azure 入口網站中建立的應用程式設為[單一租用戶](#single-tenant-applications)**。
1. 更新您的程式碼，以將要求傳送給 `common` 端點。 若要這樣做，請將端點從 `https://login.microsoftonline.com/{yourtenant}` 更新為 `https://login.microsoftonline.com/common*`。
1. 對於某些平臺（例如 ASP.NET），您也需要更新您的程式碼，以接受多個簽發者。

如需多重租用的詳細資訊，請參閱[如何使用多租用戶應用程式模式登入任何 Azure Active Directory (Azure AD) 使用者](howto-convert-app-to-be-multi-tenant.md)。

### <a name="single-tenant-applications"></a>單一租用戶應用程式

「單一租用戶應用程式」** 是一個應用程式，僅接受來自所定義之 Azure AD 執行個體的使用者登入。 將每個使用者以來賓帳戶的身分新增至應用程式註冊所在的 Azure AD 執行個體之後，外部使用者 (包括來自其他組織的公司或學校帳戶，或個人帳戶) 就可以登入單一租用戶應用程式。 

您可以透過 [Azure AD B2B 共同作業](../b2b/what-is-b2b.md)將使用者以來賓帳戶的身分新增至 Azure AD，而且能[以程式設計方式](../../active-directory-b2c/code-samples.md)執行此動作。 使用 B2B 時，使用者可以建立不需邀請即可登入的自助入口網站。 如需詳細資訊，請參閱 [Azure AD B2B 共同作業註冊的自助入口網站](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)。

單一租用戶應用程式可以啟用「與我連絡」** 體驗，但如果您想要啟用 AppSource 建議的單鍵/免費試用體驗，可改為在您的應用程式上啟用多重租用。

## <a name="appsource-trial-experiences"></a>AppSource 試用體驗

### <a name="free-trial-customer-led-trial-experience"></a>免費試用 (客戶導向的試用體驗)

客戶導向的試用是 AppSource 建議的體驗，因為它可讓您單鍵存取至您的應用程式。 下列範例顯示此體驗的樣子：

1.  使用者在 AppSource 網站中尋找您的應用程式，然後選取 [**免費試用**] 選項。

    ![顯示客戶導向試用體驗的免費試用版](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  AppSource 會將使用者重新導向至您網站中的 URL。 您的網站會自動啟動*單一登入*進程（在頁面載入時）。

    ![顯示如何將使用者重新導向至您網站中的 URL](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  使用者會重新導向至 Microsoft 登入頁面，而使用者會提供認證來登入。

    ![顯示 Microsoft 登入頁面](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. 使用者同意您的應用程式。

    ![範例：應用程式的同意頁面](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  登入完成，並將使用者重新導向回到您的網站。  使用者啟動免費試用版。

    ![顯示使用者重新導向回到您的網站時所看到的體驗](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>與我連絡 (合作夥伴導向的試用體驗)

您可以在需要手動或長期操作來佈建使用者/公司時，使用合作夥伴試用體驗，例如，您的應用程式需要佈建虛擬機器、資料庫執行個體，或需要很多時間完成的作業。 在此情況下，當使用者選取 [要求試用]**** 按鈕並填妥表單之後，AppSource 就會將使用者的連絡資訊傳送給您。 當您接收到此資訊之後，接著要佈建環境，並將如何存取試用體驗的指示傳送給使用者：<br/><br/>

1. 使用者會在 AppSource 網站中尋找您的應用程式，然後選取 [**聯絡我**]。

    ![顯示合作夥伴導向的試用體驗的連絡人](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. 使用者以連絡人資訊填滿表單。

    ![顯示包含連絡人資訊的範例表單](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. 您會收到使用者的資訊、設定試用實例，並將超連結傳送給使用者，以存取您的應用程式。

    ![顯示使用者資訊的預留位置](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. 使用者會存取您的應用程式，並完成單一登入進程。

    ![顯示應用程式登入畫面](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. 使用者同意您的應用程式。

    ![顯示應用程式的範例同意頁面](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. 登入完成，並將使用者重新導向回到您的網站。 使用者啟動免費試用版。

    ![顯示使用者重新導向回到您的網站時所看到的體驗](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>更多資訊

如需 AppSource 試用體驗的詳細資訊，請參閱[這段影片](https://aka.ms/trialexperienceforwebapps)。 

## <a name="get-support"></a>取得支援

對於 Azure AD 整合，我們使用 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) \(英文\) 搭配社群來提供支援。

我們強烈建議您先在 Stack Overflow 上詢問您的問題，並瀏覽現有的問題，以查看先前是否有人已提出您的問題。 請確定您的問題或意見已標記為[ `[azure-active-directory]` 和 `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

## <a name="next-steps"></a>後續步驟

- 如需建置支援 Azure AD 登入之應用程式的詳細資訊，請參閱 [Azure AD 的驗證案例](authentication-flows-app-scenarios.md)。
- 如需如何列出 AppSource 中的 SaaS 應用程式的資訊，請參閱 [AppSource 合作夥伴資訊](https://appsource.microsoft.com/partners)



