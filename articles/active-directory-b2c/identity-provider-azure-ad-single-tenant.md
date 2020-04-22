---
title: 為 Azure AD 組織設定登入
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5b21fcd2d3ec5560b01352b112e9ed1bb2404766
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678052"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入

若要在 Azure AD B2C 中使用 Azure Active Directory (Azure AD) 做為[識別提供者](authorization-code-flow.md)，您需要建立代表該識別提供者的應用程式。 本文說明如何讓特定 Azure AD 組織中的使用者能夠使用 Azure AD B2C 中的使用者流程登入。

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>將 Azure AD 設定成識別提供者

1. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器,然後選擇包含 Azure AD B2C 租戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 選擇**識別提供者**,然後選擇 **「新建 OpenID 連線提供者**」。
1. 輸入**名稱**。 例如，輸入 *Contoso Azure AD*。
1. 對**中繼資料網址,**`{tenant}`輸入以下網址 取代為 Azure AD 租戶的網域名稱:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    例如： `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration` 。

1. 對於**客戶端 ID,** 輸入您以前記錄的應用程式 ID。
1. 對於**客戶端機密**,輸入您以前記錄的用戶端密鑰。
1. 對**作用網域**,輸入`openid profile`。
1. 保存**回應類型**和**回應模式**的預設值。
1. ( 選擇性的 )對於**網域提示**,`contoso.com`請輸入 。 如需詳細資訊，請參閱[使用 Azure Active Directory B2C 設定直接登入](direct-signin.md#redirect-sign-in-to-a-social-provider)。
1. 在 **「識別提供程式宣告映射」下**,選擇以下聲明:

    * **使用者代碼**: *oid*
    * **顯示名稱**:*名稱*
    * **給定名稱**: *given_name*
    * **姓氏**: *family_name*
    * **電子郵件**: *unique_name*

1. 選取 [儲存]  。
