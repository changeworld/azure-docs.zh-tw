---
title: 新增不在資源庫中的應用程式-Microsoft 身分識別平臺 |Microsoft Docs
description: 將非資源庫應用程式新增至您的 Azure AD 租使用者。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063606"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>將未列出的（非資源庫）應用程式新增至您的 Azure AD 組織

除了[Azure AD 應用程式庫](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)中的選擇以外，您還可以選擇新增不在資源**庫內的應用程式**。 您可以新增組織中現有的任何應用程式，或來自不屬於 Azure AD 資源庫之廠商的任何協力廠商應用程式。 根據您的[授權合約](https://azure.microsoft.com/pricing/details/active-directory/)而定，可以使用下列功能：

- 支援[安全性聲明標記語言（SAML） 2.0](https://wikipedia.org/wiki/SAML_2.0)身分識別提供者（由 SP 起始或 IdP 起始）之任何應用程式的自助服務整合
- Web 應用程式可在使用 [密碼型 SSO](what-is-single-sign-on.md#password-based-sso)
- 使用[系統進行跨網域身分識別管理（SCIM）通訊協定以進行使用者](../app-provisioning/use-scim-to-provision-users-and-groups.md)布建之應用程式的自助連線
- 能夠在 [Office 365 應用程式啟動器](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/)或 [Azure AD 存取面板](what-is-single-sign-on.md#linked-sign-on)中新增任何應用程式的連結

本文說明如何將不在資源庫內的應用程式新增至 Azure 入口網站中的**企業應用程式**，而不需撰寫程式碼。 如果您要尋找如何將自訂應用程式與 Azure AD 整合的開發人員指引，請參閱[Azure AD 的驗證案例](../develop/authentication-scenarios.md)。 當您開發使用新通訊協定（例如[OpenId connect/OAuth](../develop/active-directory-v2-protocols.md) ）來驗證使用者的應用程式時，您可以使用 Azure 入口網站中的[應用程式註冊](../develop/quickstart-register-app.md)體驗，向 Microsoft 身分識別平臺註冊它。

## <a name="add-a-non-gallery-application"></a>新增不在資源庫內的應用程式

1. 使用您的 Microsoft 身分識別平臺系統管理員帳戶登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。

2. 選取 [**企業應用程式**] > [**新增應用程式**]。

3. （選擇性，但建議使用）在 [**流覽 Azure AD 資源庫]** 搜尋方塊中，輸入應用程式的顯示名稱。 

4. 選取 [**建立您自己的應用程式**]。 [**建立您自己的應用程式**] 頁面隨即出現。

   ![新增應用程式](media/add-non-gallery-app/create-your-own-application.png)

5. 開始輸入新應用程式的顯示名稱。 如果有任何資源庫應用程式具有類似的名稱，它們就會出現在搜尋結果清單中。

   > [!NOTE]
   > 我們建議您盡可能使用應用程式的圖庫版本。 如果您想要新增的應用程式出現在搜尋結果中，請選取該應用程式，並略過此程式的其餘部分。

6. 在**您想要對應用程式做什麼事？** 選擇 **[整合您在資源庫中找不到的任何其他應用程式**]。 此選項通常用於 SAML 和 WS-送出應用程式。

   > [!NOTE]
   > 在下列案例中，會使用其他兩個選項：
   >* **設定應用程式 proxy 以進行內部部署應用程式的安全遠端存取**，會開啟 Azure AD 應用程式 Proxy 和連接器的設定頁面。
   >* **註冊您正在處理的應用程式，以整合 Azure AD**開啟 [**應用程式註冊**] 頁面。 此選項通常用於 OpenID Connect 應用程式。

7. 選取 [建立]。 應用程式的 [概觀] 頁面隨即開啟。

## <a name="configure-user-sign-in-properties"></a>設定使用者登入屬性

1. 選取 [屬性]，以開啟屬性窗格來進行編輯。

    ![編輯屬性窗格](media/add-non-gallery-app/edit-properties.png)

2. 設定下列選項，以決定已指派或未指派給應用程式的使用者如何登入應用程式，以及使用者是否可以在存取面板中看到該應用程式。

    - [為使用者啟用登入] 可決定指派給應用程式的使用者是否可以登入。
    - [需要使用者指派] 可決定未指派給應用程式的使用者是否可以登入。
    - [可讓使用者看見] 可決定指派給應用程式的使用者是否可以在存取面板和 O365 啟動器中看見應用程式。

      **已指派**的使用者行為：

       | 應用程式屬性設定 | | | 已指派的使用者體驗 | |
       |---|---|---|---|---|
       | 為使用者啟用登入？ | 需要使用者指派？ | 可讓使用者看見？ | 已指派的使用者可以登入？ | 已指派的使用者可以看見應用程式？* |
       | 是 | 是 | 是 | 是 | 是  |
       | 是 | 是 | 否  | 是 | 否   |
       | 是 | 否  | 是 | 是 | 是  |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

      **未指派**的使用者行為：

       | 應用程式屬性設定 | | | 未指派的使用者體驗 | |
       |---|---|---|---|---|
       | 為使用者啟用登入？ | 需要使用者指派？ | 可讓使用者看見？ | 未指派的使用者可以登入？ | 未指派的使用者可以看見應用程式？* |
       | 是 | 是 | 是 | 否  | 否   |
       | 是 | 是 | 否  | 否  | 否   |
       | 是 | 否  | 是 | 是 | 否   |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

     *使用者是否可以在存取面板和 Office 365 應用程式啟動器中看到應用程式？

3. 若要使用自訂標誌，請建立 215 x 215 像素的標誌，並將它儲存為 PNG 格式。 然後瀏覽至您的標誌並加以上傳。

    ![變更標誌](media/add-non-gallery-app/change-logo.png)

4. 完成之後，選取 [儲存]。

## <a name="next-steps"></a>後續步驟

現在您已將應用程式新增至您的 Azure AD 組織，[請選擇您想使用的單一登入方法](what-is-single-sign-on.md#choosing-a-single-sign-on-method)並參閱下列適當的文章：

- [設定 SAML 型單一登入](configure-single-sign-on-non-gallery-applications.md)
- [設定密碼單一登入](configure-password-single-sign-on-non-gallery-applications.md)
- [設定連結的登入](configure-linked-sign-on.md)
