---
title: 快速入門：針對您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定單一登入 (SSO)
description: 本快速入門會逐步解說如何為您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定單一登入 (SSO)。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: f90edf525a8deb35376d6637090e7b424b2fd658
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86038985"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>快速入門：針對您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定單一登入 (SSO)

藉由為已新增至 Azure AD 租用戶的應用程式設定 SSO，來開始使用簡化的使用者登入。 設定 SSO 之後，您的使用者就能夠使用其 Azure AD 認證來登入應用程式。 SSO 會包含在免費版的 Azure AD 中。

## <a name="prerequisites"></a>必要條件

若要為已新增至 Azure AD 租用戶的應用程式設定 SSO，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 下列其中一個角色：全域管理員、雲端應用程式管理員、應用程式管理員或服務主體的擁有者。
- 支援單一登入且已預先設定並新增至 Azure AD 資源庫的應用程式。 大部分的應用程式都可以使用 Azure AD 來進行單一登入。 Azure AD 資源庫中的應用程式已預先設定。 如果您的應用程式未列出，或屬於自訂開發的應用程式，您仍然可以將其與 Azure AD 搭配使用。 請參閱目錄中的教學課程和其他文件。 本快速入門著重於已針對 SSO 預先設定，且由應用程式開發人員新增至 Azure AD 資源庫的應用程式。
- (選擇性：完成[檢視您的應用程式](view-applications-portal.md))。
- (選擇性：完成[新增應用程式](add-application-portal.md))。
- (選擇性：完成[設定應用程式](add-application-portal-configure.md))。


>[!IMPORTANT]
>若要測試本教學課程中的步驟，我們建議您使用非實際執行環境。


## <a name="enable-single-sign-on-for-an-app"></a>為應用程式啟用單一登入

當您將應用程式新增至 Azure AD 租用戶後，您會立即看到其概觀頁面。 如果您要設定已新增的應用程式，請查看第一個快速入門，其會逐步引導您檢視已新增至您租用戶的應用程式。 

為應用程式設定單一登入：

1. 在 Azure AD 入口網站中選取 [企業應用程式]，然後尋找並選取您要設定單一登入的應用程式。
2. 在 [管理] 區段中，選取 [單一登入] 來開啟屬性窗格以進行編輯。
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="顯示 Azure AD 入口網站中的單一登入設定頁面。":::
3. 選取 [SAML] 以開啟 SSO 設定頁面。 在此範例中，我們要用來設定 SSO 的應用程式是 GitHub。 設定 GitHub 之後，您的使用者將能夠使用他們在 Azure AD 租用戶中的認證來登入 GitHub。
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="顯示 GitHub 上的單一登入設定頁面。":::
4. 將應用程式設定為使用 Azure AD 進行 SAML 型 SSO 的程序，會因為應用程式不同而有所差異。 您會看到 GitHub 的指引連結。 您可以在下列位置找到其他應用程式的指南： https://docs.microsoft.com/azure/active-directory/saas-apps/
5. 請遵循指南來設定應用程式的 SSO。 許多應用程式在 SSO 功能上都有特定訂用帳戶需求。 例如，GitHub 需要企業訂用帳戶。
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="顯示 GitHub 定價頁面中企業訂用帳戶的單一登入選項。":::


## <a name="next-steps"></a>後續步驟

- [刪除應用程式](delete-application-portal.md)
