---
title: 重新導向 URI (回復 URL) 限制 |Azure
titleSuffix: Microsoft identity platform
description: Microsoft 身分識別平臺強制執行的重新導向 URI (回復 URL) 格式的限制和限制說明。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 08/07/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8be13a299de0fc3de0acaf0001722d8c96a460e6
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88205937"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>重新導向 URI (回復 URL) 限制和限制

[重新導向 URI] 或 [回復 URL] 是指當應用程式成功授權並授與授權碼或存取權杖之後，授權伺服器傳送使用者的位置。 授權伺服器會將程式碼或權杖傳送至重新導向 URI，因此請務必在應用程式註冊過程中註冊正確的位置。

 下列限制會套用至重新導向 URI：

* 重新導向 URI 的開頭必須是配置 `https` 。

* 重新導向 URI 會區分大小寫。 其大小寫必須符合您執行中應用程式之 URL 路徑的大小寫。 例如，如果您的應用程式包含作為其路徑的一部分 `.../abc/response-oidc` ，請勿 `.../ABC/response-oidc` 在重新導向 URI 中指定。 由於網頁瀏覽器會將路徑視為區分大小寫，因此，如果將與 `.../abc/response-oidc` 相關聯的 Cookie 重新導向至不相符的 `.../ABC/response-oidc` URL，可能就會予以排除。

## <a name="maximum-number-of-redirect-uris"></a>重新導向 URI 數目的上限

下表顯示您可以新增至 Microsoft 身分識別平臺中的應用程式註冊的重新導向 Uri 數目上限。

| 已登入的帳戶 | 重新導向 URI 數目的上限 | 描述 |
|--------------------------|---------------------------------|-------------|
| Microsoft 的公司或學校帳戶，位於任何組織的 Azure Active Directory (Azure AD) 租用戶中 | 256 | 應用程式資訊清單中的 `signInAudience` 欄位會設定為 *AzureADMyOrg* 或 *AzureADMultipleOrgs* |
| 個人 Microsoft 帳戶及公司與學校帳戶 | 100 | 應用程式資訊清單中的 `signInAudience` 欄位會設定為 *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>URI 長度上限

針對您新增至應用程式註冊的每個重新導向 URI，您最多可以使用256個字元。

## <a name="supported-schemes"></a>支援的配置

Azure Active Directory (Azure AD) 應用程式模型目前針對登入任何組織之 Azure AD 租使用者中工作或學校帳戶的應用程式，同時支援 HTTP 和 HTTPS 架構。 這些帳戶類型是由 `AzureADMyOrg` `AzureADMultipleOrgs` `signInAudience` 應用程式資訊清單的欄位中的和值所指定。 對於登入個人 Microsoft 帳戶的應用程式 (MSA) *和* 公司和學校帳戶 (也就是 `signInAudience` 設定為 `AzureADandPersonalMicrosoftAccount`) ，只允許 HTTPS 配置。

若要將具有 HTTP 配置的重新導向 Uri 新增至登入工作或學校帳戶的應用程式註冊，您必須在 Azure 入口網站的 [應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908) 中使用應用程式資訊清單編輯器。 不過，雖然您可以使用資訊清單編輯器來設定以 HTTP 為基礎的重新導向 URI，但我們 *強烈* 建議您針對重新導向 URI 使用 HTTPS 配置。

## <a name="localhost-exceptions"></a>Localhost 例外狀況

根據 [RFC 8252 區段 8.3](https://tools.ietf.org/html/rfc8252#section-8.3) 和 [7.3](https://tools.ietf.org/html/rfc8252#section-7.3)，「回送」或「Localhost」重新導向 uri 有兩個特殊考慮：

1. `http` URI 配置是可接受的，因為重新導向絕不會離開裝置。 因此，這兩個都是可接受的：
    - `http://127.0.0.1/myApp`
    - `https://127.0.0.1/myApp`
1. 由於原生應用程式通常需要暫時埠範圍，因此會忽略通訊埠元件 (例如， `:5001` 或 `:443`) 因比對重新導向 URI 的目的而略過。 因此，所有這些都視為相等：
    - `http://127.0.0.1/MyApp`
    - `http://127.0.0.1:1234/MyApp`
    - `http://127.0.0.1:5000/MyApp`
    - `http://127.0.0.1:8080/MyApp`

從開發的觀點來看，這表示幾件事：

* 請勿註冊多個重新導向 Uri，其中只有埠不同。 登入伺服器會任意挑選其中一個，並使用與該重新導向 URI 相關聯的行為 (例如，其是否為 `web` -、 `native` -或 `spa` -類型重新導向) 。
* 如果您需要在 localhost 上註冊多個重新導向 Uri，以便在開發期間測試不同的流程，請使用 URI 的 *路徑* 元件來區分它們。 例如， `http://127.0.0.1/MyWebApp` 不符合 `http://127.0.0.1/MyNativeApp` 。
* 根據 RFC 指導方針，您不應該 `localhost` 在重新導向 URI 中使用。 相反地，請使用實際的回送 IP 位址 `127.0.0.1` 。 這可防止您的應用程式因設定錯誤的防火牆或重新命名的網路介面而中斷。

    目前不支援 () 的 IPv6 回送位址 `[::1]` 。

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>重新導向 Uri 中的萬用字元限制

類似的萬用字元 Uri `https://*.contoso.com` 看起來很方便，但由於安全性的影響，因此應該避免。 根據 OAuth 2.0 規格 ([RFC 6749 的第 3.1.2 節](https://tools.ietf.org/html/rfc6749#section-3.1.2))，重新導向端點 URI 必須是絕對 URI。

在設定用來登入個人 Microsoft 帳戶和公司或學校帳戶的應用程式註冊中，目前不支援萬用字元 Uri。 不過，如果應用程式設定為只登入組織 Azure AD 租使用者中的公司或學校帳戶，則允許使用萬用字元 Uri。

若要將具有萬用字元的重新導向 Uri 新增至登入工作或學校帳戶的應用程式註冊，您必須在 Azure 入口網站的 [應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908) 中使用應用程式資訊清單編輯器。 雖然您可以使用資訊清單編輯器來設定具有萬用字元的重新導向 URI，但我們 *強烈* 建議您遵守 [RFC 6749 的區段 3.1.2](https://tools.ietf.org/html/rfc6749#section-3.1.2) ，並且只使用絕對 uri。

如果您的案例所需的重新導向 Uri 數目超過允許的上限，請考慮 [下列方法](#use-a-state-parameter) ，而不是新增萬用字元重新導向 URI。

### <a name="use-a-state-parameter"></a>使用狀態參數

如果您有數個子域，且您的案例要求在驗證成功之後，您會將使用者重新導向至其啟動所在的相同頁面，使用狀態參數可能會很有説明。

在此方法中：

1. 您可以建立每個應用程式的「共用」重新導向 URI，以處理從授權端點接收的安全性權杖。
1. 您的應用程式可以將應用程式特定的參數（例如，使用者在其中產生的子域 URL 或任何類似商標資訊的專案）傳送 (狀態參數中) 。 使用狀態參數時，請透過 CSRF 保護進行防護，如 [RFC 6749 的第 10.12 節所述](https://tools.ietf.org/html/rfc6749#section-10.12)。
1. 應用程式專屬參數將包含應用程式為使用者呈現正確體驗 (也就是建立適當的應用程式狀態) 所需的所有資訊。 Azure AD 授權端點會從狀態參數中去除 HTML，因此請確定您不會在此參數中傳遞 HTML 內容。
1. 當 Azure AD 將回應傳送至「共用」重新導向 URI 時，其會將狀態參數傳回給應用程式。
1. 然後，應用程式可以使用狀態參數中的值來決定接著要傳送使用者到哪個 URL。 請確定您已針對 CSRF 保護進行驗證。

> [!WARNING]
> 此方法可讓遭入侵的用戶端修改狀態參數中傳送的其他參數，藉此將使用者重新導向至不同的 URL，也就是 RFC 6819 中所述的[開放重新導向程式威脅](https://tools.ietf.org/html/rfc6819#section-4.2.4)。 因此，用戶端必須保護這些參數，方法是將狀態加密或透過其他方式進行驗證，例如根據權杖驗證重新導向 URI 中的功能變數名稱。

## <a name="next-steps"></a>後續步驟

瞭解應用 [程式註冊應用程式資訊清單](reference-app-manifest.md)。
