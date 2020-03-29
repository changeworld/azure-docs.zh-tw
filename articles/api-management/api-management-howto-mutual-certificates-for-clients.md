---
title: 在 API 管理中使用用戶端憑證身份驗證保護 API
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76713142"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的用戶端憑證驗證保護 API

API 管理提供以用戶端憑證保護對 API 之存取 (例如，用戶端對 API 管理) 的功能。 可以使用策略運算式根據所需的值驗證傳入證書和檢查證書屬性。

有關使用用戶端憑證（即從後端進行 API 管理）保護對 API 後端服務的訪問的資訊，請參閱[如何使用用戶端憑證身份驗證保護後端服務](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> 要通過開發人員、基本、標準或高級層中的 HTTP/2 接收和驗證用戶端憑證，必須在"自訂域"邊欄選項卡上打開"協商用戶端憑證"設置，如下所示。

![協商用戶端憑證](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> 要接收和驗證"消耗"層中的用戶端憑證，必須打開"自訂域"邊欄選項卡上的"請求用戶端憑證"設置，如下所示。

![請求用戶端憑證](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

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
> 禁用檢查憑證撤銷清單使用`context.Request.Certificate.VerifyNoRevocation()`而不是`context.Request.Certificate.Verify()`。
> 如果用戶端憑證是自簽名的，則必須將根（或中間）CA 憑證[上載](api-management-howto-ca-certificates.md)到 API 管理，以便`context.Request.Certificate.Verify()`並`context.Request.Certificate.VerifyNoRevocation()`正常工作。

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
> 禁用檢查憑證撤銷清單使用`context.Request.Certificate.VerifyNoRevocation()`而不是`context.Request.Certificate.Verify()`。
> 如果用戶端憑證是自簽名的，則必須將根（或中間）CA 憑證[上載](api-management-howto-ca-certificates.md)到 API 管理，以便`context.Request.Certificate.Verify()`並`context.Request.Certificate.VerifyNoRevocation()`正常工作。

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
> 禁用檢查憑證撤銷清單使用`context.Request.Certificate.VerifyNoRevocation()`而不是`context.Request.Certificate.Verify()`。
> 如果用戶端憑證是自簽名的，則必須將根（或中間）CA 憑證[上載](api-management-howto-ca-certificates.md)到 API 管理，以便`context.Request.Certificate.Verify()`並`context.Request.Certificate.VerifyNoRevocation()`正常工作。

> [!TIP]
> [本文](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672)中描述的用戶端憑證鎖死問題可以通過多種方式表現出來，例如請求凍結、請求在超時後導致`403 Forbidden`狀態碼，`context.Request.Certificate`是`null`。 此問題通常影響`POST`內容長度`PUT`約為 60KB 或更大的請求。
> 為防止此問題發生，請打開"自訂域"邊欄選項卡上所需主機名稱的"協商用戶端憑證"設置，如下所示。 此功能在"消耗"層中不可用。

![協商用戶端憑證](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>後續步驟

-   [如何使用用戶端憑證驗證來保護後端服務](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [如何上傳憑證](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
