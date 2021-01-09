---
title: 快速入門：從您的 Azure Active Directory (Azure AD) 租用戶中刪除應用程式
description: 本快速入門將使用 Azure 入口網站來刪除 Azure Active Directory (Azure AD) 租用戶中的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 12/28/2020
ms.author: kenwith
ms.openlocfilehash: e2b8bd47f7d124562a38413d36b849d1f7efacee
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802757"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>快速入門：從您的 Azure Active Directory (Azure AD) 租用戶中刪除應用程式

本快速入門將使用 Azure 入口網站來刪除已新增至 Azure Active Directory (Azure AD) 租用戶的應用程式。

## <a name="prerequisites"></a>必要條件

若要從您的 Azure AD 租用戶中刪除應用程式，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 下列其中一個角色：全域管理員、雲端應用程式管理員、應用程式管理員或服務主體的擁有者。
- 選擇性：完成[檢視您的應用程式](view-applications-portal.md)。
- 選擇性：完成[新增應用程式](add-application-portal.md)。
- 選擇性：完成[設定應用程式](add-application-portal-configure.md)。
- 選擇性：完成[將使用者指派至應用程式](add-application-portal-assign-users.md)。
- 選擇性：完成[設定單一登入](add-application-portal-setup-sso.md)。

>[!IMPORTANT]
>請使用非商業執行環境來測試本快速入門中的步驟。

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>從 Azure AD 租用戶中刪除應用程式

若要從您的 Azure AD 租用戶中刪除應用程式：

1. 在 Azure AD 入口網站中，選取 [企業應用程式]。 尋找並選取您要刪除的應用程式。 在此案例中，我們已刪除上一個快速入門中新增的 **GitHub_test** 應用程式。
1. 在左側窗格的 [管理] 區段中，選取 [屬性]。
1. 選取 [刪除]，然後選取 [是]，確認您要從 Azure AD 租用戶中刪除應用程式。

> [!TIP]
> 您可以使用圖形 API 自動管理應用程式，請參閱[使用 Microsoft Graph API 自動管理應用程式](/graph/application-saml-sso-configure-api)。

## <a name="clean-up-resources"></a>清除資源

當您完成本快速入門系列時，請考慮刪除應用程式以清除您的測試租用戶。 本快速入門中涵蓋了刪除應用程式的資訊。

## <a name="next-steps"></a>後續步驟

您已經完成快速入門系列！ 接著，了解單一登入 (SSO)，請參閱[什麼是 SSO？](what-is-single-sign-on.md) 或者，請閱讀應用程式管理的最佳做法。
> [!div class="nextstepaction"]
> [應用程式管理的最佳做法](application-management-fundamentals.md)
