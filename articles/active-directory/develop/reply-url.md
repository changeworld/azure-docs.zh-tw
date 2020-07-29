---
title: 重新導向 URI 與回覆 URL 限制 - Microsoft 身分識別平台 |Azure
description: 回覆 URL/重新導向 URl 限制
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/17/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 4fdeb0018e27a2557161b2ec1c4794d975403523
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87311614"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>重新導向 URI/回覆 URL 限制

重新導向 URI 或回覆 URL 是指當應用程式成功授權並授與授權碼或存取權杖時，授權伺服器會將使用者傳送過去的位置。 程式碼或權杖會包含在重新導向 URI 或回覆權杖中，因此請務必在應用程式註冊過程中註冊正確的位置。

 下列限制會套用至回覆 URL：

* 回覆 URL 的開頭必須是配置 `https`。

* 回覆 URL 會區分大小寫。 其大小寫必須符合您執行中應用程式之 URL 路徑的大小寫。 例如，如果您的應用程式包含作為其路徑 `.../abc/response-oidc` 的一部分，請勿在回覆 URL 中指定 `.../ABC/response-oidc`。 由於網頁瀏覽器會將路徑視為區分大小寫，因此，如果將與 `.../abc/response-oidc` 相關聯的 Cookie 重新導向至不相符的 `.../ABC/response-oidc` URL，可能就會予以排除。
    
## <a name="maximum-number-of-redirect-uris"></a>重新導向 URI 數目的上限

下表顯示當您註冊應用程式時，可以新增的重新導向 URI 數目上限。

| 已登入的帳戶 | 重新導向 URI 數目的上限 | 描述 |
|--------------------------|---------------------------------|-------------|
| Microsoft 的公司或學校帳戶，位於任何組織的 Azure Active Directory (Azure AD) 租用戶中 | 256 | 應用程式資訊清單中的 `signInAudience` 欄位會設定為 *AzureADMyOrg* 或 *AzureADMultipleOrgs* |
| 個人 Microsoft 帳戶及公司與學校帳戶 | 100 | 應用程式資訊清單中的 `signInAudience` 欄位會設定為 *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>URI 長度上限

在您新增至應用程式註冊的每個重新導向 URI 上，您最多可以使用 256 個字元。

## <a name="supported-schemes"></a>支援的配置

Azure AD 應用程式模型現在可支援 HTTP 和 HTTPS 配置，適用於在任何組織的 Azure Active Directory (Azure AD) 租用戶者中登入 Microsoft 公司或學校帳戶的應用程式。 也就是，應用程式資訊清單中的 `signInAudience` 欄位會設定為 *AzureADMyOrg* 或 *AzureADMultipleOrgs*。 對於登入個人 Microsoft 帳戶和公司及學校帳戶的應用程式 (`signInAudience` 會設定為 *AzureADandPersonalMicrosoftAccount*)，只允許 HTTPS 配置。

> [!NOTE]
> 新的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗不允許開發人員在 UI 上使用 HTTP 配置來新增 URI。 只有透過應用程式資訊清單編輯器，才能為登入公司或學校帳戶的應用程式新增 HTTP URI。 未來，新的應用程式將無法在重新導向 URI 中使用 HTTP 配置。 不過，在重新導向 URI 中包含 HTTP 配置的舊有應用程式將會繼續運作。 開發人員必須在重新導向 URI 中使用 HTTPS 配置。

## <a name="localhost-exceptions"></a>Localhost 例外狀況

根據[RFC 8252 區段 8.3](https://tools.ietf.org/html/rfc8252#section-8.3)和[7.3](https://tools.ietf.org/html/rfc8252#section-7.3)，「回送」或「Localhost」重新導向 uri 有兩個特殊考慮：

1. `http`URI 配置是可接受的，因為重新導向絕不會離開裝置。  這表示 `http://127.0.0.1/myApp` 是可接受的，也是 `https://127.0.0.1/myApp` 。 
1. 由於原生應用程式通常需要暫時埠範圍，因此會忽略埠元件（例如 `:5001` 或 `:443` ），以符合重新導向 URI 的目的。  因此， `http://127.0.0.1:5000/MyApp` 和 `http://127.0.0.1:1234/MyApp` 兩者都符合 `http://127.0.0.1/MyApp``http://127.0.0.1:8080/MyApp`

從開發的觀點來看，這表示幾件事：

1. 請勿在只有埠不同的情況下註冊多個回復 Uri。  登入伺服器會任意挑選其中一個，並使用與該回復 URI 相關聯的行為（例如，它是否為 `web` 、 `native` 和 `spa` -類型重新導向）。
1. 如果您需要在 localhost 上註冊多個重新導向 Uri，以便在開發期間測試不同的流程，請使用 URI 的*路徑*元件來區分它們。  `http://127.0.0.1/MyWebApp`不符合 `http://127.0.0.1/MyNativeApp` 。  
1. 根據 RFC 指導方針，您不應該 `localhost` 在重新導向 URI 中使用。  相反地，請使用實際的回送 IP 位址- `127.0.0.1` 。 這可防止您的應用程式因設定錯誤的防火牆或重新命名的網路介面而中斷。

>[!NOTE]
> 目前不支援 IPv6 回送（ `[::1]` ）。  這會在日後新增。

## <a name="restrictions-using-a-wildcard-in-uris"></a>在 URI 中使用萬用字元的限制

萬用字元 URI (例如 `https://*.contoso.com`) 很方便，但應該避免。 在重新導向 URI 中使用萬用字元會影響安全性。 根據 OAuth 2.0 規格 ([RFC 6749 的第 3.1.2 節](https://tools.ietf.org/html/rfc6749#section-3.1.2))，重新導向端點 URI 必須是絕對 URI。

針對設定為登入個人 Microsoft 帳戶和公司或學校帳戶的應用程式，Azure AD 應用程式模型不支援萬用字元 URI。 不過，現在已設定為在組織的 Azure AD 租用戶中登入公司或學校帳戶的應用程式中，則允許使用萬用字元 URI。

> [!NOTE]
> 新的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗不允許開發人員在 UI 上新增萬用字元 URI。 只有透過應用程式資訊清單編輯器，才能為登入公司或學校帳戶的應用程式新增萬用字元 URI。 未來，新的應用程式將無法在重新導向 URI 中使用萬用字元。 不過，在重新導向 URI 中包含萬用字元的舊有應用程式將會繼續運作。

如果您案例需要的重新導向 URI 數目超過允許的上限，除了新增萬用字元重新導向 URI 之外，請考慮下列方法。

### <a name="use-a-state-parameter"></a>使用狀態參數

如果您有一些子網域，而且您的案例需要您在成功驗證時將使用者重新導向至其一開始的相同頁面，則使用狀態參數可能會很實用。

在此方法中：

1. 您可以建立每個應用程式的「共用」重新導向 URI，以處理從授權端點接收的安全性權杖。
1. 您的應用程式可以在狀態參數中，傳送應用程式專屬的參數 (例如，使用者原本所在的子網域 URL，或商標資訊之類的任何項目)。 使用狀態參數時，請透過 CSRF 保護進行防護，如 [RFC 6749 的第 10.12 節所述](https://tools.ietf.org/html/rfc6749#section-10.12)。 
1. 應用程式專屬參數將包含應用程式為使用者呈現正確體驗 (也就是建立適當的應用程式狀態) 所需的所有資訊。 Azure AD 授權端點會從狀態參數中去除 HTML，因此請確定您不會在此參數中傳遞 HTML 內容。
1. 當 Azure AD 將回應傳送至「共用」重新導向 URI 時，其會將狀態參數傳回給應用程式。
1. 然後，應用程式可以使用狀態參數中的值來決定接著要傳送使用者到哪個 URL。 請確定您已針對 CSRF 保護進行驗證。

> [!NOTE]
> 此方法可讓遭入侵的用戶端修改狀態參數中傳送的其他參數，藉此將使用者重新導向至不同的 URL，也就是 RFC 6819 中所述的[開放重新導向程式威脅](https://tools.ietf.org/html/rfc6819#section-4.2.4)。 因此，用戶端必須藉由將狀態加密來保護這些參數，或以其他方式進行驗證，例如在重新導向 URI 中驗證權杖的網域名稱。

## <a name="next-steps"></a>後續步驟

- 了解[應用程式資訊清單](reference-app-manifest.md)
