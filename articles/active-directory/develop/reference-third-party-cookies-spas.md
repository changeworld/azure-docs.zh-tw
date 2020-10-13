---
title: 如何處理 Safari 中的智慧型追蹤保護 (ITP) | Azure
titleSuffix: Microsoft identity platform
description: 當不再允許協力廠商 Cookie 時，會進行單頁應用程式 (SPA) 驗證。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: cc93f4062851f01dd127c108ca60bc240a1940e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87311750"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>處理 Safari 中的 ITP，以及其他會封鎖協力廠商 Cookie 的瀏覽器

如今許多瀏覽器會封鎖協力廠商 Cookie，這些是對網域要求的 Cookie，與瀏覽器列中所顯示的不同。 這會中斷隱含流程，且需要新的驗證模式才能成功登入使用者。 在 Microsoft 身分識別平台中，我們使用授權流程搭配 PKCE 和重新整理權杖，在協力廠商 Cookie 受到封鎖時，讓使用者保持登入。

## <a name="what-is-intelligent-tracking-protection-itp"></a>什麼是智慧型追蹤保護 (ITP)？

Apple Safari 有一個預設會開啟的隱私權保護功能，稱為[智慧型追蹤保護](https://webkit.org/tracking-prevention-policy/)或 ITP。 ITP 會封鎖「協力廠商」Cookie，這是跨網域要求的 Cookie。

使用者追蹤的常見形式，是將 iframe 載入背景中的協力廠商網站中，並使用 Cookie 讓使用者跨網際網路相互關聯。 可惜的是，此模式也是在單頁應用程式 (SPA) 中實作[隱含流程](v2-oauth2-implicit-grant-flow.md)的標準方式。 當瀏覽器封鎖協力廠商 Cookie 以防止使用者追蹤時，SPA 也會中斷。

Safari 不會單獨封鎖協力廠商 Cookie，以增強使用者隱私權。 根據預設，Brave 已封鎖協力廠商 Cookie，而 Chromium (Google Chrome 和 Microsoft Edge 背後的平台) 已宣佈他們在未來也會停止支援協力廠商 Cookie。

本文中所述的解決方案適用於所有的瀏覽器，或是協力廠商 Cookie 受到封鎖的任何位置。

## <a name="overview-of-the-solution"></a>解決方案的概觀

若要繼續在 SPA 中驗證使用者，應用程式開發人員必須使用[授權碼流程](v2-oauth2-auth-code-flow.md)。 在授權碼流程中，身分識別提供者會發出程式碼，而 SPA 會贖回存取權杖和重新整理權杖的程式碼。 當應用程式需要其他權杖時，可以使用[重新整理權杖流程](v2-oauth2-auth-code-flow.md#refresh-the-access-token)來取得新的權杖。 MSAL.js 2.0 是適用於 SPA 的 Microsoft 身分識別平台程式庫，會實作 SPA 的授權碼流程，而次要更新則是 MSAL.js 1.x 的一項替代方案。

針對 Microsoft 身分識別平台，SPA 和原生用戶端會遵循類似的通訊協定指引：

* 使用 [PKCE 程式碼挑戰](https://tools.ietf.org/html/rfc7636)
    * PKCE 是 Microsoft 身分識別平台上 SPA 的「必要項目」。 PKCE 是原生和機密用戶端的「建議項目」。
* 不使用用戶端密碼

SPA 有兩個額外的限制：

* [重新導向 URI 必須標示為類型 `spa`](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps)，才能在登入端點上啟用 CORS。
* 透過授權碼流程發出至 `spa` 重新導向 URI 的重新整理權杖會有 24 小時的存留期，而不是 90 天的存留期。

![SPA 應用程式的程式碼流程](media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.png)

## <a name="performance-and-ux-implications"></a>效能與 UX 的影響

使用隱含流程的某些應用程式會嘗試使用 `prompt=none` 開啟登入 iframe，而不需要重新導向。 在大部分的瀏覽器中，此要求會以目前登入使用者的權杖回應 (假設已授與同意)。 此模式意味著應用程式不需要完整的頁面重新導向，即可將使用者登入、改善效能和使用者體驗，使用者造訪網頁並已登入。 因為當協力廠商 Cookie 受到封鎖時，iframe 中的 `prompt=none` 已不再是選項，所以應用程式必須造訪最上層框架中的登入頁面，才能發出授權碼。

有兩種方式可以完成登入：

* **完整頁面重新導向**
    * 第一次載入 SPA 時，如果未存在任何工作階段 (或工作階段已過期)，請將使用者重新導向至登入頁面。 使用者的瀏覽器會造訪登入頁面、顯示包含使用者工作階段的 Cookie，然後使用片段中的程式碼和權杖重新導向回應用程式。
    * 重新導向會導致 SPA 載入兩次。 遵循 SPA 快取的最佳做法，讓應用程式不會完整下載兩次。
    * 請考慮在應用程式中使用預先載入順序，以檢查登入工作階段並重新導向至登入頁面，應用程式才會完整解壓縮並執行 JavaScript 承載。
* **快顯**
    * 如果在應用程式中，完整頁面重新導向的使用者體驗 (UX) 無法使用，請考慮使用快顯來處理驗證。
    * 當快顯在驗證後完成重新導向至應用程式時，重新導向處理常式中的程式碼會將程式碼和權杖儲存在本機儲存體中，以供應用程式使用。 MSAL.js 支援用於驗證的快顯，如同大部分的程式庫。
    * 瀏覽器會減少對快顯的支援，因此可能不是最可靠的選項。 在建立快顯視窗之前，可能需要使用者與 SPA 進行互動，以滿足瀏覽器的需求。

>[!NOTE]
> Apple [描述一個快顯方法](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)，做為暫時的相容性修正程式，以提供協力廠商 Cookie 的原始視窗存取權。 雖然 Apple 在未來可能會移除此權限轉移，但不會影響此處的指引。 在這裡，會使用快顯視窗做為登入頁面的第一方導覽，從而找到工作階段並提供驗證碼。 這在未來應該會繼續發展。

### <a name="a-note-on-iframe-apps"></a>iframe 應用程式的注意事項

Web 應用程式中常見的模式是使用 iframe 將一個應用程式內嵌在另一個應用程式內。 最上層的框架會處理使用者的驗證，而 iframe 中裝載的應用程式可以信任使用者已登入，並使用隱含流程以無訊息方式擷取權杖。 當協力廠商 Cookie 受到封鎖時，便無法再取得無訊息權杖，iframe 中內嵌的應用程式必須切換為使用快顯來存取使用者的工作階段，因為其無法導覽至登入頁面。

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>在瀏覽器中重新整理權杖的安全性影響

將重新整理權杖發行至瀏覽器會視為安全性問題。 跨網站指令碼 (XSS) 攻擊或遭盜用的 JS 套件可以竊取重新整理權杖，並從遠端加以使用，直到其過期或撤銷為止。 為了將重新整理權杖遭竊的風險降至最低，SPA 只會發出 24 小時有效的權杖。 在 24 小時之後，應用程式必須透過最上層框架取得新的授權碼，才能造訪登入頁面。

選擇這種有限存留期的重新整理權杖模式，做為安全性與降級 UX 之間的平衡。 若沒有重新整理權杖或協力廠商 Cookie，則在需要新的或其他權杖時，授權碼流程 (如 [OAuth 目前安全性最佳做法草稿](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14) 所建議) 會變得很繁重。 每次權杖過期時 (Microsoft 身分識別平台權杖通常是每小時)，每個單一權杖都需要完整的頁面重新導向或快顯視窗。

## <a name="next-steps"></a>後續步驟

深入了解[授權碼流程](v2-oauth2-auth-code-flow.md)。

使用 [MSAL.js 2.0 快速入門](quickstart-v2-javascript-auth-code.md)試用授權碼流程。
