---
title: 添加非庫應用程式 - 微軟身份平臺 |微軟文檔
description: 將非庫應用程式添加到 Azure AD 租戶。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063606"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>將未列出的（非庫）應用程式添加到 Azure AD 組織

除了[Azure AD 應用程式庫中](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)的選項之外，您還可以選擇添加**非庫應用程式**。 可以添加組織中已存在的任何應用程式，或者從尚未加入 Azure AD 庫的供應商處添加任何協力廠商應用程式。 根據您的[授權合約](https://azure.microsoft.com/pricing/details/active-directory/)，以下功能可用：

- 支援[安全斷言標記語言 （SAML） 2.0](https://wikipedia.org/wiki/SAML_2.0)標識提供程式（SP 啟動或 IdP 啟動）的任何應用程式的自助服務集成
- Web 應用程式可在使用 [密碼型 SSO](what-is-single-sign-on.md#password-based-sso)
- 使用[跨域身份管理系統 （SCIM） 協定進行使用者預配](../app-provisioning/use-scim-to-provision-users-and-groups.md)的應用程式的自助服務連接
- 能夠在 [Office 365 應用程式啟動器](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/)或 [Azure AD 存取面板](what-is-single-sign-on.md#linked-sign-on)中新增任何應用程式的連結

本文介紹如何在不編寫代碼的情況下將非庫應用程式添加到 Azure 門戶中的**企業應用程式**。 相反，如果您正在尋找有關如何將自訂應用與 Azure AD 集成的開發人員指南，請參閱[Azure AD 的身份驗證方案](../develop/authentication-scenarios.md)。 開發使用[OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md)等現代協定對使用者進行身份驗證的應用時，可以使用 Azure 門戶中的[應用註冊](../develop/quickstart-register-app.md)體驗將其註冊到 Microsoft 標識平臺。

## <a name="add-a-non-gallery-application"></a>新增不在資源庫內的應用程式

1. 使用 Microsoft 標識平臺管理員帳戶登錄到[Azure 活動目錄門戶](https://aad.portal.azure.com/)。

2. 選擇**企業應用程式** > **新應用程式**。

3. （可選但推薦）在 **"流覽 Azure AD 庫**"搜索框中，輸入應用程式的顯示名稱。 

4. 選擇 **"創建自己的應用程式**"。 將顯示"**創建您自己的應用程式**"頁。

   ![新增應用程式](media/add-non-gallery-app/create-your-own-application.png)

5. 開始鍵入新應用程式的顯示名稱。 如果有任何具有類似名稱的庫應用程式，它們將顯示在搜尋結果清單中。

   > [!NOTE]
   > 我們建議盡可能使用應用程式的庫版本。 如果要添加的應用程式將顯示在搜尋結果中，請選擇該應用程式並跳過此過程的其餘部分。

6. 在"**您希望如何處理您的應用程式**"下？ 選擇 **"集成任何其他您在庫中找不到的應用程式**"。 此選項通常用於 SAML 和 WS-Fed 應用程式。

   > [!NOTE]
   > 其他兩個選項用於以下方案：
   >* **配置應用程式代理以安全遠端存取本地應用程式**將打開 Azure AD 應用程式代理和連接器的配置頁。
   >* **註冊正在使用與 Azure AD 集成的應用程式**將打開**應用註冊**頁。 此選項通常用於 OpenID 連接應用程式。

7. 選取 [建立]****。 應用程式的 [概觀]**** 頁面隨即開啟。

## <a name="configure-user-sign-in-properties"></a>設定使用者登入屬性

1. 選取 [屬性]****，以開啟屬性窗格來進行編輯。

    ![編輯屬性窗格](media/add-non-gallery-app/edit-properties.png)

2. 設定下列選項，以決定已指派或未指派給應用程式的使用者如何登入應用程式，以及使用者是否可以在存取面板中看到該應用程式。

    - **啟用使用者登錄**可確定分配給應用程式的使用者可以登錄。
    - **所需的使用者分配**確定未分配給應用程式的使用者可以登錄。
    - [可讓使用者看見]**** 可決定指派給應用程式的使用者是否可以在存取面板和 O365 啟動器中看見應用程式。

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

4. 完成之後，選取 [儲存]****。

## <a name="next-steps"></a>後續步驟

現在您已將應用程式新增至您的 Azure AD 組織，[請選擇您想使用的單一登入方法](what-is-single-sign-on.md#choosing-a-single-sign-on-method)並參閱下列適當的文章：

- [設定 SAML 型單一登入](configure-single-sign-on-non-gallery-applications.md)
- [設定密碼單一登入](configure-password-single-sign-on-non-gallery-applications.md)
- [設定連結的登入](configure-linked-sign-on.md)
