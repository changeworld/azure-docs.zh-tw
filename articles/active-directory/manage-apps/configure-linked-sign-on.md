---
title: 在 Azure Active Directory 中設定連結的登入
description: 在 Azure AD 中設定連結的登入。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 2269a8f7f58d35fee5e2ca30a77af5e8cba83678
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87459329"
---
# <a name="configure-linked-sign-on"></a>設定連結的登入

在應用程式管理的[快速入門系列](view-applications-portal.md)中，您已瞭解如何使用 Azure AD 做為應用程式的身分識別提供者（IdP）。 在快速入門手冊中，您會設定 SAML 型 SSO。 另一個選項是 [已**連結**]。 這篇文章會更詳細地說明已連結的選項。

當使用者在組織的[我的應用程式](https://myapplications.microsoft.com/)或 Office 365 入口網站中選取應用程式時，[**連結**] 選項可讓您設定目標位置。

連結選項非常重要的一些常見案例包括：
- 將連結新增至目前使用同盟的自訂 web 應用程式，例如 Active Directory 同盟服務（AD FS）。
- 將深層連結新增到您只想要顯示在使用者存取面板上的特定 SharePoint 頁面或其他網頁。
- 將連結新增至不需要驗證的應用程式。 
 
 **連結**的選項不會透過 Azure AD 認證提供登入功能。 但是，您仍然可以使用**企業應用程式**的一些其他功能。 例如，您可以使用 audit 記錄，並新增自訂標誌和應用程式名稱。

## <a name="before-you-begin"></a>開始之前

若要快速增加，請逐步執行應用程式管理的[快速入門系列](view-applications-portal.md)。 在您設定單一登入的快速入門中，您也會找到 [**連結**] 選項。 

[已**連結**] 選項不會透過 Azure AD 提供登入功能。 選項只會設定使用者在[我的應用程式](https://myapplications.microsoft.com/)或 Microsoft 365 應用程式啟動器上選取應用程式時，將傳送至的位置。

> [!IMPORTANT] 
> 在某些情況下，[**單一登入**] 選項不會出現在**企業應用**程式中的應用程式導覽中。 
>
> 如果應用程式是使用**應用程式註冊**註冊，則單一登入功能會設定為預設使用 OIDC OAuth。 在此情況下，[**企業應用程式**] 下的流覽中不會顯示 [**單一登入**] 選項。 當您使用**應用程式註冊**新增自訂應用程式時，您可以在資訊清單檔中設定選項。 若要深入瞭解資訊清單檔案，請參閱[Azure Active Directory 應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)。 若要深入瞭解 SSO 標準，請參閱[使用 Microsoft 身分識別平臺的驗證和授權](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform)。 
>
> 當應用程式裝載于另一個租使用者時，或您的帳戶沒有必要許可權（全域管理員、雲端應用程式系統管理員、應用程式系統管理員或服務主體的擁有者）時，流覽中將遺失**單一登入**的其他案例。 許可權也可能會導致您可以開啟**單一登入**但無法儲存的案例。 若要深入瞭解 Azure AD 系統管理角色，請參閱（ https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 。

### <a name="configure-link"></a>設定連結

若要設定應用程式的連結，請選取 [**單一登入**] 頁面上的 [**連結**]。 然後輸入連結並選取 [**儲存**]。 需要提醒您哪裡可以找到這些選項？ 查看[快速入門系列](view-applications-portal.md)。
 
在您設定應用程式之後，請將使用者和群組指派給它。 當您指派使用者時，您可以控制應用程式在[我的應用程式](https://myapplications.microsoft.com/)或 Microsoft 365 應用程式啟動器上出現的時間。

## <a name="next-steps"></a>後續步驟

- [將使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)
- [設定使用者帳戶自動佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
