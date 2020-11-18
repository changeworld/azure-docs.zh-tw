---
title: 快速入門：為您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定 SAML 型單一登入 (SSO)
description: 本快速入門會逐步解說如何為您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定 SAML 型單一登入 (SSO)。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 235c5c19c9a6e587106b3fd41691a3f775caa84f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656610"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>快速入門：為您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定 SAML 型單一登入 (SSO)

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

在您將應用程式新增至 Azure AD 租用戶之後，概觀頁面就會出現。 如果您要設定已新增的應用程式，請查看第一個快速入門。 其中會逐步引導您檢視已新增至您租用戶的應用程式。 

為應用程式設定單一登入：

1. 在 Azure AD 入口網站中，選取 [企業應用程式]。 然後尋找並選取您要為其設定單一登入的應用程式。
1. 在 [管理] 區段中，選取 [單一登入] 來開啟 [單一登入] 窗格以進行編輯。

    > [!IMPORTANT]
    > 如果應用程式將 OpenID Connect (OIDC) 標準用於 SSO，您就不會在導覽區中看到單一登入選項。 請參閱 OIDC 型 SSO 的快速入門，以了解如何進行其設定。

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="顯示 Azure AD 入口網站中單一登入設定頁面的螢幕擷取畫面。":::

1. 選取 [SAML] 以開啟 SSO 設定頁面。 在此範例中，我們要用來設定 SSO 的應用程式是 GitHub。 設定 GitHub 之後，您的使用者將能夠使用他們在 Azure AD 租用戶中的認證來登入 GitHub。

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="顯示 GitHub 上單一登入設定頁面的螢幕擷取畫面。":::

1. 將應用程式設定為使用 Azure AD 進行 SAML 型 SSO 的程序，會因為應用程式不同而有所差異。 您會看到 GitHub 的指引連結。 若要尋找其他應用程式的指南，請參閱[整合 SaaS 應用程式與 Azure Active Directory 的教學課程](/azure/active-directory/saas-apps/)。
1. 請遵循指南來設定應用程式的 SSO。 許多應用程式在 SSO 功能上都有特定訂用帳戶需求。 例如，GitHub 需要企業訂用帳戶。
    > [!TIP]
    > 若要深入了解 SAML 設定選項，請參閱[設定 SAML 型單一登入](configure-saml-single-sign-on.md)。

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="顯示 GitHub 定價頁面中企業訂用帳戶單一登入選項的螢幕擷取畫面。":::

> [!TIP]
> 您可以使用圖形 API 自動管理應用程式，請參閱[使用 Microsoft Graph API 自動管理應用程式](/graph/application-saml-sso-configure-api)。


## <a name="clean-up-resources"></a>清除資源

當您完成本快速入門系列時，請考慮刪除應用程式以清除您的測試租用戶。 本系列的最後一個快速入門涵蓋了刪除應用程式的說明，請參閱[刪除應用程式](delete-application-portal.md)。

## <a name="next-steps"></a>後續步驟

前往下一篇文章，了解如何刪除應用程式。
> [!div class="nextstepaction"]
> [刪除應用程式](delete-application-portal.md)