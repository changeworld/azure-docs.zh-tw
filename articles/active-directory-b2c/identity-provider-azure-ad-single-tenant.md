---
title: 設定 Azure AD 組織的登入
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a079cfe155119a6afe8575767dd3e7c09a564f9a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445625"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入

若要在 Azure AD B2C 中使用 Azure Active Directory (Azure AD) 做為[識別提供者](authorization-code-flow.md)，您需要建立代表該識別提供者的應用程式。 本文說明如何讓特定 Azure AD 組織中的使用者能夠使用 Azure AD B2C 中的使用者流程登入。

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>將 Azure AD 設定成識別提供者

1. 確定您使用的目錄包含 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂閱] 篩選，並選擇包含您 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [識別提供者]，然後選取 [新增 OpenID Connect 提供者]。
1. 輸入 [名稱]。 例如，輸入 *Contoso Azure AD*。
1. 針對 [中繼資料 URL] 輸入以下 URL，並將 `{tenant}` 取代為您的 Azure AD 租用戶網域名稱：

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    例如： `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration` 。
    例如： `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration` 。

1. 在 [用戶端識別碼]中，輸入您先前記錄的應用程式識別碼。
1. 在 [用戶端密碼] 中，輸入您先前記錄的用戶端密碼。
1. 在 [ **範圍**] 中，輸入 `openid profile` 。
1. 保留 **回應類型** 的預設值，以及 **回應模式**。
1.  (**網域提示** 的選擇性) ，請輸入 `contoso.com` 。 如需詳細資訊，請參閱[使用 Azure Active Directory B2C 設定直接登入](direct-signin.md#redirect-sign-in-to-a-social-provider)。
1. 在 [ **識別提供者宣告對應**] 下，選取下列宣告：

    * **使用者識別碼**：oid
    * **顯示名稱**：name
    * **指定的名稱**：given_name
    * **姓氏**：family_name
    * **電子郵件**： *preferred_username*

1. 選取 [儲存]。
