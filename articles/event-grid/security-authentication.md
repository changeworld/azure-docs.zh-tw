---
title: Azure Event Grid 安全性與驗證
description: 本文說明驗證事件方格資源存取權的不同方式（WebHook、訂閱、自訂主題）
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 528c3613549ee49009f99d45e5bd9c2cf1745d78
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779989"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>驗證對 Azure 事件方格資源的存取
本文提供下列案例的相關資訊：  

- 使用共用存取簽章（SAS）或金鑰，驗證將事件發佈到 Azure 事件方格主題的用戶端。 
- 使用 Azure Active Directory （Azure AD）來保護您的 webhook 端點，以驗證事件方格以將事件**傳遞**至端點。

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>使用 SAS 或金鑰來驗證發佈用戶端
自訂主題使用共用存取簽章 (SAS) 或金鑰驗證。 我們建議使用 SAS，但金鑰驗證提供簡單的程式編寫，並且與許多現有的 Webhook 發佈者相容。

您要在 HTTP 標題包含驗證值。 若選擇 SAS，請使用 **aeg-sas-token** 作為標題的值。 若選擇金鑰驗證，請使用 **aeg-sas-key** 作為標題的值。

### <a name="key-authentication"></a>金鑰驗證

金鑰驗證是最簡單的驗證方法。 請使用此格式：`aeg-sas-key: <your key>`

舉例來說，您將金鑰與此一起傳遞：

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS 權杖

Event Grid 的 SAS 權杖包含資源、過期時間及簽章。 SAS 權杖的格式為：`r={resource}&e={expiration}&s={signature}`。

這裡的資源是您傳送事件之目標 Event Grid 主題的路徑。 例如，有效的資源路徑為： `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`。 若要查看所有支援的 API 版本，請參閱[EventGrid 資源類型](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)。 

簽章由您從金鑰產生。

以下為一個有效 **aeg-sas-token** 值的例子：

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

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

### <a name="encryption-at-rest"></a>待用加密

由「事件方格」服務寫入磁片的所有事件或資料都會由 Microsoft 管理的金鑰加密，以確保它會在待用時加密。 此外，事件或資料保留的最長時間為24小時，遵循[事件方格重試原則](delivery-and-retry.md)。 事件方格會在24小時或事件存留時間之後自動刪除所有事件或資料，以較少者為准。

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>驗證將事件傳遞至 webhook 端點
下列各節說明如何驗證將事件傳遞至 webhook 端點。 無論您使用哪一種方法，都必須使用驗證交握機制。 如需詳細資訊，請參閱[Webhook 事件傳遞](webhook-event-delivery.md)。 

### <a name="using-azure-active-directory-azure-ad"></a>使用 Azure Active Directory （Azure AD）
您可以使用 Azure Active Directory （Azure AD）來驗證並授權事件方格，以將事件傳遞至您的端點，以保護您的 webhook 端點。 您必須建立 Azure AD 應用程式、在應用程式授權事件方格中建立角色和服務主體，並設定事件訂用帳戶以使用 Azure AD 應用程式。 [瞭解如何使用事件方格設定 Azure Active Directory](secure-webhook-delivery.md)。

### <a name="using-client-secret-as-a-query-parameter"></a>使用用戶端密碼作為查詢參數
您可以在建立事件訂閱時將查詢參數新增至 Webhook URL，以保護您的 Webhook 端點。 將其中一個查詢參數設定為用戶端密碼，例如[存取權杖](https://en.wikipedia.org/wiki/Access_token)或共用密碼。 Webhook 可以使用秘密來辨識事件是否來自「事件方格」且具有有效的權限。 事件格線會在傳遞至 Webhook 的每個事件中包含這些查詢參數。 如果用戶端密碼已更新，則也需要更新事件訂用帳戶。 為避免在此秘密輪替期間發生傳遞失敗，請在有限的期間內讓 webhook 接受舊密碼和新的秘密。 

由於查詢參數可能包含用戶端密碼，因此會特別小心處理。 它們會以加密方式儲存，而且不能供服務操作員存取。 它們不會記錄為服務記錄/追蹤的一部分。 編輯事件訂閱時，除非在 Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 中使用 [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) 參數，否則不會顯示或傳回查詢參數。

如需將事件傳遞至 webhook 的詳細資訊，請參閱[Webhook 事件傳遞](webhook-event-delivery.md)

> [!IMPORTANT]
Azure 事件方格僅支援**HTTPS** webhook 端點。 

## <a name="next-steps"></a>後續步驟

- 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)
