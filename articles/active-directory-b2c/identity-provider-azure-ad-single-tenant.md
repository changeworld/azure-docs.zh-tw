---
title: 設定 Azure AD 組織的登入
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: b0ff1c2d913d0a4402b491f3c84ce0d35cd081df
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847585"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入

若要在 Azure AD B2C 中使用 Azure Active Directory (Azure AD) 做為[識別提供者](authorization-code-flow.md)，您需要建立代表該識別提供者的應用程式。 本文說明如何讓特定 Azure AD 組織中的使用者能夠使用 Azure AD B2C 中的使用者流程登入。

## <a name="create-an-azure-ad-app"></a>建立 Azure AD 應用程式

若要讓特定 Azure AD 組織的使用者登入，您需要在組織 Azure AD 租用戶內註冊應用程式，此租用戶與您的 Azure AD B2C 租用戶不同。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 請確定您使用的是包含您 Azure AD 租使用者的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD 租使用者的目錄。 這與您的 Azure AD B2C 租使用者不是相同的租使用者。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
4. 選取 [新增註冊]。
5. 輸入應用程式的名稱。 例如： `Azure AD B2C App` 。
6. 只接受此應用程式**在此組織目錄中**選取的帳戶。
7. 針對 [重新**導向 URI**]，接受 [ **Web**] 的值，並以所有小寫字母輸入下列 URL，其中 `your-B2C-tenant-name` 會取代為您 Azure AD B2C 的租使用者名稱。 例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`：

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。

8. 按一下 [註冊]。 複製**應用程式（用戶端）識別碼**，以供稍後使用。
9. 在 [應用程式] 功能表中選取 [**憑證 & 秘密**]，然後選取 [**新增用戶端密碼**]。
10. 輸入用戶端密碼的 [名稱]。 例如： `Azure AD B2C App Secret` 。
11. 選取 [到期時間]。 針對此應用程式，接受**1 年**的選取專案。
12. 選取 **[新增]** 並複製所顯示的新用戶端密碼值，以供稍後使用。

## <a name="configure-azure-ad-as-an-identity-provider"></a>將 Azure AD 設定成識別提供者

1. 請確定您使用的是包含 Azure AD B2C 租使用者的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [**識別提供者**]，然後選取 **[新增 OpenID Connect 提供者]** 。
1. 輸入 [名稱]。 例如，輸入 *Contoso Azure AD*。
1. 在 [**中繼資料 url**] 中，輸入下列 url，將 `your-AD-tenant-domain` 取代為 Azure AD 租使用者的功能變數名稱：

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    例如： `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration` 。

    **請不要使用 Azure AD** v2.0 中繼資料端點，例如 `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`。 這麼做會導致在嘗試登入時發生類似 `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` 的錯誤。

1. 針對 [**用戶端識別碼**]，輸入您先前記錄的應用程式識別碼。
1. 針對 [**用戶端密碼**]，輸入您先前記錄的用戶端密碼。
1. 保留 [**範圍**]、[**回應類型**] 和 [**回應模式]** 的預設值。
1. 選擇性輸入**Domain_hint**的值。 例如， *ContosoAD*。 這是在要求中使用 *domain_hint* 參考此識別提供者時，要使用的值。
1. 在 [**識別提供者宣告對應**] 底下，輸入下列宣告對應值：

    * **使用者識別碼**： *oid*
    * **顯示名稱**：*名稱*
    * **名字**： *given_name*
    * **姓氏**： *family_name*
    * **電子郵件**： *unique_name*

1. 選取 [儲存]。
