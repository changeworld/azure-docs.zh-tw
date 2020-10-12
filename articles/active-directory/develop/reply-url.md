---
title: 重新導向 URI (回復 URL) 限制 |蔚藍
titleSuffix: Microsoft identity platform
description: Microsoft 身分識別平臺強制執行的重新導向 URI (回復 URL) 格式的限制和限制描述。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 08/07/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: bd6f88db2b55a5f0f445659e4b5ef609d3e146e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030305"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>重新導向 URI (回復 URL) 限制和限制

重新導向 URI （或回復 URL）是在應用程式成功授權並授與授權碼或存取權杖之後，授權伺服器將使用者傳送給使用者的位置。 授權伺服器會將程式碼或權杖傳送至重新導向 URI，因此請務必在應用程式註冊過程中註冊正確的位置。

 下列限制會套用至重新導向 URI：

* 重新導向 URI 的開頭必須是配置 `https` 。

* 重新導向 URI 會區分大小寫。 其大小寫必須符合您執行中應用程式之 URL 路徑的大小寫。 例如，如果您的應用程式包含在它的路徑 `.../abc/response-oidc` 中，請勿 `.../ABC/response-oidc` 在重新導向 URI 中指定。 由於網頁瀏覽器會將路徑視為區分大小寫，因此，如果將與 `.../abc/response-oidc` 相關聯的 Cookie 重新導向至不相符的 `.../ABC/response-oidc` URL，可能就會予以排除。

## <a name="maximum-number-of-redirect-uris"></a>重新導向 URI 數目的上限

下表顯示您可以在 Microsoft 身分識別平臺中新增至應用程式註冊的重新導向 Uri 數目上限。

| 已登入的帳戶 | 重新導向 URI 數目的上限 | 描述 |
|--------------------------|---------------------------------|-------------|
| Microsoft 的公司或學校帳戶，位於任何組織的 Azure Active Directory (Azure AD) 租用戶中 | 256 | 應用程式資訊清單中的 `signInAudience` 欄位會設定為 *AzureADMyOrg* 或 *AzureADMultipleOrgs* |
| 個人 Microsoft 帳戶及公司與學校帳戶 | 100 | 應用程式資訊清單中的 `signInAudience` 欄位會設定為 *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>URI 長度上限

您可以針對您新增至應用程式註冊的每個重新導向 URI 使用最多256個字元。

## <a name="supported-schemes"></a>支援的配置

Azure Active Directory (Azure AD) 應用程式模型目前支援在任何組織的 Azure AD 租使用者中登入工作或學校帳戶的應用程式的 HTTP 和 HTTPS 配置。 這些帳戶類型是由 `AzureADMyOrg` `AzureADMultipleOrgs` `signInAudience` 應用程式資訊清單之欄位中的和值所指定。 針對登入個人 Microsoft 帳戶的應用程式 (MSA) *和* 公司和學校帳戶 (也就是說， `signInAudience` 設定為 `AzureADandPersonalMicrosoftAccount`) ，則只允許 HTTPS 配置。

若要使用 HTTP 配置將重新導向 Uri 新增至登入工作或學校帳戶的應用程式註冊，您需要在 Azure 入口網站的 [應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908) 中使用應用程式資訊清單編輯器。 不過，雖然您可以使用資訊清單編輯器來設定 HTTP 型重新導向 URI，但 *強烈* 建議您將 HTTPS 配置用於您的重新導向 uri。

## <a name="localhost-exceptions"></a>Localhost 例外狀況

根據 [RFC 8252 區段 8.3](https://tools.ietf.org/html/rfc8252#section-8.3) 和 [7.3](https://tools.ietf.org/html/rfc8252#section-7.3)、「回送」或「Localhost」重新導向 uri 有兩個特殊考慮：

1. `http` URI 配置是可接受的，因為重新導向永遠不會離開裝置。 如此一來，這兩個都是可接受的：
    - `http://127.0.0.1/myApp`
    - `https://127.0.0.1/myApp`
1. 由於原生應用程式通常需要暫時的埠範圍，因此 (的埠元件， `:5001` 或為了 `:443` 比對重新導向 URI 的目的，會忽略) 。 如此一來，所有這些專案都會被視為相同：
    - `http://127.0.0.1/MyApp`
    - `http://127.0.0.1:1234/MyApp`
    - `http://127.0.0.1:5000/MyApp`
    - `http://127.0.0.1:8080/MyApp`

從開發的觀點來看，這表示有幾件事：

* 請勿在埠不同的情況下註冊多個重新導向 Uri。 登入伺服器會任意挑選一個，並使用與該重新導向 URI 相關聯的行為 (例如，它是 `web` -、 `native` -或 `spa` -類型的重新導向) 。
* 如果您需要在 localhost 上註冊多個重新導向 Uri，以在開發期間測試不同的流程，請使用 URI 的 *路徑* 元件來區分它們。 例如， `http://127.0.0.1/MyWebApp` 不符合 `http://127.0.0.1/MyNativeApp` 。
* 根據 RFC 指導方針，您不應該 `localhost` 在重新導向 URI 中使用。 相反地，請使用實際的回送 IP 位址 `127.0.0.1` 。 這可防止您的應用程式因防火牆設定錯誤或重新命名網路介面而中斷。

    若要使用 `http` 具有回送位址 (127.0.0.1) 而不是 localhost 的配置，您必須編輯 [應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#replyurls-attribute)。 

    目前不支援 IPv6 回送位址 (`[::1]`) 。

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>重新導向 Uri 中的萬用字元限制

萬用字元 Uri `https://*.contoso.com` 似乎很方便，但由於安全性的影響，應予以避免。 根據 OAuth 2.0 規格 ([RFC 6749 的第 3.1.2 節](https://tools.ietf.org/html/rfc6749#section-3.1.2))，重新導向端點 URI 必須是絕對 URI。

在設定用來登入個人 Microsoft 帳戶和公司或學校帳戶的應用程式註冊中，目前不支援萬用字元 Uri。 但是，如果應用程式設定為只登入組織 Azure AD 租使用者中的公司或學校帳戶，則允許使用萬用字元 Uri。

若要在登入工作或學校帳戶的應用程式註冊中新增具有萬用字元的重新導向 Uri，您需要在 Azure 入口網站的 [應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908) 中使用應用程式資訊清單編輯器。 雖然您可以使用資訊清單編輯器來設定具有萬用字元的重新導向 URI，但 *強烈* 建議您遵循 [RFC 6749 的3.1.2 一節](https://tools.ietf.org/html/rfc6749#section-3.1.2) ，並只使用絕對 uri。

如果您的案例需要比允許的最大限制更多的重新導向 Uri，請考慮 [下列方法](#use-a-state-parameter) ，而不是新增萬用字元重新導向 URI。

### <a name="use-a-state-parameter"></a>使用狀態參數

如果您有數個子域，而您的案例要求您在驗證成功後，將使用者重新導向至他們啟動的相同頁面，使用狀態參數可能會有説明。

在此方法中：

1. 您可以建立每個應用程式的「共用」重新導向 URI，以處理從授權端點接收的安全性權杖。
1. 您的應用程式可以將特定應用程式的參數傳送 (例如，使用者產生的子域 URL，或在 state 參數中) 商標資訊等任何內容。 使用狀態參數時，請透過 CSRF 保護進行防護，如 [RFC 6749 的第 10.12 節所述](https://tools.ietf.org/html/rfc6749#section-10.12)。
1. 應用程式專屬參數將包含應用程式為使用者呈現正確體驗 (也就是建立適當的應用程式狀態) 所需的所有資訊。 Azure AD 授權端點會從狀態參數中去除 HTML，因此請確定您不會在此參數中傳遞 HTML 內容。
1. 當 Azure AD 將回應傳送至「共用」重新導向 URI 時，其會將狀態參數傳回給應用程式。
1. 然後，應用程式可以使用狀態參數中的值來決定接著要傳送使用者到哪個 URL。 請確定您已針對 CSRF 保護進行驗證。

> [!WARNING]
> 此方法可讓遭入侵的用戶端修改狀態參數中傳送的其他參數，藉此將使用者重新導向至不同的 URL，也就是 RFC 6819 中所述的[開放重新導向程式威脅](https://tools.ietf.org/html/rfc6819#section-4.2.4)。 因此，用戶端必須藉由將狀態加密或透過其他方式進行驗證，來保護這些參數，例如在重新導向 URI 中針對權杖驗證功能變數名稱。

## <a name="next-steps"></a>後續步驟

深入瞭解應用程式註冊 [應用程式資訊清單](reference-app-manifest.md)。
