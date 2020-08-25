---
title: 在 Azure AD 應用程式資源庫中列出您的應用程式 |Microsoft Docs
description: 了解如何列出 Azure Active Directory 應用程式庫中支援單一登入的應用程式
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/20/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 662bda03fc2d389ed316bd2c7141d5e6a30cef1a
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814591"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>在 Azure Active Directory 應用程式庫中列出您的應用程式

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

本文說明如何在 Azure Active Directory (Azure AD) 應用程式資源庫中列出應用程式、執行 (SSO) 的單一登入，以及管理清單。

## <a name="what-is-the-azure-ad-application-gallery"></a>什麼是 Azure AD 應用程式庫？

- 客戶可找到最佳且適用的單一登入體驗。
- 應用程式的設定相當簡單且基本。
- 快速搜尋可在應用程式庫中找到您的應用程式。
- 免費、基本及進階 Azure AD 的客戶都可使用這項整合。
- 共同客戶可取得逐步設定教學課程。
- 使用系統進行跨網域身分識別管理的客戶 ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) 可以針對相同的應用程式使用布建。

## <a name="prerequisites"></a>必要條件

- 針對同盟應用程式 (開啟識別碼和 SAML/WS-饋送) ，應用程式必須支援軟體即服務 (SaaS) 模型，才能列在 Azure AD 應用程式庫中。 企業資源庫應用程式必須支援多個客戶設定，而不是任何特定客戶。
- 針對 Open ID Connect，必須 multitenanted 應用程式，而且必須為應用程式正確地執行 [Azure AD 同意架構](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 。 使用者可以將登入要求傳送至一般端點，讓任何客戶都能為應用程式提供同意。 您可以根據租用戶識別碼和在權杖中收到的使用者 UPN 來控制使用者存取權。
- 針對 SAML 2.0/WS-送出，您的應用程式必須能夠在 SP 或 IDP 模式下進行 SAML/WS-饋送 SSO 整合。 在您提交要求之前，請確定這項功能是否正常運作。
- 若為密碼 SSO，請確定您的應用程式支援表單驗證，才能完成密碼保存，讓單一登入能如預期般運作。
- 您需要一個永久的帳戶來進行測試，而且至少已註冊兩個使用者。

**如何取得開發人員的 Azure AD？**

您可以取得免費的測試帳戶，其中包含所有 premium Azure AD 功能-90 天免費，只要您使用它進行開發，就可以延長： https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>在入口網站中提交要求

在您測試過應用程式整合是否與 Azure AD 搭配運作之後，請在 [Microsoft 應用程式網路入口網站](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交應用程式要求。

如果您在登入後出現下列頁面，請聯絡 [AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 提供您要用來提交要求的電子郵件帳戶。 慣用的商業電子郵件地址 [name@yourbusiness.com](mailto:name@yourbusiness.com) 。 Azure AD 團隊會在 Microsoft 應用程式網路入口網站中新增該帳戶。

![SharePoint 入口網站上的存取要求訊息](./media/howto-app-gallery-listing/errorimage.png)

新增帳戶之後，您可以登入 Microsoft 應用程式網路入口網站。

如果在您登入後出現下列頁面，請在文字方塊中提供需要存取權的業務理由。 然後選取 [ **要求存取**]。

  ![SharePoint 入口網站上的商業理由方塊](./media/howto-app-gallery-listing/accessrequest.png)

我們的小組會檢閱此詳細資料，並據以提供存取權給您。 核准您的要求之後，您可以登入入口網站，並在首頁上選取 [ **提交要求 (ISV) ** ] 磚來提交要求。

![在首頁上提交要求 (ISV) 磚](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>登入入口網站的問題

如果您在登入時看到此錯誤，則以下是問題的詳細資料，而這就是您可以修正問題的方式。

* 如果您的登入遭到封鎖，如下所示：

  ![在資源庫中解析應用程式時發生問題](./media/howto-app-gallery-listing/blocked.png)

**發生了什麼事情：**

來賓使用者會與主租使用者同盟，也就是 Azure AD。 來賓使用者具有高風險。 Microsoft 不允許高風險使用者存取其資源。 所有高風險使用者 (員工或來賓/廠商) 都必須補救/關閉其存取 Microsoft 資源的風險。 若是來賓使用者，此使用者風險來自于 home 租使用者，而此原則來自 (Microsoft 的資源租使用者) 。
 
**安全解決方案：**

* MFA 註冊的來賓使用者會補救自己的使用者風險。 這可以由在其主租使用者執行安全密碼變更或重設 (的來賓使用者執行， https://aka.ms/sspr) (這需要在 home 租使用者) 進行 MFA 和 SSPR。 安全的密碼變更或重設必須在 Azure AD 上起始，而非內部部署。

* 來賓使用者的系統管理員會補救其風險。 在此情況下，系統管理員會執行密碼重設， (暫時性密碼產生) 。 這不需要身分識別保護。 來賓使用者的系統管理員可以前往 [ https://aka.ms/RiskyUsers 重設密碼]，然後按一下 [重設密碼]。

* 來賓使用者的系統管理員會關閉/關閉其風險。 同樣地，這不需要身分識別保護。 系統管理員可以前往 [ https://aka.ms/RiskyUsers 解除使用者風險] 並按一下。 不過，系統管理員必須進行審慎的處理，以確保在關閉使用者風險之前，這是誤報的負風險評估。 否則，他們會藉由隱藏未進行調查的風險評量，將其和 Microsoft 的資源視為風險。

> [!NOTE]
> 如果您有任何存取問題，請洽詢 [AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="implement-sso-by-using-the-federation-protocol"></a>使用同盟通訊協定來執行 SSO

若要在 Azure AD 應用程式庫中列出某個應用程式，您必須先實作 Azure AD 所支援的下列其中一種同盟通訊協定。 您也必須同意 Azure AD 應用程式資源庫條款及條件。 閱讀 [本網站](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)上 Azure AD 應用程式資源庫的條款及條件。

- **OpenID Connect**：若要使用 Open ID Connect 通訊協定將您的應用程式與 Azure AD 整合，請遵循 [開發人員的指示](v1-authentication-scenarios.md)。

    ![列出資源庫中的 OpenID Connect 應用程式](./media/howto-app-gallery-listing/openid.png)

    * 如果您想要使用 OpenID Connect 將應用程式新增至資源庫中的清單，請選取 **OpenID Connect & OAuth 2.0** ，如下所示。
    * 如果您有任何存取問題，請洽詢 [AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

- **Saml 2.0** 或 **ws-addressing**：如果您的應用程式支援 saml 2.0，您可以遵循 [新增自訂應用程式的指示](../manage-apps/view-applications-portal.md)，將它直接與 Azure AD 租使用者整合。

  ![在資源庫中列出 SAML 2.0 或 WS 送出的應用程式](./media/howto-app-gallery-listing/saml.png)

  * 如果您想要使用 **saml 2.0** 或 **ws-addressing**將應用程式新增至資源庫中的清單，請選取 [ **Saml 2.0/ws-送** 出]，如下所示。

  * 如果您有任何存取問題，請洽詢 [AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="implement-sso-by-using-the-password-sso"></a>使用密碼 SSO 來執行 SSO

建立一個具有可設定[密碼單一登入](../manage-apps/what-is-single-sign-on.md)之 HTML 登入頁面的 Web 應用程式。 密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 如果有數個使用者需要共用單一帳戶（例如組織的社交媒體應用程式帳戶），這也很有用。

![在資源庫中列出密碼 SSO 應用程式](./media/howto-app-gallery-listing/passwordsso.png)

* 如果您想要使用密碼 SSO 將應用程式新增至資源庫中的清單，請選取 [ **密碼 sso (使用者名稱 & 密碼) ** 如下所示。
* 如果您有任何存取問題，請洽詢 [AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="request-for-user-provisioning"></a>要求使用者布建

遵循下圖所示的程式，要求使用者布建。

   ![要求使用者布建](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>更新或移除現有清單

若要在 Azure AD 應用程式資源庫中更新或移除現有的應用程式，您必須先在 [應用程式網路入口網站](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交要求。 如果您有 Office 365 帳戶，請使用該帳戶登入此入口網站。 如果沒有，請使用您的 Microsoft 帳戶（例如 Outlook 或 Hotmail）登入。

- 選取適當的選項，如下圖所示。

    ![在資源庫中列出 SAML 應用程式](./media/howto-app-gallery-listing/updateorremove.png)

    * 若要更新現有的應用程式，請根據您的需求選取適當的選項。
    * 若要從 Azure AD 應用程式資源庫中移除現有的應用程式，請 **從資源庫選取 [移除我的應用程式清單**]。
    * 如果您有任何存取問題，請洽詢 [AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="list-requests-by-customers"></a>列出客戶的要求

客戶可以藉由選取**客戶**  >  **提交新要求**的應用程式要求，提交列出應用程式的要求。

![顯示客戶要求的應用程式圖格](./media/howto-app-gallery-listing/customer-submit-request.png)

以下是客戶要求應用程式的流程。

![顯示客戶要求的應用程式流程](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>時間表

在資源庫中列出 SAML 2.0 或 WS 送出應用程式的程式時程表是7到10個工作天。

  ![在資源庫中列出 SAML 應用程式的時程表](./media/howto-app-gallery-listing/timeline.png)

在資源庫中列出 OpenID Connect 應用程式的程式時程表是2至5個工作天。

  ![在資源庫中列出 OpenID Connect 應用程式的時程表](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>呈報

針對任何擴大，請傳送電子郵件給 [AZURE AD SSO 整合小組](mailto:SaaSApplicationIntegrations@service.microsoft.com) SaaSApplicationIntegrations@service.microsoft.com ，我們會儘快回應。