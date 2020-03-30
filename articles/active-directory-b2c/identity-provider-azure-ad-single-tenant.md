---
title: 為 Azure AD 組織設置登錄
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 35fc4e1d64fa7df392fa878db14c0464da7dccf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188302"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入

若要在 Azure AD B2C 中使用 Azure Active Directory (Azure AD) 做為[識別提供者](authorization-code-flow.md)，您需要建立代表該識別提供者的應用程式。 本文說明如何讓特定 Azure AD 組織中的使用者能夠使用 Azure AD B2C 中的使用者流程登入。

## <a name="create-an-azure-ad-app"></a>建立 Azure AD 應用程式

若要讓特定 Azure AD 組織的使用者登入，您需要在組織 Azure AD 租用戶內註冊應用程式，此租用戶與您的 Azure AD B2C 租用戶不同。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 請確保使用的是包含 Azure AD 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器，然後選擇包含 Azure AD 租戶的目錄。 這與 Azure AD B2C 租戶不同。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
4. 選取 [新增註冊]****。
5. 輸入應用程式的名稱。 例如： `Azure AD B2C App` 。
6. **僅接受此組織目錄中針對此應用程式的帳戶**選擇。
7. 對於**重定向 URI**，接受**Web**的值，並在所有小寫字母中輸入以下 URL，其中`your-B2C-tenant-name`替換為 Azure AD B2C 租戶的名稱。 例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`：

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。

8. 按一下 [註冊]**** 複製**應用程式（用戶端）ID**以以後使用。
9. 在應用程式功能表中選擇**證書&機密**，然後選擇 **"新用戶端機密**"。
10. 輸入用戶端機密的名稱。 例如： `Azure AD B2C App Secret` 。
11. 選擇過期期限。 對於此申請，接受 1**年中的**選擇。
12. 選擇 **"添加**並複製顯示後使用的新用戶端金鑰的值"。

## <a name="configure-azure-ad-as-an-identity-provider"></a>將 Azure AD 設定成識別提供者

1. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器，然後選擇包含 Azure AD B2C 租戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 選擇**標識提供程式**，然後選擇 **"新建 OpenID 連接提供程式**"。
1. 輸入**名稱**。 例如，輸入 *Contoso Azure AD*。
1. 對於**中繼資料 URL，** 輸入以下 URL`your-AD-tenant-domain`替換為 Azure AD 租戶的功能變數名稱：

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    例如： `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration` 。

    **不要**使用 Azure AD v2.0 中繼資料終結點，`https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`例如 。 這樣做會導致類似于嘗試登錄`AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'`時的錯誤。

1. 對於**用戶端 ID，** 輸入您以前記錄的應用程式 ID。
1. 對於**用戶端機密**，輸入您以前記錄的用戶端金鑰。
1. 保留**範圍**、**回應類型**和**回應模式**的預設值。
1. （可選）輸入**Domain_hint**的值。 例如 *，ContosoAD*。 這是在要求中使用 *domain_hint* 參考此識別提供者時，要使用的值。
1. 在**標識提供程式宣告對應**下，輸入以下宣告對應值：

    * **使用者 ID**： *oid*
    * **顯示名稱**：*名稱*
    * **給定名稱**： *given_name*
    * **姓氏**： *family_name*
    * **電子郵件**： *unique_name*

1. 選取 [儲存]****。
