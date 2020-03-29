---
title: 配置應用的發佈域 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何配置應用程式的發行者域，以便使用者知道他們的資訊發送位置。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 68040c8ee22454c300296493b6c840eabbca98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697127"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>如何：配置應用程式的發佈域

應用程式的發行者域在[應用程式的同意提示](application-consent-experience.md)上向使用者顯示，以便使用者知道他們的資訊發送位置。 2019 年 5 月 21 日之後註冊的沒有發佈伺服器域的多租戶應用程式將顯示為**未經驗證**的 。 多租戶應用程式是支援單個組織目錄之外的帳戶的應用程式;例如，支援所有 Azure AD 帳戶，或支援所有 Azure AD 帳戶和個人 Microsoft 帳戶。

## <a name="new-applications"></a>新應用程式

註冊新應用時，應用的發行者域可能會設置為預設值。 該值取決於應用的註冊位置，特別是應用是否已在租戶中註冊，以及租戶是否具有租戶已驗證的域。

如果存在租戶驗證的域，則應用的發行者域將預設為租戶的主已驗證域。 如果沒有租戶驗證的域（當應用程式未在租戶中註冊時，情況就是這種情況），則應用的發行者域將設置為 null。

下表總結了發行者域值的預設行為。  

| 租戶驗證的域 | 發行者域的預設值 |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - €.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com（主要） | domain2.com |

如果未設置多租戶應用程式的發行者域，或者如果該域設置為以 .onmicrosoft.com 結尾的域，則應用的同意提示將顯示**未經驗證**的發行者域。

## <a name="grandfathered-applications"></a>祖父應用程式

如果應用在 2019 年 5 月 21 日之前註冊，則如果您尚未設置發佈伺服器域，則應用程式的同意提示將不會顯示**未經驗證**。 我們建議您設置發行者域值，以便使用者可以在應用的同意提示上看到此資訊。

## <a name="configure-publisher-domain-using-the-azure-portal"></a>使用 Azure 門戶配置發行者域

要設置應用的發行者域，請按照以下步驟操作。

1. 使用工作或學校帳戶或個人 Microsoft 帳戶登錄到[Azure 門戶](https://portal.azure.com)。

1. 如果您的帳戶出現在多個 Azure AD 租用戶中：
   1. 從頁面右上角的功能表中選取您的設定檔，然後**切換目錄**。
   1. 將工作階段變更為要在其中建立應用程式的 Azure AD 租用戶。

1. 導航到[Azure 活動目錄>應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)以查找並選擇要配置的應用。

   選擇應用後，您將看到應用的 **"概述"** 頁面。

1. 在應用的 **"概述"** 頁面中，選擇 **"品牌"** 部分。

1. 查找**發佈伺服器域**欄位並選擇以下選項之一：

   - 如果尚未配置域，請選擇 **"配置域**"。
   - 如果已配置域，請選擇 **"更新域**"。

如果你的應用在租戶中註冊，您將看到兩個選項卡可供選擇：**選擇已驗證的域**並**驗證新域**。

如果應用未在租戶中註冊，則只會看到驗證應用程式新域的選項。

### <a name="to-verify-a-new-domain-for-your-app"></a>驗證應用的新域

1. 創建名為`microsoft-identity-association.json`的檔並粘貼以下 JSON 程式碼片段。

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. 將預留位置 *[YOUR-APP-ID-HERE]* 替換為與您的應用對應的應用程式（用戶端）ID。

1. 將檔託管在： `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`。 替換預留位置 *[YOUR-DOMAIN-HERE]* 以匹配已驗證的域。

1. 按一下"**驗證並保存域**"按鈕。

### <a name="to-select-a-verified-domain"></a>選擇已驗證的域

- 如果租戶已驗證域，請從 **"選擇已驗證的域**下拉清單"中選擇其中一個域。

>[!Note]
> 應返回的預期"內容類型"標頭為`application/json`。 如果您使用其他類似內容，您可能會收到如下所述錯誤`application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>對應用同意提示的影響

配置發行者域會影響使用者在應用同意提示上看到的內容。 要完全理解同意提示的元件，請參閱[理解申請同意體驗](application-consent-experience.md)。

下表描述了 2019 年 5 月 21 日之前創建的應用程式的行為。

![2019 年 5 月 21 日之前創建的應用程式的同意提示](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

2019 年 5 月 21 日之後創建的新應用程式的行為將取決於發行者域和應用程式類型。 下表描述了使用不同的配置組合希望看到的更改。

![2019 年 5 月 21 日之後創建的應用的同意提示](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>重定向 URI 的影響

使用任何工作或學校帳戶登錄使用者的應用程式或個人 Microsoft 帳戶 （[多租戶](single-and-multi-tenant-apps.md)） 在指定重定向 URI 時受到的限制很少。

### <a name="single-root-domain-restriction"></a>單根域限制

當多租戶應用的發行者域值設置為 null 時，應用將限制為重定向 URI 共用單個根域。 例如，不允許以下值組合，因為根域contoso.com與fabrikam.com不匹配。

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>子域限制

子域是允許的，但您必須顯式註冊根域。 例如，當以下 URI 共用單個根域時，不允許組合。

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

但是，如果開發人員顯式添加根域，則允許組合。

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>例外狀況

以下情況不受單根域限制：

- 單個租戶應用或以單個目錄中的帳戶為目標的應用
- 使用本地主機作為重定向 URI
- 使用自訂方案（非 HTTP 或 HTTPS）重定向 URI

## <a name="configure-publisher-domain-programmatically"></a>以程式設計方式配置發行者域

目前，沒有 REST API 或 PowerShell 支援以程式設計方式配置發行者域。
