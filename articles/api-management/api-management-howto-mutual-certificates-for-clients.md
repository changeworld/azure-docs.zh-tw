---
title: 在 API 管理中使用用戶端憑證驗證保護 Api
titleSuffix: Azure API Management
description: 了解如何使用用戶端憑證來保護對 API 的存取
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
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713142"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的用戶端憑證驗證保護 API

API 管理提供以用戶端憑證保護對 API 之存取 (例如，用戶端對 API 管理) 的功能。 您可以使用原則運算式來驗證傳入憑證，並根據所需的值來檢查憑證屬性。

如需使用用戶端憑證保護對 API 後端服務之存取的相關資訊（例如，API 管理到後端），請參閱[如何使用用戶端憑證驗證來保護後端服務](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

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
> 若要停用檢查憑證撤銷清單，請使用 `context.Request.Certificate.VerifyNoRevocation()`，而不是 `context.Request.Certificate.Verify()`。
> 如果用戶端憑證是自我簽署的，則必須將根（或中繼） CA 憑證[上傳](api-management-howto-ca-certificates.md)至 API 管理，以進行 `context.Request.Certificate.Verify()`，`context.Request.Certificate.VerifyNoRevocation()` 才能正常執行。

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
> 若要停用檢查憑證撤銷清單，請使用 `context.Request.Certificate.VerifyNoRevocation()`，而不是 `context.Request.Certificate.Verify()`。
> 如果用戶端憑證是自我簽署的，則必須將根（或中繼） CA 憑證[上傳](api-management-howto-ca-certificates.md)至 API 管理，以進行 `context.Request.Certificate.Verify()`，`context.Request.Certificate.VerifyNoRevocation()` 才能正常執行。

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
> 若要停用檢查憑證撤銷清單，請使用 `context.Request.Certificate.VerifyNoRevocation()`，而不是 `context.Request.Certificate.Verify()`。
> 如果用戶端憑證是自我簽署的，則必須將根（或中繼） CA 憑證[上傳](api-management-howto-ca-certificates.md)至 API 管理，以進行 `context.Request.Certificate.Verify()`，`context.Request.Certificate.VerifyNoRevocation()` 才能正常執行。

> [!TIP]
> 本文所述的用戶端憑證鎖死[問題可以透過](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672)數種方式來資訊清單，例如要求凍結、要求會在超時之後導致 `403 Forbidden` 狀態碼，`context.Request.Certificate` `null`。 此問題通常會影響內容長度大約60KB 或更大的 `POST` 和 `PUT` 要求。
> 若要避免此問題發生，請在 [自訂網域] 分頁上開啟所需主機名稱的「Negotiate 用戶端憑證」設定，如下所示。 此功能不適用於使用層。

![協調用戶端憑證](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>後續步驟

-   [如何使用用戶端憑證驗證來保護後端服務](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [如何上傳憑證](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
