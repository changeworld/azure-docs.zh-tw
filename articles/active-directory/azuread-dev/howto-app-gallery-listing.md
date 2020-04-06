---
title: 在 Azure AD 應用程式庫中列出應用 |微軟文件
description: 了解如何列出 Azure Active Directory 應用程式庫中支援單一登入的應用程式
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: fbdae8b8506731f13ff08e877ea8eedb6d57fa78
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666932"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>在 Azure Active Directory 應用程式庫中列出您的應用程式

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

本文演示如何在 Azure 活動目錄 (Azure AD) 應用程式庫中列出應用程式、實現單一登錄 (SSO) 以及管理清單。

## <a name="what-is-the-azure-ad-application-gallery"></a>什麼是 Azure AD 應用程式庫？

- 客戶可找到最佳且適用的單一登入體驗。
- 應用程式的設定相當簡單且基本。
- 快速搜尋可在應用程式庫中找到您的應用程式。
- 免費、基本及進階 Azure AD 的客戶都可使用這項整合。
- 共同客戶可取得逐步設定教學課程。
- 使用跨域識別管理系統[(SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) 的客戶可以使用同一應用的預配。

## <a name="prerequisites"></a>Prerequisites

- 對於聯合應用程式(打開 ID 和 SAML/WS-Fed),應用程式必須支援軟體即服務 (SaaS) 模型,以便在 Azure AD 應用庫中列出。 企業庫應用程式必須支援多個客戶配置,而不是任何特定客戶。
- 對於開啟 ID 連線,應用程式必須多租戶,並且必須為應用程式正確實現[Azure AD 同意框架](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。 用戶可以將登錄請求發送到公共終結點,以便任何客戶都可以同意應用程式。 您可以根據租用戶識別碼和在權杖中收到的使用者 UPN 來控制使用者存取權。
- 對於 SAML 2.0/WS-Fed,您的應用程式必須能夠在 SP 或 IDP 模式下執行 SAML/WS-Fed SSO 整合。 在提交請求之前,請確保此功能工作正常。
- 對於密碼 SSO,請確保應用程式支援表單身份驗證,以便可以執行密碼保管,使單一登錄按預期工作。
- 需要一個永久帳戶來測試至少兩個使用者。

**如何為開發人員獲取 Azure AD?**

您可以取得免費的測試帳戶與所有先進 Azure AD 功能 - 90 天免費,並可以延長,只要你做開發工作:https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>在入口網站中提交要求

測試應用程式整合是否與 Azure AD 配合使用後,請在[Microsoft 應用程式網路門戶](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交應用程式請求。

如果在登錄后出現以下頁面,請與 Azure [AD SSO 整合團隊](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)聯繫。 提供要用於提交請求的電子郵件帳戶。 首選企業電子郵寄地址[name@yourbusiness.com](mailto:name@yourbusiness.com)。 Azure AD 團隊將在 Microsoft 應用程式網路門戶中添加帳戶。

![存取 SharePoint 門戶上的請求訊息](./media/howto-app-gallery-listing/errorimage.png)

添加帳戶後,您可以登錄到 Microsoft 應用程式網路門戶。

如果在登錄后出現以下頁面,請提供需要訪問的文本框中的業務理由。 然後選擇 **「請求訪問**」。

  ![SharePoint 門戶上的業務理由框](./media/howto-app-gallery-listing/accessrequest.png)

我們的小組會檢閱此詳細資料，並據以提供存取權給您。 請求獲得批准后,您可以通過在主頁上選擇 **「提交請求 (ISV)」** 磁貼登錄門戶並提交請求。

![在主頁上提交要求 (ISV) 磁貼](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>登入門戶時的問題

如果您在登錄時看到此錯誤,則下面是有關該問題的詳細資訊,這是修復此問題的方式。

* 如果您的登錄被阻止,如下所示:

  ![解決庫中應用程式的問題](./media/howto-app-gallery-listing/blocked.png)

**發生什麼事情:**

來賓用戶聯合到主租戶,該租戶也是 Azure AD。 來賓用戶處於高風險。 微軟不允許高風險用戶訪問其資源。 所有高風險用戶(員工或客人/供應商)必須修復/關閉其風險才能訪問 Microsoft 資源。 對於來賓使用者,此用戶風險來自主租戶,策略來自資源租戶(本例中為 Microsoft)。
 
**安全解決方案:**

* MFA 註冊來賓用戶修復自己的用戶風險。 這可以通過執行安全密碼更改或重置的來賓使用者https://aka.ms/sspr)(在其主租戶處)完成(這需要家庭租戶的 MFA 和 SSPR)。 必須在 Azure AD 上啟動安全密碼更改或重置,而不是打開前置。

* 來賓使用者讓管理員修復其風險。 在這種情況下,管理員將執行密碼重置(臨時密碼生成)。 這不需要身份保護。 來賓使用者的管理員可以轉到https://aka.ms/RiskyUsers並單擊"重置密碼」。

* 來賓使用者有他們的管理員關閉/消除他們的風險。 同樣,這不需要身份保護。 管理員可以轉到https://aka.ms/RiskyUsers並單擊"消除用戶風險」。 但是,管理員必須盡職盡責,以確保這是一個誤報風險評估,然後再關閉用戶風險。 否則,他們可以通過不進行調查就壓制風險評估,從而將自身和微軟的資源置於危險之中。

> [!NOTE]
> 如果訪問有任何問題,請與 Azure [AD SSO 整合團隊](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)聯繫。

## <a name="implement-sso-by-using-the-federation-protocol"></a>使用同盟協定使用 SSO

若要在 Azure AD 應用程式庫中列出某個應用程式，您必須先實作 Azure AD 所支援的下列其中一種同盟通訊協定。 您還需要同意 Azure AD 應用程式庫條款和條件。 閱讀[本網站](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)上 Azure AD 應用程式庫的條款和條件。

- **OpenID 連線**:要使用 Open ID 連接協定將應用程式與 Azure AD 整合,請按照[開發人員的說明](v1-authentication-scenarios.md)操作。

    ![在函式庫中列出 OpenID 連線應用程式](./media/howto-app-gallery-listing/openid.png)

    * 如果要使用 OpenID 連接將應用程式添加到庫中的清單,請選擇**OpenID 連接& OAuth 2.0,** 如圖所示。
    * 如果訪問有任何問題,請與 Azure [AD SSO 整合團隊](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)聯繫。

- **SAML 2.0**或**WS-Fed:** 如果應用支援 SAML 2.0,則可以按照[添加自訂應用程式的說明](../active-directory-saas-custom-apps.md)將其直接與 Azure AD 租戶集成。

  ![在函式庫中列出 SAML 2.0 或 WS-Fed 應用程式](./media/howto-app-gallery-listing/saml.png)

  * 如果要使用**SAML 2.0**或**WS-Fed**將應用程式添加到庫中清單,請選擇**SAML 2.0/WS-Fed,** 如下所示。

  * 如果訪問有任何問題,請與 Azure [AD SSO 整合團隊](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)聯繫。

## <a name="implement-sso-by-using-the-password-sso"></a>使用密碼 SSO 實作 SSO

建立一個具有可設定[密碼單一登入](../manage-apps/what-is-single-sign-on.md)之 HTML 登入頁面的 Web 應用程式。 密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 對於多個使用者需要共用單個帳戶(例如,到組織的社交媒體應用帳戶)的方案,它非常有用。

![在庫中列出密碼 SSO 應用程式](./media/howto-app-gallery-listing/passwordsso.png)

* 如果要使用密碼 SSO 將應用程式添加到庫中的清單,請選擇 **「密碼 SSO」,** 如下所示。
* 如果訪問有任何問題,請與 Azure [AD SSO 整合團隊](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)聯繫。

## <a name="request-for-user-provisioning"></a>使用者預先要求

按照下圖所示的過程請求使用者預配。

   ![使用者預先要求](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>更新或移除現有清單

要更新或刪除 Azure AD 應用庫中的現有應用程式,首先需要在[應用程式網路門戶](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交請求。 如果您有 Office 365 帳戶，請使用該帳戶登入此入口網站。 否則,請使用 Microsoft 帳戶(如 Outlook 或 Hotmail)登錄。

- 選擇如下圖像所示的相應選項。

    ![在函式庫中列出 SAML 應用程式](./media/howto-app-gallery-listing/updateorremove.png)

    * 要更新現有應用程式,請根據您的要求選擇適當的選項。
    * 要從 Azure AD 的函式庫中移除現有應用程式,請選擇**從庫中移除我的應用程式清單**。
    * 如果訪問有任何問題,請與 Azure [AD SSO 整合團隊](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)聯繫。

## <a name="list-requests-by-customers"></a>列出客戶的要求

客戶可以通過選擇 > 客戶 提交**新請求****的應用請求提交**申請以列出申請。

![顯示客戶要求的應用程式磁貼](./media/howto-app-gallery-listing/customer-submit-request.png)

以下是客戶請求的應用程式流。

![顯示客戶要求的應用程式](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>時間表

在庫中列出 SAML 2.0 或 WS-Fed 應用程式的過程時程表為 7 到 10 個工作日。

  ![在函式庫中列出 SAML 應用程式的時程表](./media/howto-app-gallery-listing/timeline.png)

在庫中列出 OpenID Connect 應用程式的過程的時間線是 2 到 5 個工作日。

  ![在函式庫中列出 OpenID 連線應用程式的時程表](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>呈報

對於任何升級,請發送電子郵件至 Azure SaaSApplicationIntegrations@service.microsoft.com AD [SSO 整合團隊](mailto:SaaSApplicationIntegrations@service.microsoft.com),我們將儘快做出回應。