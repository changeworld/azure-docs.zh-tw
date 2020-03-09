---
title: 可設定的 Azure AD 權杖存留期
titleSuffix: Microsoft identity platform
description: 了解如何設定 Azure AD 所簽發的權杖存留期。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 0b2b9dbe52a5696f21b287402fc4cbaa32b29c73
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377806"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Azure Active Directory 中可設定的權杖存留期 (預覽)

您可以指定 Azure Active Directory (Azure AD) 所簽發的權杖存留期。 不論是針對組織中所有的應用程式、針對多租用戶 (多組織) 應用程式，還是針對組織中特定的服務主體，都可以設定權杖存留期。

> [!IMPORTANT]
> 在預覽期間收到客戶的意見之後，我們已在 Azure AD 條件式存取中實作為[驗證會話管理功能](https://go.microsoft.com/fwlink/?linkid=2083106)。 您可以使用這項新功能，設定 [登入頻率] 來設定重新整理權杖存留期。 2020 5 月1日之後，您將無法使用可設定的權杖存留期原則來設定會話和重新整理權杖。 您仍然可以在淘汰後設定存取權杖存留期。

在 Azure AD 中，原則物件代表在組織中個別應用程式或所有應用程式上強制執行的一組規則。 每個原則類型都具有包含一組屬性的獨特結構，這些屬性會套用至它們已被指派的物件。

您可以為您的組織指定原則做為預設原則。 只要此原則不被優先順序更高的原則覆寫，就會套用至組織中的任何應用程式。 您也可以將原則指派給特定應用程式。 優先順序會因原則類型而異。

> [!NOTE]
> 可設定的權杖存留期原則僅適用于存取 SharePoint Online 和商務用 OneDrive 資源的行動和桌面用戶端，不適用於網頁瀏覽器會話。
> 若要管理 SharePoint Online 和商務用 OneDrive 之網頁瀏覽器會話的存留期，請使用[條件式存取會話存留期](../conditional-access/howto-conditional-access-session-lifetime.md)功能。 請參閱 [SharePoint Online 部落格](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) \(英文\)，以深入了解如何設定閒置工作階段逾時。

## <a name="token-types"></a>權杖類型

您可以針對重新整理權杖、存取權杖、SAML 權杖、會話權杖和識別碼權杖設定權杖存留期原則。

### <a name="access-tokens"></a>存取權杖

用戶端會使用存取權杖來存取受保護的資源。 存取權杖僅可用於特定使用者、用戶端及資源的組合。 存取權杖是不可撤銷的，在到期之前都會一直有效。 惡意執行者若已取得存取權杖，便可在權杖的存留期範圍內使用該權杖。 調整存取權杖存留期是在改進系統效能與增加用戶端在使用者帳戶停用後保留存取權的時間，這兩者之間所做的一項權衡取捨。 系統效能的改進是藉由減少用戶端需要取得新存取權杖的次數來達成。  預設值是 1 小時 - 1 小時後，用戶端必須使用重新整理權杖 (通常以無訊息模式) 取得新的重新整理權杖和存取權杖。 

### <a name="saml-tokens"></a>SAML 權杖

SAML 權杖是由許多以 web 為基礎的 SAAS 應用程式所使用，而且是使用 Azure Active Directory 的 SAML2 通訊協定端點來取得。 應用程式也會使用 WS-同盟來取用它們。 權杖的預設存留期為1小時。 從應用程式的觀點來看，權杖的有效期間是由權杖中 `<conditions …>` 元素的 NotOnOrAfter 值所指定。 在權杖的有效期間結束後，用戶端必須起始新的驗證要求，而不需要透過單一登入（SSO）會話權杖的互動登入，就能滿足這種情況。

您可以使用 `TokenLifetimePolicy`中的 `AccessTokenLifetime` 參數來變更 NotOnOrAfter 的值。 它會設定為原則中設定的存留期（如果有的話），再加上5分鐘的時鐘誤差因數。

請注意，在 `<SubjectConfirmationData>` 元素中指定的主旨確認 NotOnOrAfter 不會受到權杖存留期設定的影響。 

### <a name="refresh-tokens"></a>重新整理權杖

當用戶端取得存取權杖來存取受保護的資源時，用戶端會也會收到重新整理權杖。 當存取權杖到期時，可以使用重新整理權杖來取得一組新的存取/重新整理權杖。 重新整理權杖會繫結至使用者與用戶端組合。 重新整理權杖可以[隨時撤銷](access-tokens.md#token-revocation)，每次使用權杖時，會檢查權杖的有效性。  重新整理權杖不會在用來擷取新存取權杖時撤銷，但是，最好的做法是在取得新權杖時，安全地刪除舊權杖。 

區別機密用戶端與公開用戶端相當重要，因為這會影響可以使用重新整理權杖的時間長度。 如需不同類型用戶端的詳細資訊，請參閱 [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1)。

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>具有機密用戶端重新整理權杖的權杖存留期
機密用戶端是可以安全地儲存用戶端密碼的應用程式。 它們可以證明要求是來自安全的用戶端應用程式，而不是來自惡意的執行者。 例如，Web 應用程式是機密用戶端，因為它可在 Web 伺服器上儲存用戶端密碼。 它不是公開的。 因為這些流程較安全，所以簽發給這些流程的重新整理權杖預設存留期為 `until-revoked`、無法使用原則來變更，而且將不會在自發性密碼重設中撤銷。

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>具有公開用戶端重新整理權杖的權杖存留期

公開用戶端無法安全地儲存用戶端密碼。 例如，iOS/Android 應用程式無法模糊來自資源擁有者的密碼，因此被視為公開用戶端。 您可以在資源上設定原則，讓來自公開用戶端的重新整理權杖只要超過指定的期間，便無法取得一組新的存取/重新整理權杖。 （若要這樣做，請使用「重新整理權杖最大閒置時間」屬性（`MaxInactiveTime`））。您也可以使用原則來設定一個期間，超過這段時間之後就不再接受重新整理權杖。 （若要這樣做，請使用「重新整理權杖最大壽命」屬性）。您可以調整重新整理權杖的存留期，以控制當使用者使用公用用戶端應用程式時，何時以及何時需要重新輸入認證，而不是以無訊息方式重新驗證。

> [!NOTE]
> 最大存留期屬性是可以使用單一權杖的時間長度。 

### <a name="id-tokens"></a>ID 權杖
識別碼權杖會傳遞至網站與原生用戶端。 識別碼權杖包含使用者的設定檔資訊。 識別碼權杖會繫結至特定的使用者與用戶端組合。 識別碼權杖在到期前都會被視為有效。 通常，Web 應用程式會將應用程式中的使用者工作階段存留期，與針對該使用者簽發之識別碼權杖的存留期做比對。 您可以調整識別碼權杖的存留期，以控制 Web 應用程式讓應用程式工作階段到期並要求使用者重新向 Azure AD 進行驗證 (以無訊息方式或以互動方式) 的頻率。

### <a name="single-sign-on-session-tokens"></a>單一登入工作階段權杖
當使用者透過 Azure AD 進行驗證時，系統會透過使用者的瀏覽器和 Azure AD 建立單一登入 (SSO) 工作階段。 SSO 權杖 (採用 Cookie 的形式) 即代表此工作階段。 SSO 會話權杖不會系結至特定的資源/用戶端應用程式。 SSO 工作階段權杖是可撤銷的，而每次使用這些權杖時，系統都會檢查其有效性。

Azure AD 會使用兩種 SSO 工作階段權杖︰持續性和非持續性。 持續性工作階段權杖是由瀏覽器儲存為持續性 Cookie。 非持續性工作階段權杖是儲存為工作階段 Cookie。 （當瀏覽器關閉時，會終結會話 cookie）。通常會儲存非持續性會話權杖。 但是，當使用者在驗證期間選取 [讓我保持登入] 核取方塊時，則會儲存一個持續性工作階段權杖。

非持續性工作階段權杖有 24 小時的存留期。 持續性權杖有 180 天的存留期。 每當 SSO 會話權杖在其有效期間內使用時，有效期間會延長24小時或180天，視權杖類型而定。 如果未在 SSO 工作階段權杖的有效期內使用此權杖，系統就會將其視為過期而不再接受它。

您可以使用原則來設定第一個工作階段權杖簽發之後的時間，超出該時間就不會再接受工作階段權杖。 （若要這樣做，請使用 [會話權杖最大壽命] 屬性）。您可以調整會話權杖的存留期，以控制當使用者使用 web 應用程式時，需要重新輸入認證的時間和頻率，而不是以無訊息方式驗證。

### <a name="token-lifetime-policy-properties"></a>權杖存留期原則屬性
權杖存留期原則是一種包含權杖存留期規則的原則物件。 使用原則的屬性來控制指定的權杖存留期。 如果未設定任何原則，系統就會強制執行預設存留期值。

### <a name="configurable-token-lifetime-properties"></a>可設定的權杖存留期屬性
| 屬性 | 原則屬性字串 | 影響 | 預設 | 最小值 | 最大值 |
| --- | --- | --- | --- | --- | --- |
| 存取權杖存留期 |AccessTokenLifetime<sup>2</sup> |存取權杖、識別碼權杖、SAML2 權杖 |1 小時 |10 分鐘 |1 日 |
| 重新整理權杖最大閒置時間 |MaxInactiveTime |重新整理權杖 |90 天 |10 分鐘 |90 天 |
| 單一要素重新整理權杖最大壽命 |MaxAgeSingleFactor |重新整理權杖 (適用於任何使用者) |直到撤銷為止 |10 分鐘 |直到撤銷為止<sup>1</sup> |
| 多重要素重新整理權杖最大壽命 |MaxAgeMultiFactor |重新整理權杖 (適用於任何使用者) |直到撤銷為止 |10 分鐘 |直到撤銷為止<sup>1</sup> |
| 單一要素工作階段權杖最大壽命 |MaxAgeSessionSingleFactor |工作階段權杖 (持續性和非持續性) |直到撤銷為止 |10 分鐘 |直到撤銷為止<sup>1</sup> |
| 多重要素工作階段權杖最大壽命 |MaxAgeSessionMultiFactor |工作階段權杖 (持續性和非持續性) |直到撤銷為止 |10 分鐘 |直到撤銷為止<sup>1</sup> |

* <sup>1</sup>針對這些屬性，可設定的明確時間長度上限為 365 天。
* <sup>2</sup>為確保 Microsoft 小組 Web 用戶端能夠運作，建議您讓 Microsoft 團隊的 AccessTokenLifetime 保持在15分鐘以上。

### <a name="exceptions"></a>例外狀況
| 屬性 | 影響 | 預設 |
| --- | --- | --- |
| 重新整理權杖最大壽命 (針對沒有足夠撤銷資訊的同盟使用者簽發<sup>1</sup>) |重新整理權杖 (針對沒有足夠撤銷資訊的同盟使用者簽發<sup>1</sup>) |12 小時 |
| 重新整理權杖最大閒置時間 (針對機密用戶端簽發) |重新整理權杖 (針對機密用戶端簽發) |90 天 |
| 重新整理權杖最大壽命 (針對機密用戶端簽發) |重新整理權杖 (針對機密用戶端簽發) |直到撤銷為止 |

* <sup>1</sup>沒有足夠撤銷資訊的同盟使用者包括任何未同步 "LastPasswordChangeTimestamp" 屬性的使用者。 這些使用者只有這個很短的「最大壽命」，因為 AAD 無法確認何時該撤銷繫結至舊認證的權杖 (例如已變更的密碼)，所以必須更頻繁地回頭檢查，以確定使用者和相關聯的權杖仍然有效。 若要改善這項體驗，租用戶管理員必須確定他們已同步 "LastPasswordChangeTimestamp" 屬性 (這可以使用 Powershell 或透過 AADSync 在使用者物件上設定)。

### <a name="policy-evaluation-and-prioritization"></a>原則評估及優先順序
您可以建立權杖存留期原則然後將其指派給特定的應用程式、您的組織和服務主體。 多個原則可以套用至特定應用程式。 生效的權杖存留期原則會遵循下列規則：

* 如果已將原則明確指派給服務主體，就會強制執行該原則。
* 如果未將任何原則明確指派給服務主體，則會強制執行指派給該服務主體之父組織的原則。
* 如果未將任何原則明確指派給服務主體或組織，則會強制執行指派給應用程式的原則。
* 如果未將任何原則指派給服務主體、組織或應用程式物件，則會強制執行預設值。 (請參閱[可設定的權杖存留期屬性](#configurable-token-lifetime-properties)中的表格。)

如需有關應用程式物件與服務主體物件之間關係的詳細資訊，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](app-objects-and-service-principals.md)。

使用權杖時，系統便會評估權杖的有效性。 在所要存取之應用程式上優先順序最高的原則會生效。

這裡使用的所有時間範圍都會根據 C# [TimeSpan](/dotnet/api/system.timespan) \(機器翻譯\) 物件進行格式化：D.HH:MM:SS。  因此，80 天又 30 分鐘為 `80.00:30:00`。  如果前置的 D 為零，則可捨棄，因此 90 分鐘為 `00:90:00`。  

> [!NOTE]
> 以下是範例案例。
>
> 使用者想要存取兩個 Web 應用程式︰Web 應用程式 A 和 Web 應用程式 B。
> 
> 因素：
> * 兩個 Web 應用程式都在相同的父組織中。
> * 「工作階段權杖最大壽命」為 8 小時的權杖存留期原則 1 已設定為父組織的預設值。
> * Web 應用程式 A 是一個一般用途 Web 應用程式且未與任何原則連結。
> * Web 應用程式 B 適用於高度機密的程序。 其服務主體會連結至權杖存留期原則 2，其「工作階段權杖最大壽命」為 30 分鐘。
>
> 在下午 12:00，使用者啟動新的瀏覽器工作階段並嘗試存取 Web 應用程式 A。系統會將使用者重新導向到 Azure AD 並要求使用者登入。 這會在瀏覽器中建立一個帶有工作階段權杖的 Cookie。 系統會將使用者重新導向回 Web 應用程式 A，其中會提供一個可讓使用者存取該應用程式的識別碼權杖。
>
> 接著，在下午 12:15，使用者嘗試存取 Web 應用程式 B。瀏覽器會重新導向到 Azure AD 來偵測工作階段 Cookie。 Web 應用程式 B 的服務主體會與權杖存留期原則 2 連結，但同時也是帶有預設權杖存留期原則 1 之父組織的一部分。 權杖存留期原則 2 會生效，因為與服務主體連結之原則的優先順序高於組織預設原則。 工作階段權杖原先是在過去 30 分鐘內簽發的，因此被視為有效。 系統會將使用者重新導向回 Web 應用程式 B，其中會提供一個授與使用者存取權的識別碼權杖。
>
> 在下午 1:00，使用者嘗試存取 Web 應用程式 A。系統會將使用者重新導向到 Azure AD。 Web 應用程式 A 並未與任何原則連結，但由於它位於帶有預設權杖存留期原則 1 的組織中，因此該原則會生效。 偵測到原先在過去八小時內簽發的工作階段 Cookie。 系統會以無訊息模式將使用者重新導向回具有新識別碼權杖的 Web 應用程式 A。 使用者不需要驗證。
>
> 使用者立即嘗試存取 Web 應用程式 B。系統會將使用者重新導向到 Azure AD。 與先前一樣，權杖存留期原則 2 會生效。 因為權杖已簽發 30 分鐘以上，系統會提示使用者重新輸入其登入認證。 會簽發全新的工作階段權杖和識別碼權杖。 接著，使用者便可存取 Web 應用程式 B。
>
>

## <a name="configurable-policy-property-details"></a>可設定的原則屬性詳細資料
### <a name="access-token-lifetime"></a>存取權杖存留期
**字串：** AccessTokenLifetime

**會影響：** 存取權杖、識別碼權杖、SAML 權杖

**摘要：** 此原則可控制將此資源的存取權杖和識別碼權杖視為有效的期限。 減少存取權杖存留期屬性可減輕存取權杖或識別碼權杖被惡意執行者長時間使用的風險。 （這些權杖無法撤銷）。取捨是會對效能造成負面影響，因為必須更頻繁地取代權杖。

### <a name="refresh-token-max-inactive-time"></a>重新整理權杖最大閒置時間
**字串︰** MaxInactiveTime

**影響：** 重新整理權杖

**摘要：** 此原則可控制在簽發重新整理權杖多久之後，用戶端才不能在嘗試存取此資源時，再使用該權杖來擷取一組新的存取權杖/重新整理權杖。 因為使用重新整理權杖時通常會傳回一個新的重新整理權杖，因此，如果用戶端在指定時間期間使用目前重新整理權杖，嘗試存取任何資源，這個原則會阻止存取。

此原則會強制尚未在其用戶端上變成作用中的使用者必須重新驗證，才能擷取新的重新整理權杖。

「重新整理權杖最大閒置時間」屬性必須設定成低於「單一要素權杖最大壽命」和「多重要素重新整理權杖最大壽命」屬性的值。

### <a name="single-factor-refresh-token-max-age"></a>單一要素重新整理權杖最大壽命
**字串︰** MaxAgeSingleFactor

**影響：** 重新整理權杖

**摘要︰** 此原則可控制使用者在上次僅以單一要素成功驗證之後，可以持續多久使用重新整理權杖來取得一組新的存取/重新整理權杖。 使用者驗證並接收新的重新整理權杖之後，使用者可以使用重新整理權杖流程一段指定的時間。 （只要目前的重新整理權杖未撤銷，且未使用的時間超過非作用中的時間，這就是如此。）此時，會強制使用者重新驗證，以接收新的重新整理權杖。

縮短最大壽命將會強制使用者更頻繁地進行驗證。 因為單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此屬性設定為等於或小於「多重要素重新整理權杖最大壽命」屬性的值。

### <a name="multi-factor-refresh-token-max-age"></a>多重要素重新整理權杖最大壽命
**字串︰** MaxAgeMultiFactor

**影響：** 重新整理權杖

**摘要︰** 此原則可控制使用者在上次使用多重要素成功驗證之後，可以持續多久使用重新整理權杖來取得一組新的存取/重新整理權杖。 使用者驗證並接收新的重新整理權杖之後，使用者可以使用重新整理權杖流程一段指定的時間。 （只要目前的重新整理權杖未撤銷，且未使用的時間超過非作用中時間，這就是如此）。此時，系統會強制使用者重新驗證，以接收新的重新整理權杖。

縮短最大壽命將會強制使用者更頻繁地進行驗證。 因為單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此屬性設定為等於或大於「單一要素重新整理權杖最大壽命」屬性的值。

### <a name="single-factor-session-token-max-age"></a>單一要素工作階段權杖最大壽命
**字串︰** MaxAgeSessionSingleFactor

**影響：** 工作階段權杖 (持續性和非持續性)

**摘要︰** 此原則可控制使用者在上次僅以單一要素成功驗證之後，可以持續多久使用工作階段權杖來取得新的識別碼和工作階段權杖。 使用者驗證並接收新的工作階段權杖之後，使用者可以使用工作階段權杖流程一段指定的時間。 （只要目前的會話權杖未撤銷，而且尚未過期，這就是這種情況）。在指定的一段時間之後，會強制使用者重新驗證，以接收新的會話權杖。

縮短最大壽命將會強制使用者更頻繁地進行驗證。 因為單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此屬性設定為等於或小於「多重要素工作階段權杖最大壽命」屬性的值。

### <a name="multi-factor-session-token-max-age"></a>多重要素工作階段權杖最大壽命
**字串︰** MaxAgeSessionMultiFactor

**影響：** 工作階段權杖 (持續性和非持續性)

**摘要︰** 此原則可控制使用者在上次以多重要素成功驗證之後，可以持續多久使用工作階段權杖來取得新的識別碼和工作階段權杖。 使用者驗證並接收新的工作階段權杖之後，使用者可以使用工作階段權杖流程一段指定的時間。 （只要目前的會話權杖未撤銷，而且尚未過期，這就是這種情況）。在指定的一段時間之後，會強制使用者重新驗證，以接收新的會話權杖。

縮短最大壽命將會強制使用者更頻繁地進行驗證。 因為單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此屬性設定為等於或大於「單一要素工作階段權杖最大壽命」屬性的值。

## <a name="example-token-lifetime-policies"></a>範例權杖存留期原則
當您為應用程式、服務主體及您整體組織建立及管理權杖存留期時，在 Azure AD 中許多案例都是可能的。 在本節中，我們將逐步解說一些常見的原則案例，這些案例可以協助您強制實行下列各項的新規則：

* 權杖存留期
* 權杖最大閒置時間
* 權杖最大壽命

在範例中，您可以了解如何︰

* 管理組織的預設原則
* 為 Web 登入建立原則
* 針對呼叫 Web API 的原生應用程式建立原則
* 管理進階原則

### <a name="prerequisites"></a>Prerequisites
在下列範例中，您建立、更新連結，並刪除應用程式、服務主體和您整體組織的原則。 如果您是 Azure AD 的新手，我們建議您先深入了解[如何取得 Azure AD 租用戶](quickstart-create-new-tenant.md)，然後再利用這些範例繼續進行。  

若要開始使用，請執行下列步驟：

1. 下載最新的 [Azure AD PowerShell 模組公開預覽版本](https://www.powershellgallery.com/packages/AzureADPreview)。
2. 執行 `Connect` 命令以登入您的 Azure AD 管理帳戶。 您每次啟動新的工作階段時執行此命令。

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. 若要查看在組織中建立的所有原則，請執行下列命令。 在下列案例中的大多數操作之後，執行此命令。 執行命令也會協助您取得原則的 ** **。

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>範例：管理組織的預設原則
在此範例中，您會建立一個原則，讓您的使用者在整個組織中的登入頻率較低。 為了這樣做，我們將為「單一要素重新整理權杖」建立一個在整個組織套用的權杖存留期原則。 此原則套用至您組織中的每個應用程式，以及每個尚未設定原則的服務主體。

1. 建立權杖存留期原則。

    1. 將單一要素重新整理權杖設為「直到撤銷為止」。 權杖不會過期直到存取權被撤銷。 建立下列原則定義︰

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2. 若要建立原則，請執行下列命令：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3. 若要查看您的新原則並取得原則的 **ObjectId**，請執行下列命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. 更新原則。

    您可能會決定您在此範例中設定的第一個原則不若您的服務所需的那樣嚴格。 若要設定單一要素重新整理權杖在兩天內過期，請執行下列命令︰

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>範例：為 Web 登入建立原則

在此範例中，您建立會要求使用者提高驗證頻率來登入 Web 應用程式的原則。 此原則會為 Web 應用程式的服務主體設定存取權杖/識別碼權杖的存留期及多重要素工作階段權杖的最大壽命。

1. 建立權杖存留期原則。

    這個 Web 登入原則會將存取權杖/識別碼權杖的存留期及單一要素工作階段權杖最大壽命設定為 2 小時。

    1. 若要建立原則，請執行此命令：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. 若要查看您的新原則並取得原則的 **ObjectId**，請執行下列命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. 將原則指派給服務主體。 您也需要取得服務主體的 **ObjectId**。

    1. 使用[get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) Cmdlet 來查看您組織的所有服務主體或單一服務主體。
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    2. 當您有服務主體時，請執行下列命令：
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>範例：針對呼叫 Web API 的原生應用程式建立原則
在此範例中，您建立會要求使用者減少驗證頻率的原則。 原則也會延長使用者必須重新驗證之前，可以是非使用中的時間量。 原則會套用到 Web API。 當原生應用程式要求 Web API 做為資源時，會套用此原則。

1. 建立權杖存留期原則。

    1. 若要為 Web API 建立嚴格的原則，請執行下列命令：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. 若要查看您的新原則，請執行下列命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. 將原則指派給 Web API。 您也需要取得應用程式的 **ObjectId**。 使用[get-azureadapplication](/powershell/module/azuread/get-azureadapplication) Cmdlet 來尋找您的應用程式的**ObjectId**，或使用[Azure 入口網站](https://portal.azure.com/)。

    取得應用程式的**ObjectId**並指派原則：

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>範例：管理進階原則
在此範例中，您會建立一些原則，以瞭解優先順序系統的運作方式。 您也會學到如何管理多個套用至數個物件的原則。

1. 建立權杖存留期原則。

    1. 若要建立一個將「單一要素重新整理權杖」存留期設定為 30 天的組織預設原則，請執行下列命令：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2. 若要查看您的新原則，請執行下列命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. 將原則指派給服務主體。

    現在，您具有原則，該原則套用到整個組織。 您可能想要針對特定的服務主體保留這個 30 天原則，但是將組織預設原則變更為上限「直到撤銷為止」。

    1. 若要查看您組織的所有服務主體，請使用[get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) Cmdlet。

    2. 當您有服務主體時，請執行下列命令：

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

3. 將 `IsOrganizationDefault` 旗標設為 false：

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. 建立新的組織預設原則：

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    現在，原始原則已連結至您的服務主體，且新原則已設定為您的組織預設原則。 請務必記住，套用至服務主體的原則優先順序會高於組織預設原則。

## <a name="cmdlet-reference"></a>Cmdlet 參考

### <a name="manage-policies"></a>管理原則

您可以使用下列 Cmdlet 來管理原則。

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

建立新的原則。

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| 參數 | 描述 | 範例 |
| --- | --- | --- |
| <code>&#8209;Definition</code> |字串化 JSON 的陣列，包含所有原則的規則。 | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |原則名稱的字串。 |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |如果為 true，就會將原則設定為組織的預設原則。 如果為 false，則不會執行任何動作。 |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |原則類型。 針對權杖存留期，請一律使用 "TokenLifetimePolicy"。 | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [選用] |設定原則的替代識別碼。 |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
取得所有 Azure AD 原則或指定的原則。

```powershell
Get-AzureADPolicy
```

| 參數 | 描述 | 範例 |
| --- | --- | --- |
| <code>&#8209;Id</code> [選用] |您想要之原則的**ObjectId （ID）** 。 |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
取得與原則連結的所有應用程式和服務主體。

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| 參數 | 描述 | 範例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |您想要之原則的**ObjectId （ID）** 。 |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
更新現有的原則。

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| 參數 | 描述 | 範例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |您想要之原則的**ObjectId （ID）** 。 |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |原則名稱的字串。 |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [選用] |字串化 JSON 的陣列，包含所有原則的規則。 |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [選用] |如果為 true，就會將原則設定為組織的預設原則。 如果為 false，則不會執行任何動作。 |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [選用] |原則類型。 針對權杖存留期，請一律使用 "TokenLifetimePolicy"。 |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [選用] |設定原則的替代識別碼。 |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
刪除指定的原則。

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| 參數 | 描述 | 範例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |您想要之原則的**ObjectId （ID）** 。 | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>應用程式原則
您可以針對應用程式原則使用下列 Cmdlet。</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
將指定的原則連結至應用程式。

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| 參數 | 描述 | 範例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |應用程式的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |原則的 **ObjectId**。 | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
取得指派給應用程式的原則。

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| 參數 | 描述 | 範例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |應用程式的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
從應用程式移除原則。

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| 參數 | 描述 | 範例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |應用程式的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |原則的 **ObjectId**。 | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>服務主體原則
您可以針對服務主體原則使用下列 Cmdlet。

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
將指定的原則連結至服務主體。

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| 參數 | 描述 | 範例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |應用程式的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |原則的 **ObjectId**。 | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
取得與指定的服務主體連結的任何原則。

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| 參數 | 描述 | 範例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |應用程式的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
從指定的服務主體移除原則。

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| 參數 | 描述 | 範例 |
| --- | --- | --- |
| <code>&#8209;Id</code> |應用程式的**ObjectId （ID）** 。 | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |原則的 **ObjectId**。 | `-PolicyId <ObjectId of Policy>` |
