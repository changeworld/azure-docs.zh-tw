---
title: 驗證用戶端將事件發佈至事件方格自訂主題或網域
description: 本文說明驗證用戶端將事件發佈至事件方格自訂主題的不同方式。
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e934ce0d8f5e31dc8dd7592a2e553cd278af2b10
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019108"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a> (Azure 事件方格) 驗證發佈用戶端
本文提供驗證用戶端的相關資訊，這些用戶端會使用 **存取金鑰** 或共用存取簽章 ** (SAS) ** 權杖來將事件發佈至 Azure 事件方格主題或網域。 我們建議使用 SAS 權杖，但金鑰驗證提供簡單的程式設計，而且與許多現有的 webhook 發行者都相容。  

## <a name="authenticate-using-an-access-key"></a>使用存取金鑰進行驗證
存取金鑰驗證是最簡單的驗證形式。 您可以將存取金鑰傳遞為 HTTP 標頭或 URL 查詢參數。 

### <a name="access-key-in-a-http-header"></a>HTTP 標頭中的存取金鑰
傳遞存取金鑰作為 HTTP 標頭的值： `aeg-sas-key` 。

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>存取金鑰作為查詢參數
您也可以指定 `aeg-sas-key` 作為查詢參數。 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

如需如何取得主題或網域之存取金鑰的指示，請參閱 [取得存取金鑰](get-access-keys.md)。

## <a name="authenticate-using-a-sas-token"></a>使用 SAS 權杖進行驗證
事件方格資源的 SAS 權杖包含資源、到期時間和簽章。 SAS 權杖的格式為：`r={resource}&e={expiration}&s={signature}`。

這裡的資源是您傳送事件之目標 Event Grid 主題的路徑。 以下為一個有效資源路徑的例子：`https://<yourtopic>.<region>.eventgrid.azure.net/api/events`。 若要查看所有支援的 API 版本，請參閱 [Microsoft.EventGrid 資源類型](/azure/templates/microsoft.eventgrid/allversions)。 

首先，以程式設計方式產生 SAS 權杖，然後使用 `aeg-sas-token` 標頭或 `Authorization SharedAccessSignature` 標頭來向事件方格進行驗證。 

### <a name="generate-sas-token-programmatically"></a>以程式設計方式產生 SAS 權杖
下列範例建立了供 Event Grid 使用的 SAS 權杖：

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="using-aeg-sas-token-header"></a>使用 aeg-event-type-sas-token 標頭
以下是將 SAS 權杖作為標頭值傳遞的範例 `aeg-sas-toke` 。 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>使用授權標頭
以下是將 SAS 權杖作為標頭值傳遞的範例 `Authorization` 。 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>後續步驟
請參閱 [事件傳遞驗證](security-authentication.md) ，以瞭解如何使用事件處理常式進行驗證以傳遞事件。 