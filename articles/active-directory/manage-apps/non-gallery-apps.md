---
title: 針對未列在應用程式庫中的應用程式使用 Azure AD
description: 瞭解如何整合未列在 Azure AD 資源庫中的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 3483701d34b8fef5b40a411e22b33a73db2de44b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658735"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>針對未列在應用程式庫中的應用程式使用 Azure AD

在 [新增應用程式](add-application-portal.md) 快速入門中，您將瞭解如何將應用程式新增至您的 Azure AD 租使用者。

除了 [Azure AD 應用程式庫](../saas-apps/tutorial-list.md)中的選項之外，您還可以選擇新增 **非資源庫應用程式**。 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>未列在 Azure AD 資源庫中的應用程式功能

您可以新增任何已存在於組織中的應用程式，或新增來自尚未屬於 Azure AD 資源庫的供應商的任何第三方應用程式。 根據您的[授權合約](https://azure.microsoft.com/pricing/details/active-directory/)，可以使用下列功能：

- 任何支援[安全性聲明標記語言 (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) 身分識別提供者的應用程式，皆可進行自助式整合 (SP 起始或 IdP 起始)
- 使用 [密碼型 SSO](sso-options.md#password-based-sso) 對具有 HTML 登入頁面的任何 Web 應用程式進行自助服務整合
- 應用程式可使用[跨網域身分識別管理系統 (SCIM) 通訊協定進行自助式連線，以執行使用者佈建](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- 能夠將連結新增至[Office 365 應用程式啟動器](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/)或[我的應用程式](sso-options.md#linked-sign-on)中的任何應用程式

如果您要尋找有關如何整合自訂應用程式與 Azure AD 的開發人員指引，請參閱 [Azure AD 的驗證案例](../develop/authentication-vs-authorization.md)。 當您開發一個使用新式通訊協定 (例如 [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md)) 的應用程式來驗證使用者時，可以使用 Azure 入口網站中的[應用程式註冊](../develop/quickstart-register-app.md)體驗，向 Microsoft 身分識別平台進行註冊。

## <a name="next-steps"></a>後續步驟

- [應用程式管理的快速入門系列](view-applications-portal.md)