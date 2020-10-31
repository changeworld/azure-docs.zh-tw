---
title: 使用 API 管理中的用戶端憑證驗證來保護 Api
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
ms.openlocfilehash: 4e5522c162e08f0257bd6f20b058bf8bb858cff3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099341"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的用戶端憑證驗證保護 API

API 管理提供以用戶端憑證保護對 API 之存取 (例如，用戶端對 API 管理) 的功能。 您可以使用原則運算式驗證傳入的憑證，並檢查憑證屬性是否符合所需的值。

如需使用用戶端憑證來保護對 API 後端服務之存取的相關資訊 (例如，對後端) 的 API 管理，請參閱 [如何使用用戶端憑證驗證來保護後端服務](./api-management-howto-mutual-certificates.md)

> [!IMPORTANT]
> 若要在開發人員、基本、標準或進階層中透過 HTTP/2 接收及驗證用戶端憑證，您必須開啟 [自訂網域] 分頁的 [Negotiate 用戶端憑證] 設定，如下所示。

![Negotiate 用戶端憑證](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

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
> 若要停用檢查憑證撤銷清單，請使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()` 。
> 如果用戶端憑證是自我簽署的，根 (或中繼) CA 憑證 (s) 必須 [上傳](api-management-howto-ca-certificates.md) 至 API 管理 `context.Request.Certificate.Verify()` ， `context.Request.Certificate.VerifyNoRevocation()` 才能運作。

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
> 若要停用檢查憑證撤銷清單，請使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()` 。
> 如果用戶端憑證是自我簽署的，根 (或中繼) CA 憑證 (s) 必須 [上傳](api-management-howto-ca-certificates.md) 至 API 管理 `context.Request.Certificate.Verify()` ， `context.Request.Certificate.VerifyNoRevocation()` 才能運作。

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
> 若要停用檢查憑證撤銷清單，請使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()` 。
> 如果用戶端憑證是自我簽署的，根 (或中繼) CA 憑證 (s) 必須 [上傳](api-management-howto-ca-certificates.md) 至 API 管理 `context.Request.Certificate.Verify()` ， `context.Request.Certificate.VerifyNoRevocation()` 才能運作。

> [!TIP]
> 本文所述的用戶端憑證鎖死 [問題可能會](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) 以數種方式資訊清單，例如要求凍結，要求 `403 Forbidden` 會在超時後產生狀態碼 `context.Request.Certificate` `null` 。 此問題通常會 `POST` 影響 `PUT` 內容長度大約60KB 或更大的要求。
> 若要避免發生此問題，請在 [自訂網域] 分頁上，針對所需的主機名稱開啟 [Negotiate 用戶端憑證] 設定，如本檔的第一個影像所示。 這項功能不適用於使用量層。


## <a name="next-steps"></a>後續步驟

-   [如何使用用戶端憑證驗證來保護後端服務](./api-management-howto-mutual-certificates.md)
-   [如何上傳憑證](./api-management-howto-mutual-certificates.md)
