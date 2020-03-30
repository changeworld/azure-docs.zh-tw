---
title: 使用自訂策略進行電話註冊和登錄（預覽版）
titleSuffix: Azure AD B2C
description: 使用 Azure 活動目錄 B2C 中的自訂策略，在文本消息中向應用程式使用者的電話發送一次性密碼 （OTP）。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183953"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>在 Azure AD B2C（預覽）中使用自訂策略設置電話註冊和登錄

在 Azure 活動目錄 B2C（Azure AD B2C） 中使用手機註冊和登錄，使使用者能夠使用一次性密碼 （OTP） 向其手機傳送簡訊來註冊並登錄到應用程式。 一次性密碼有助於將使用者忘記或洩露其密碼的風險降至最低。

按照本文中的步驟使用自訂策略，使您的客戶能夠使用發送到其手機的一次性密碼註冊並登錄到您的應用程式。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>定價

使用 SMS 短信向使用者發送一次性密碼，並且可能會因發送的每條消息向您收費。 有關定價資訊，請參閱[Azure 活動目錄 B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)的**單獨費用**部分。

## <a name="prerequisites"></a>Prerequisites

在設置 OTP 之前，您需要提供以下資源。

* [Azure AD B2C 租用戶](tutorial-create-tenant.md)
* 在租戶中[註冊的 Web 應用程式](tutorial-register-applications.md)
* 上載到租戶的[自訂策略](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>獲取電話註冊&登錄入門包

首先更新電話註冊和登錄自訂策略檔，以便與 Azure AD B2C 租戶一起工作。

以下步驟假定您已完成[先決條件](#prerequisites)，並已將[自訂策略初學者包存儲庫][starter-pack]克隆到本地電腦。

1. 在啟動包回購的本地克隆中查找[電話註冊和登錄自訂策略檔][starter-pack-phone]，或直接下載這些檔。 XML 策略檔位於以下目錄中：

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. 在每個檔中，將字串`yourtenant`替換為 Azure AD B2C 租戶的名稱。 例如，如果 B2C 租戶的名稱為*contosob2c，* 則 所有實例`yourtenant.onmicrosoft.com`都變為`contosob2c.onmicrosoft.com`。

1. 完成"[將應用程式標識添加到 Azure](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) [活動目錄 B2C 中的自訂策略](custom-policy-get-started.md)"開始"的自訂策略部分中的步驟。 在這種情況下，使用在完成先決條件`/phone-number-passwordless/`**`Phone_Email_Base.xml`**"*身份體驗框架*"和 *"代理身份體驗框架*"時註冊的兩個應用程式（**用戶端）標識**進行更新。

## <a name="upload-the-policy-files"></a>上傳策略檔

1. 登錄到 Azure[門戶](https://portal.azure.com)並導航到 Azure AD B2C 租戶。
1. 在 **"策略"** 下，選擇**身份體驗框架**。
1. 選擇 **"上載自訂策略**"。
1. 按以下順序上載策略檔：
    1. *Phone_Email_Base.xml*
    1. *註冊人signinin與Phone.xml*
    1. *註冊人signin與電話或電子郵件.xml*
    1. *設定檔編輯電話僅.xml*
    1. *設定檔編輯PhoneEmail.xml*
    1. *更改電話號碼.xml*
    1. *密碼重設電子郵件.xml*

上載每個檔時，Azure 會添加前`B2C_1A_`綴 。

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 在**自訂策略**下，選擇**B2C_1A_SignUpOrSignInWithPhone。**
1. 在 **"選擇應用程式"** 下，選擇在完成先決條件時註冊的*Webapp1*應用程式。
1. 對於**選擇答覆 URL，** 請選擇`https://jwt.ms`。
1. 選擇 **"立即運行**"並使用電子郵件地址或電話號碼註冊。
1. 選擇 **"立即運行"，** 然後使用相同的帳戶登錄，以確認配置正確。

## <a name="get-user-account-by-phone-number"></a>按電話號碼獲取使用者帳戶

使用電話號碼註冊但不提供恢復電子郵件地址的使用者記錄在 Azure AD B2C 目錄中，其電話號碼為其登錄名稱。 如果使用者然後想要更改其電話號碼，您的説明台或支援小組必須首先找到他們的帳戶，然後更新其電話號碼。

您可以使用[Microsoft 圖形](manage-user-accounts-graph-api.md)按使用者的電話號碼（登錄名稱）查找使用者：

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

例如：

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>後續步驟

您可以在 GitHub 上找到電話註冊和登錄自訂策略初學者包（和其他初學者包）：

[Azure-採樣/主動目錄-b2c-自訂策略啟動包/方案/電話號無密碼][starter-pack-phone]

入門包策略檔使用多重要素驗證技術設定檔和電話號碼聲明轉換：

* [定義 Azure 多重要素驗證技術設定檔](multi-factor-auth-technical-profile.md)
* [定義電話號碼聲明轉換](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
