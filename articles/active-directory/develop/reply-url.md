---
title: 重新導向 URI & 回復 URL 限制-Microsoft 身分識別平臺 |Azure
description: 回復 Url/重新導向 Url 限制 & 限制
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8fdc64632be8b5fcb3dca8de2ee833fef25719fe
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656733"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>重新導向 URI/回覆 URL 限制

[重新導向 URI] 或 [回復 URL] 是指當應用程式成功授權並授與授權碼或存取權杖時，授權伺服器會將使用者傳送至的位置。 程式碼或權杖包含在 [重新導向 URI] 或 [回復權杖] 中，因此請務必在應用程式註冊過程中註冊正確的位置。

 下列限制適用于回復 Url：

    * 回復 URL 的開頭必須是配置 `https`。
    * 回復 URL 會區分大小寫。 其大小寫必須符合您執行中應用程式之 URL 路徑的大小寫。 例如，如果您的應用程式包含作為其路徑 `.../abc/response-oidc`的一部分，請勿在回復 URL 中指定 `.../ABC/response-oidc`。 由於網頁瀏覽器會將路徑視為區分大小寫，因此如果將與 `.../abc/response-oidc` 相關聯的 cookie 重新導向至不相符的 `.../ABC/response-oidc` URL，可能會予以排除。
    
## <a name="maximum-number-of-redirect-uris"></a>重新導向 Uri 的數目上限

下表顯示當您註冊應用程式時，可以新增的重新導向 Uri 數目上限。

| 已登入的帳戶 | 重新導向 Uri 的數目上限 | 描述 |
|--------------------------|---------------------------------|-------------|
| 任何組織的 Azure Active Directory （Azure AD）租使用者中的 Microsoft 公司或學校帳戶 | 256 | 應用程式資訊清單中的 `signInAudience` 欄位設定為*AzureADMyOrg*或*AzureADMultipleOrgs* |
| 個人 Microsoft 帳戶和公司和學校帳戶 | 100 | 應用程式資訊清單中的 `signInAudience` 欄位已設定為*AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>URI 長度上限

針對您新增至應用程式註冊的每個重新導向 URI，您最多可以使用256個字元。

## <a name="supported-schemes"></a>支援的架構
Azure AD 應用程式模型現在支援 HTTP 和 HTTPS 架構，適用于在任何組織的 Azure Active Directory （Azure AD）租使用者中登入 Microsoft 公司或學校帳戶的應用程式。 這是應用程式資訊清單中 `signInAudience` 欄位設定為*AzureADMyOrg*或*AzureADMultipleOrgs*。 對於登入個人 Microsoft 帳戶和公司和學校帳戶（`signInAudience` 設定為*AzureADandPersonalMicrosoftAccount*）的應用程式，只允許 HTTPS 配置。

> [!NOTE]
> 新的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗不允許開發人員在 UI 上使用 HTTP 配置來新增 uri。 只有透過應用程式資訊清單編輯器，才支援為登入工作或學校帳戶的應用程式新增 HTTP Uri。 未來，新的應用程式將無法在重新導向 URI 中使用 HTTP 配置。 不過，包含重新導向 Uri 中之 HTTP 配置的繼承應用程式將會繼續工作。 開發人員必須在重新導向 Uri 中使用 HTTPS 配置。

## <a name="restrictions-using-a-wildcard-in-uris"></a>在 Uri 中使用萬用字元的限制

萬用字元 Uri （例如 `https://*.contoso.com`）很方便，但應該避免。 在重新導向 URI 中使用萬用字元會影響安全性。 根據 OAuth 2.0 規格（[RFC 6749 的區段 3.1.2](https://tools.ietf.org/html/rfc6749#section-3.1.2)），重新導向端點 URI 必須是絕對 uri。 

針對設定為登入個人 Microsoft 帳戶和公司或學校帳戶的應用程式，Azure AD 應用程式模型不支援萬用字元 Uri。 不過，已設定為立即在組織的 Azure AD 租使用者中登入公司或學校帳戶的應用程式，允許使用萬用字元 Uri。 
 
> [!NOTE]
> 新的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗不允許開發人員在 UI 上新增萬用字元 uri。 只有透過應用程式資訊清單編輯器，才支援為登入工作或學校帳戶的應用程式新增萬用字元 URI。 未來，新的應用程式將無法在重新導向 URI 中使用萬用字元。 不過，重新導向 Uri 中包含萬用字元的繼承應用程式將會繼續作用。

如果您的案例所需的重新導向 Uri 數目超過允許的上限，而不是新增萬用字元重新導向 URI，請考慮下列方法。

### <a name="use-a-state-parameter"></a>使用 state 參數

如果您有一些子域，而且如果您的案例要求您在成功驗證時將使用者重新導向至其啟動所在的相同頁面，則使用狀態參數可能會很有説明。 

這種方法：

1. 建立每個應用程式的「共用」重新導向 URI，以處理您從授權端點接收的安全性權杖。
1. 您的應用程式可以在 state 參數中，傳送應用程式特定的參數（例如，使用者源自于的子域 URL，或商標資訊之類的任何專案）。 使用 state 參數時，請防範[RFC 6749 第10.12 節](https://tools.ietf.org/html/rfc6749#section-10.12)所指定的 CSRF 保護。 
1. 應用程式特定的參數將包含應用程式為使用者呈現正確體驗所需的所有資訊，也就是建立適當的應用程式狀態。 Azure AD 授權端點會從狀態參數中去除 HTML，因此請確定您不會在此參數中傳遞 HTML 內容。
1. 當 Azure AD 將回應傳送至「共用」重新導向 URI 時，它會將狀態參數傳回給應用程式。
1. 然後，應用程式可以使用 state 參數中的值來決定要進一步傳送使用者的目標 URL。 請確定您已驗證是否有 CSRF 保護。

> [!NOTE]
> 此方法可讓遭入侵的用戶端修改狀態參數中傳送的其他參數，藉此將使用者重新導向至不同的 URL，也就是 RFC 6819 中所述的開啟的重新導向器[威脅](https://tools.ietf.org/html/rfc6819#section-4.2.4)。 因此，用戶端必須保護這些參數，方法是將狀態加密，或以其他方式進行驗證，例如在重新導向 URI 中驗證權杖的功能變數名稱。

## <a name="next-steps"></a>後續步驟

- 瞭解[應用程式資訊清單](reference-app-manifest.md)
