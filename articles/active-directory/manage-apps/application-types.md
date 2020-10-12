---
title: 使用您的 Azure Active Directory 租使用者來查看身分識別管理的應用程式
description: 瞭解如何使用您的 Azure Active Directory 租使用者來查看身分識別管理的所有應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 5229b123986149903d562bc0b3d6902c0136f647
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707878"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>使用您的 Azure AD 租使用者來查看身分識別管理的應用程式
[應用程式管理的快速入門系列](view-applications-portal.md)將帶領您瞭解基本概念。 在其中，您將瞭解如何使用您的 Azure AD 租使用者來查看身分識別管理的所有應用程式。 本文將更深入地探討您將找到的應用程式類型。

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>特定應用程式為何出現在我的所有應用程式清單中？
篩選為 [所有應用程式]**** 時，[所有應用程式清單]**** **** 會顯示租用戶中的每個服務主體物件。 服務主體物件可能以各種方式出現在此清單中︰
- 當您從應用程式庫新增任何應用程式時，包括︰
   - **Azure AD-企業應用程式** –使用 Azure AD 入口網站上的 [ **企業應用程式** ] 選項新增至您租使用者的應用程式。 通常會使用 SAML 標準來整合應用程式。
   - **Azure AD-應用程式註冊** –使用 Azure AD 入口網站上的 **應用程式註冊** 選項，將應用程式新增至您的租使用者。 通常會使用 Open ID Connect 和 OAuth 標準來自訂開發的應用程式。
   - **應用程式 Proxy 應用程式** – 執行於您的內部部署環境中，而您想要讓使用者能夠安全地從外部對其進行單一登入的應用程式
- 註冊或登入時，與 Azure Active Directory 整合的協力廠商應用程式。 其中一個範例是 [Smartsheet](https://app.smartsheet.com/b/home) 或 [DocuSign](https://www.docusign.net/member/MemberLogin.aspx)。
- Microsoft 應用程式（例如 Microsoft 365）。
- 當您使用[應用程式登錄](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)建立自訂開發的應用程式來新增應用程式註冊時
- 當您使用 [V2.0 應用程式註冊入口網站](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)建立自訂開發的應用程式來新增應用程式註冊時
- 當您新增應用程式時，您正在使用 Visual Studio 的 [ASP.NET Authentication 方法](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) 進行開發，或 [已連線的服務](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)
- 當您使用 [Azure AD PowerShell 模組](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)建立服務主體物件時
- 當您以系統管理員身分[同意應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)使用您租用戶中的資料時
- 當[使用者同意應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)使用您租用戶中的資料時
- 當您啟用的某些服務會在您的租用戶中儲存資料時。 其中一個範例是「密碼重設」，它會模擬成服務主體，安全地儲存您的密碼重設原則。

深入瞭解如何將應用程式新增至您的目錄，以及如何 [將應用程式新增至 Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)。

## <a name="next-steps"></a>後續步驟
[使用 Azure Active Directory 來管理應用程式](what-is-application-management.md)
