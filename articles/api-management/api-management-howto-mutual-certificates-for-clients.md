---
title: 在 API 管理中使用用戶端憑證驗證保護 Api
titleSuffix: Azure API Management
description: 瞭解如何使用用戶端憑證來保護對 Api 的存取。 您可以使用原則運算式來驗證傳入的憑證。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 19e0d741d959eba704f26e7e8f7b5d311aa77775
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904853"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的用戶端憑證驗證保護 API

API 管理提供以用戶端憑證保護對 API 之存取 (例如，用戶端對 API 管理) 的功能。 您可以使用原則運算式來驗證傳入憑證，並根據所需的值來檢查憑證屬性。

如需使用用戶端憑證來保護 API 後端服務存取的詳細資訊 (例如，對後端) 的 API 管理，請參閱[如何使用用戶端憑證驗證來保護後端服務](./api-management-howto-mutual-certificates.md)

> [!IMPORTANT]
> 若要透過 HTTP/2 在開發人員、基本、標準或高階層中接收和驗證用戶端憑證，您必須在 [自訂網域] 分頁上開啟 [協商用戶端憑證] 設定，如下所示。

![協調用戶端憑證](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> 若要接收並確認取用層中的用戶端憑證，您必須在 [自訂網域] 分頁上開啟 [要求用戶端憑證] 設定，如下所示。

![要求用戶端憑證](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>檢查簽發者和主旨

您可以設定下面的原則來檢查用戶端憑證的簽發者和主旨：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 若要停用檢查憑證撤銷清單，請使用， `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()` 。
> 如果用戶端憑證是自我簽署的，根 (或中繼) CA 憑證 (s) 必須[上傳](api-management-howto-ca-certificates.md)至 API 管理 `context.Request.Certificate.Verify()` ， `context.Request.Certificate.VerifyNoRevocation()` 才能正常執行。

## <a name="checking-the-thumbprint"></a>檢查指紋

您可以設定下面的原則來檢查用戶端憑證的指紋：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 若要停用檢查憑證撤銷清單，請使用， `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()` 。
> 如果用戶端憑證是自我簽署的，根 (或中繼) CA 憑證 (s) 必須[上傳](api-management-howto-ca-certificates.md)至 API 管理 `context.Request.Certificate.Verify()` ， `context.Request.Certificate.VerifyNoRevocation()` 才能正常執行。

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>檢查指紋是否符合已上傳到 API 管理的憑證

下列範例說明如何檢查用戶端憑證的指紋是否符合已上傳到 API 管理的憑證：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> 若要停用檢查憑證撤銷清單，請使用， `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()` 。
> 如果用戶端憑證是自我簽署的，根 (或中繼) CA 憑證 (s) 必須[上傳](api-management-howto-ca-certificates.md)至 API 管理 `context.Request.Certificate.Verify()` ， `context.Request.Certificate.VerifyNoRevocation()` 才能正常執行。

> [!TIP]
> 這[篇文章](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672)中所述的用戶端憑證鎖死問題可以透過數種方式來資訊清單，例如要求凍結、要求在 `403 Forbidden` 超時之後產生狀態碼， `context.Request.Certificate` 是 `null` 。 此問題通常會 `POST` 影響 `PUT` 內容長度約60KB 或更大的要求。
> 若要避免此問題發生，請在 [自訂網域] 分頁上開啟所需主機名稱的「Negotiate 用戶端憑證」設定，如下所示。 此功能不適用於使用層。

![協調用戶端憑證](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>後續步驟

-   [如何使用用戶端憑證驗證來保護後端服務](./api-management-howto-mutual-certificates.md)
-   [如何上傳憑證](./api-management-howto-mutual-certificates.md)
