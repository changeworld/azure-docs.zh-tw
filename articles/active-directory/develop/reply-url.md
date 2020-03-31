---
title: 重定向 URI &回復 URL 限制 - 微軟身份平臺 |蔚藍
description: 回復 URL/重定向 URl&限制
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656733"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>重新導向 URI/回覆 URL 限制

重定向 URI 或回復 URL 是授權伺服器在應用成功授權並授予授權代碼或訪問權杖後將使用者發送到的位置。 代碼或權杖包含在重定向 URI 或回復權杖中，因此在應用註冊過程中註冊正確的位置非常重要。

 以下限制適用于回復 URL：

    * 答覆 URL 必須以方案`https`開頭。
    * 答覆 URL 區分大小寫。 其大小寫必須與正在運行的應用程式的 URL 路徑的情況匹配。 例如，如果應用程式包含作為其路徑`.../abc/response-oidc`的一部分，請不要在答覆 URL`.../ABC/response-oidc`中指定。 由於 Web 瀏覽器將路徑視為區分大小寫的，因此，`.../abc/response-oidc`如果重定向到案例不匹配`.../ABC/response-oidc`的 URL，可能會排除與路徑關聯的 Cookie。
    
## <a name="maximum-number-of-redirect-uris"></a>重定向 URI 的最大數量

下表顯示了註冊應用時可以添加的最大重定向 URI 數。

| 正在登錄的帳戶 | 重定向 URI 的最大數量 | 描述 |
|--------------------------|---------------------------------|-------------|
| 任何組織的 Azure 活動目錄 （Azure AD） 租戶中的 Microsoft 工作或學校帳戶 | 256 | `signInAudience`應用程式清單中的欄位設置為*AzureADMyOrg*或*AzureAD 多路組織* |
| 個人 Microsoft 帳戶、工作帳戶和學校帳戶 | 100 | `signInAudience`應用程式清單中的欄位設置為*AzureADand個人 Microsoft 帳戶* |

## <a name="maximum-uri-length"></a>最大 URI 長度

對於添加到應用註冊的每個重定向 URI，最多可以使用 256 個字元。

## <a name="supported-schemes"></a>支援的計畫
Azure AD 應用程式模型今天支援對在任何組織的 Azure 活動目錄 （Azure AD） 租戶中登錄 Microsoft 工作或學校帳戶的應用的 HTTP 和 HTTPS 方案。 這是`signInAudience`應用程式清單中的欄位設置為*AzureADMyOrg*或*AzureAD 多維多組織*。 對於在個人 Microsoft 帳戶和工作帳戶和學校帳戶（`signInAudience`設置為*AzureADand個人 Microsoft 帳戶*）中登錄的應用，僅允許 HTTPS 方案。

> [!NOTE]
> 新的[應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗不允許開發人員在 UI 上添加帶有 HTTP 方案的 URI。 僅通過應用清單編輯器支援為登錄工作或學校帳戶的應用添加 HTTP URI。 今後，新應用將無法在重定向 URI 中使用 HTTP 方案。 但是，在重定向 URI 中包含 HTTP 方案的舊應用將繼續工作。 開發人員必須在重定向 URI 中使用 HTTPS 方案。

## <a name="restrictions-using-a-wildcard-in-uris"></a>在 URI 中使用萬用字元的限制

萬用字元 URI（如`https://*.contoso.com`）很方便，但應避免使用。 在重定向 URI 中使用萬用字元具有安全影響。 根據 OAuth 2.0 規範[（RFC 6749 的第 3.1.2 節](https://tools.ietf.org/html/rfc6749#section-3.1.2)），重定向端點 URI 必須是絕對 URI。 

Azure AD 應用程式模型不支援配置為登錄個人 Microsoft 帳戶和工作或學校帳戶的應用的萬用字元 URI。 但是，對於配置為在組織的 Azure AD 租戶中登錄工作或學校帳戶的應用，允許萬用字元 URI。 
 
> [!NOTE]
> 新的[應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗不允許開發人員在 UI 上添加萬用字元 URI。 僅通過應用清單編輯器支援為登錄工作或學校帳戶的應用添加 wilcard URI。 今後，新應用將無法在重定向 URI 中使用萬用字元。 但是，在重定向 URI 中包含萬用字元的舊應用將繼續工作。

如果方案需要的重定向 URI 超過允許的最大值限制，而不是添加萬用字元重定向 URI，請考慮以下方法。

### <a name="use-a-state-parameter"></a>使用狀態參數

如果您有多個子域，並且您的方案要求您在成功身份驗證後將使用者重定向到啟動它們所在的同一頁面，則使用狀態參數可能會有所説明。 

在此方法中：

1. 創建每個應用程式的"共用"重定向 URI，以處理從授權終結點收到的安全權杖。
1. 應用程式可以在狀態參數中發送特定于應用程式的參數（例如使用者所在的子域 URL 或任何品牌資訊）。 使用狀態參數時，要防止[RFC 6749 節 10.12 節中](https://tools.ietf.org/html/rfc6749#section-10.12)指定的 CSRF 保護。 
1. 特定于應用程式的參數將包括應用程式為使用者呈現正確體驗所需的所有資訊，即構造適當的應用程式狀態。 Azure AD 授權終結點從狀態參數中剝離 HTML，因此請確保未在此參數中傳遞 HTML 內容。
1. 當 Azure AD 向"共用"重定向 URI 發送回應時，它將將狀態參數發送回應用程式。
1. 然後，應用程式可以使用狀態參數中的值來確定要進一步向使用者發送到的 URL。 請確保驗證 CSRF 保護。

> [!NOTE]
> 此方法允許受攻擊的用戶端修改狀態參數中發送的其他參數，從而將使用者重定向到其他 URL，這是 RFC 6819 中描述的[打開重定向器威脅](https://tools.ietf.org/html/rfc6819#section-4.2.4)。 因此，用戶端必須通過加密狀態或通過某些其他方法（如根據權杖在重定向 URI 中驗證功能變數名稱）來保護這些參數。

## <a name="next-steps"></a>後續步驟

- 瞭解[應用程式清單](reference-app-manifest.md)
