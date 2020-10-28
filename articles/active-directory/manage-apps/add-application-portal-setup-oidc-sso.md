---
title: 快速入門：為您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定 OIDC 型單一登入 (SSO)
description: 本快速入門會逐步解說如何為您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定 OIDC 型單一登入 (SSO)。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 0a25d910b694968802e853a81c16e34dd0539f7a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203495"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>快速入門：為您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定 OIDC 型單一登入 (SSO)

藉由為已新增至 Azure Active Directory (Azure AD) 租用戶的應用程式設定單一登入 (SSO)，來開始使用簡化的使用者登入。 設定 SSO 之後，您的使用者就可以使用其 Azure AD 認證來登入應用程式。 SSO 會包含在免費版的 Azure AD 中。

## <a name="prerequisites"></a>必要條件

若要為已新增至 Azure AD 租用戶的應用程式設定 SSO，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 下列其中一個角色：全域管理員、雲端應用程式管理員、應用程式管理員或服務主體的擁有者。
- 支援 SSO 且已預先設定並新增至 Azure AD 資源庫的應用程式。 大部分的應用程式都可以使用 Azure AD 來進行 SSO。 Azure AD 資源庫中的應用程式已預先設定。 如果您的應用程式未列出，或屬於自訂開發的應用程式，您仍然可以將其與 Azure AD 搭配使用。 請參閱目錄中的教學課程和其他文件。 本快速入門著重於已針對 SSO 預先設定，且由應用程式開發人員新增至 Azure AD 資源庫的應用程式。
- 選擇性：完成[檢視您的應用程式](view-applications-portal.md)。
- 選擇性：完成[新增應用程式](add-application-portal.md)。
- 選擇性：完成[設定應用程式](add-application-portal-configure.md)。
- 選擇性：完成[將使用者指派至應用程式](add-application-portal-assign-users.md)。

>[!IMPORTANT]
>請使用非商業執行環境來測試本快速入門中的步驟。

## <a name="enable-single-sign-on-for-an-app"></a>為應用程式啟用單一登入

當您新增使用 OIDC 標準進行 SSO 的應用程式時，您會看到設定按鈕。 選取該按鈕時，您會移至應用程式站台，並完成應用程式的註冊程序。 此系列先前的「新增應用程式」快速入門中，提供了新增應用程式的程序。 如果您要設定已新增的應用程式，請查看第一個快速入門。 其中會逐步引導您檢視已在您的租用戶中的應用程式。 

為應用程式設定單一登入：

1. 在此系列先前的快速入門中，您已了解如何新增將使用您的 Azure AD 租用戶進行身分識別管理的應用程式。 如果應用程式開發人員使用 OIDC 標準來實作 SSO，則在新增應用程式時，您將會看到註冊按鈕。 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="此螢幕擷取畫面顯示單一登入選項和註冊按鈕。" lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. 選取 [註冊] 後，您會進入應用程式開發人員登入頁面。 請使用 Azure Active Directory 登入認證進行登入。 

   > [!IMPORTANT]
    > 如果您已有應用程式的訂用帳戶，則會驗證使用者詳細資料和租用戶/目錄資訊。 如果應用程式無法驗證使用者，則會將您重新導向至註冊應用程式服務的頁面或錯誤頁面。

3. 驗證成功後，將會出現一個要求管理員同意的對話方塊。 選取 [代表您的組織同意]，然後選取 [接受]。 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="此螢幕擷取畫面顯示單一登入選項和註冊按鈕。" lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. 應用程式會新增至您的租用戶，並顯示應用程式首頁。


> [!TIP]
> 您可以使用圖形 API 自動管理應用程式，請參閱[使用 Microsoft Graph API 自動管理應用程式](https://docs.microsoft.com/graph/application-saml-sso-configure-api)。


## <a name="clean-up-resources"></a>清除資源

當您完成本快速入門系列時，請考慮刪除應用程式以清除您的測試租用戶。 本系列的最後一個快速入門涵蓋了刪除應用程式的說明，請參閱[刪除應用程式](delete-application-portal.md)。

## <a name="next-steps"></a>後續步驟

前往下一篇文章，了解如何刪除應用程式。
> [!div class="nextstepaction"]
> [刪除應用程式](delete-application-portal.md)
