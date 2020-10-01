---
title: 可設定的權杖存留期
titleSuffix: Microsoft identity platform
description: 瞭解如何設定 Microsoft 身分識別平臺所發出的權杖存留期。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: a9bf992a65914afb8fa800041b57ad9f44ba4fa0
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91595606"
---
# <a name="configurable-token-lifetimes-in-microsoft-identity-platform-preview"></a>Microsoft 身分識別平臺中可設定的權杖存留期 (預覽版) 

您可以指定 Microsoft 身分識別平臺所簽發的權杖存留期。 不論是針對組織中所有的應用程式、針對多租用戶 (多組織) 應用程式，還是針對組織中特定的服務主體，都可以設定權杖存留期。 不過，我們目前不支援設定 [受控識別服務主體](../managed-identities-azure-resources/overview.md)的權杖存留期。

> [!IMPORTANT]
> 在預覽期間收到客戶的意見之後，我們已在 Azure AD 條件式存取中實行 [驗證會話管理功能](../conditional-access/howto-conditional-access-session-lifetime.md) 。 您可以使用這項新功能，藉由設定 [登入頻率] 來設定重新整理權杖存留期。 2020 5 月30日之後，沒有新的租使用者可以使用可設定的權杖存留期原則來設定會話和重新整理權杖。 淘汰將在這之後的幾個月內發生，這表示我們將停止接受現有的會話和重新整理權杖原則。 您仍然可以在淘汰之後設定存取權杖存留期。

在 Azure AD 中，原則物件代表在組織中個別應用程式或所有應用程式上強制執行的一組規則。 每個原則類型都具有包含一組屬性的獨特結構，這些屬性會套用至它們已被指派的物件。

您可以為您的組織指定原則做為預設原則。 只要此原則不被優先順序更高的原則覆寫，就會套用至組織中的任何應用程式。 您也可以將原則指派給特定應用程式。 優先順序會因原則類型而異。

如需範例，請參閱 [如何設定權杖存留期的範例](configure-token-lifetimes.md)。

> [!NOTE]
> 可設定的權杖存留期原則僅適用于存取 SharePoint Online 和商務用 OneDrive 資源的行動和桌面用戶端，且不適用於網頁瀏覽器會話。
> 若要管理 SharePoint Online 和商務用 OneDrive 的網頁瀏覽器會話存留期，請使用 [條件式存取會話存留期](../conditional-access/howto-conditional-access-session-lifetime.md) 功能。 請參閱 [SharePoint Online 部落格](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) \(英文\)，以深入了解如何設定閒置工作階段逾時。

## <a name="token-types"></a>權杖類型

您可以針對重新整理權杖、存取權杖、SAML 權杖、會話權杖及識別碼權杖設定權杖存留期原則。

### <a name="access-tokens"></a>存取權杖

用戶端會使用存取權杖來存取受保護的資源。 存取權杖僅可用於特定使用者、用戶端及資源的組合。 存取權杖是不可撤銷的，在到期之前都會一直有效。 惡意執行者若已取得存取權杖，便可在權杖的存留期範圍內使用該權杖。 調整存取權杖存留期是在改進系統效能與增加用戶端在使用者帳戶停用後保留存取權的時間，這兩者之間所做的一項權衡取捨。 系統效能的改進是藉由減少用戶端需要取得新存取權杖的次數來達成。  預設值是 1 小時 - 1 小時後，用戶端必須使用重新整理權杖 (通常以無訊息模式) 取得新的重新整理權杖和存取權杖。 

### <a name="saml-tokens"></a>SAML 權杖

許多 web 架構 SAAS 應用程式都會使用 SAML 權杖，並使用 Azure Active Directory 的 SAML2 通訊協定端點取得。 使用 WS-同盟的應用程式也會使用它們。 權杖的預設存留期為1小時。 從應用程式的觀點來看，權杖的有效期間是由權杖中元素的 NotOnOrAfter 值所指定 `<conditions …>` 。 在權杖的有效期間結束之後，用戶端必須起始新的驗證要求，這通常會在沒有互動式登入的情況下滿足，因為單一登入 (SSO) 會話權杖的結果。

您可以使用中的參數來變更 NotOnOrAfter 的值 `AccessTokenLifetime` `TokenLifetimePolicy` 。 它會設定為原則中所設定的存留期（如果有的話），加上五分鐘的時鐘誤差因數。

在元素中指定的主旨確認 NotOnOrAfter `<SubjectConfirmationData>` 不會受到權杖存留期設定的影響。 

### <a name="refresh-tokens"></a>重新整理權杖

當用戶端取得存取權杖來存取受保護的資源時，用戶端會也會收到重新整理權杖。 當存取權杖到期時，可以使用重新整理權杖來取得一組新的存取/重新整理權杖。 重新整理權杖會繫結至使用者與用戶端組合。 重新整理權杖可以[隨時撤銷](access-tokens.md#token-revocation)，每次使用權杖時，會檢查權杖的有效性。  重新整理權杖不會在用來擷取新存取權杖時撤銷，但是，最好的做法是在取得新權杖時，安全地刪除舊權杖。 

區別機密用戶端與公開用戶端相當重要，因為這會影響可以使用重新整理權杖的時間長度。 如需不同類型用戶端的詳細資訊，請參閱 [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1)。

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>具有機密用戶端重新整理權杖的權杖存留期
機密用戶端是可以安全地儲存用戶端密碼的應用程式。 它們可以證明要求是來自安全的用戶端應用程式，而不是來自惡意的執行者。 例如，Web 應用程式是機密用戶端，因為它可在 Web 伺服器上儲存用戶端密碼。 它不是公開的。 因為這些流程較安全，所以簽發給這些流程的重新整理權杖預設存留期為 `until-revoked`、無法使用原則來變更，而且將不會在自發性密碼重設中撤銷。

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>具有公開用戶端重新整理權杖的權杖存留期

公開用戶端無法安全地儲存用戶端密碼。 例如，iOS/Android 應用程式無法模糊來自資源擁有者的密碼，因此被視為公開用戶端。 您可以在資源上設定原則，讓來自公開用戶端的重新整理權杖只要超過指定的期間，便無法取得一組新的存取/重新整理權杖。 若要這樣做，請使用「重新整理 [權杖最大閒置時間」屬性](#refresh-token-max-inactive-time) (`MaxInactiveTime`) 。 您也可以使用原則來設定期間，超過該期間就不會再接受重新整理權杖。 若要這樣做，請使用「 [單一要素重新整理權杖最大壽命](#single-factor-session-token-max-age) 」或「 [多重要素會話權杖最大壽命](#multi-factor-refresh-token-max-age) 」屬性。 您可以調整重新整理權杖的存留期，以控制當使用者使用公開用戶端應用程式時，必須在何時及隔多久重新輸入一次認證，而不是以無訊息方式重新驗證。

> [!NOTE]
> 最大壽命屬性是可以使用單一權杖的時間長度。 

### <a name="id-tokens"></a>ID 權杖
識別碼權杖會傳遞至網站與原生用戶端。 識別碼權杖包含使用者的設定檔資訊。 識別碼權杖會繫結至特定的使用者與用戶端組合。 識別碼權杖在到期前都會被視為有效。 通常，Web 應用程式會將應用程式中的使用者工作階段存留期，與針對該使用者簽發之識別碼權杖的存留期做比對。 您可以調整識別碼權杖的存留期，以控制 web 應用程式的應用程式會話過期的頻率，以及要求使用者以無訊息模式或互動方式) 來重新驗證 Microsoft 身分識別平臺 (的頻率。

### <a name="single-sign-on-session-tokens"></a>單一登入工作階段權杖
當使用者使用 Microsoft 身分識別平臺進行驗證時，會使用使用者的瀏覽器和 Microsoft 身分識別平臺來建立單一登入會話 (SSO) 。 SSO 權杖 (採用 Cookie 的形式) 即代表此工作階段。 SSO 會話權杖未系結至特定的資源/用戶端應用程式。 SSO 工作階段權杖是可撤銷的，而每次使用這些權杖時，系統都會檢查其有效性。

Microsoft 身分識別平臺會使用兩種 SSO 會話權杖：持續性和非持續性。 持續性工作階段權杖是由瀏覽器儲存為持續性 Cookie。 非持續性工作階段權杖是儲存為工作階段 Cookie。 當瀏覽器關閉時， (會話 cookie 會終結。 ) 通常會儲存非持續性會話權杖。 但是，當使用者在驗證期間選取 [讓我保持登入]**** 核取方塊時，則會儲存一個持續性工作階段權杖。

非持續性工作階段權杖有 24 小時的存留期。 持續性權杖的存留期為90天。 每當 SSO 會話權杖在其有效期間內使用時，會根據權杖類型，將有效期間延長為24小時或90天。 如果未在 SSO 工作階段權杖的有效期內使用此權杖，系統就會將其視為過期而不再接受它。

您可以使用原則來設定第一個工作階段權杖簽發之後的時間，超出該時間就不會再接受工作階段權杖。  (要執行這項操作，請使用「會話權杖最大壽命」屬性。 ) 您可以調整會話權杖的存留期，以控制當使用者使用 web 應用程式時，需要重新輸入認證，而不是以無訊息方式進行驗證的時間和頻率。

### <a name="token-lifetime-policy-properties"></a>權杖存留期原則屬性
權杖存留期原則是一種包含權杖存留期規則的原則物件。 使用原則的屬性來控制指定的權杖存留期。 如果未設定任何原則，系統就會強制執行預設存留期值。

### <a name="configurable-token-lifetime-properties"></a>可設定的權杖存留期屬性
| 屬性 | 原則屬性字串 | 影響 | 預設 | 最小值 | 最大值 |
| --- | --- | --- | --- | --- | --- |
| 存取權杖存留期 |AccessTokenLifetime<sup>2</sup> |存取權杖、識別碼權杖、SAML2 權杖 |1 小時 |10 分鐘 |1 日 |
| 重新整理權杖最大閒置時間 |MaxInactiveTime |重新整理權杖 |90 天 |10 分鐘 |90 天 |
| 單一要素重新整理權杖最大壽命 |MaxAgeSingleFactor |重新整理權杖 (適用於任何使用者) |直到撤銷為止 |10 分鐘 |直到撤銷為止<sup>1</sup> |
| 多重要素重新整理權杖最大壽命 |MaxAgeMultiFactor |重新整理權杖 (適用於任何使用者) | 180 天 |10 分鐘 |直到撤銷為止<sup>1</sup> |
| 單一要素工作階段權杖最大壽命 |MaxAgeSessionSingleFactor |工作階段權杖 (持續性和非持續性) |直到撤銷為止 |10 分鐘 |直到撤銷為止<sup>1</sup> |
| 多重要素工作階段權杖最大壽命 |MaxAgeSessionMultiFactor |工作階段權杖 (持續性和非持續性) | 180 天 |10 分鐘 |直到撤銷為止<sup>1</sup> |

* <sup>1</sup>針對這些屬性，可設定的明確時間長度上限為 365 天。
* <sup>2</sup>若要確保 Microsoft 小組 Web 用戶端可以運作，建議您讓 Microsoft 小組保持 AccessTokenLifetime 超過15分鐘。

### <a name="exceptions"></a>例外狀況
| 屬性 | 影響 | 預設 |
| --- | --- | --- |
| 重新整理權杖最大壽命 (針對沒有足夠撤銷資訊的同盟使用者簽發<sup>1</sup>) |重新整理權杖 (針對沒有足夠撤銷資訊的同盟使用者簽發<sup>1</sup>) |12 小時 |
| 重新整理權杖最大閒置時間 (針對機密用戶端簽發) |重新整理權杖 (針對機密用戶端簽發) |90 天 |
| 重新整理權杖最大壽命 (針對機密用戶端簽發) |重新整理權杖 (針對機密用戶端簽發) |直到撤銷為止 |

* <sup>1</sup> 個沒有足夠撤銷資訊的同盟使用者，包括沒有同步 "LastPasswordChangeTimestamp" 屬性的使用者。 這些使用者會獲得這個短暫的最大存留期，因為 Azure Active Directory 無法驗證何時要撤銷系結至舊認證的權杖 (例如已變更) 的密碼，而且必須更頻繁地回來檢查，以確保使用者和相關聯的權杖仍處於良好的地位。 若要改善此體驗，租使用者管理員必須確定它們正在同步 "LastPasswordChangeTimestamp" 屬性 (您可以使用 PowerShell 或透過 AADSync) ，在使用者物件上設定此屬性。

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
> 在下午12:00 時，使用者會啟動新的瀏覽器會話並嘗試存取 Web 應用程式 A。系統會將使用者重新導向至 Microsoft 身分識別平臺，並要求您登入。 這會在瀏覽器中建立一個帶有工作階段權杖的 Cookie。 系統會將使用者重新導向回 Web 應用程式 A，其中會提供一個可讓使用者存取該應用程式的識別碼權杖。
>
> 在下午12:15 時，使用者嘗試存取 Web 應用程式 B。瀏覽器會重新導向至 Microsoft 身分識別平臺，以偵測會話 cookie。 Web 應用程式 B 的服務主體會與權杖存留期原則 2 連結，但同時也是帶有預設權杖存留期原則 1 之父組織的一部分。 權杖存留期原則 2 會生效，因為與服務主體連結之原則的優先順序高於組織預設原則。 工作階段權杖原先是在過去 30 分鐘內簽發的，因此被視為有效。 系統會將使用者重新導向回 Web 應用程式 B，其中會提供一個授與使用者存取權的識別碼權杖。
>
> 在下午1:00 時，使用者嘗試存取 Web 應用程式 A。使用者會被重新導向至 Microsoft 身分識別平臺。 Web 應用程式 A 並未與任何原則連結，但由於它位於帶有預設權杖存留期原則 1 的組織中，因此該原則會生效。 偵測到原先在過去八小時內簽發的工作階段 Cookie。 系統會以無訊息模式將使用者重新導向回具有新識別碼權杖的 Web 應用程式 A。 使用者不需要驗證。
>
> 之後，使用者會立即嘗試存取 Web 應用程式 B。使用者會被重新導向至 Microsoft 身分識別平臺。 與先前一樣，權杖存留期原則 2 會生效。 因為權杖已簽發 30 分鐘以上，系統會提示使用者重新輸入其登入認證。 會簽發全新的工作階段權杖和識別碼權杖。 接著，使用者便可存取 Web 應用程式 B。
>
>

## <a name="configurable-policy-property-details"></a>可設定的原則屬性詳細資料
### <a name="access-token-lifetime"></a>存取權杖存留期
**字串：** AccessTokenLifetime

**影響：** 存取權杖、識別碼權杖、SAML 權杖

**摘要：** 此原則可控制將此資源的存取權杖和識別碼權杖視為有效的期限。 減少存取權杖存留期屬性可減輕存取權杖或識別碼權杖被惡意執行者長時間使用的風險。  (無法撤銷這些權杖。 ) 取捨是效能受到負面影響，因為權杖必須較常被取代。

如需範例，請參閱 [建立 web 登入的原則](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)。

### <a name="refresh-token-max-inactive-time"></a>重新整理權杖最大閒置時間
**字串︰** MaxInactiveTime

**影響：** 重新整理權杖

**摘要：** 此原則可控制在簽發重新整理權杖多久之後，用戶端才不能在嘗試存取此資源時，再使用該權杖來擷取一組新的存取權杖/重新整理權杖。 因為使用重新整理權杖時通常會傳回一個新的重新整理權杖，因此，如果用戶端在指定時間期間使用目前重新整理權杖，嘗試存取任何資源，這個原則會阻止存取。

此原則會強制尚未在其用戶端上變成作用中的使用者必須重新驗證，才能擷取新的重新整理權杖。

「重新整理權杖最大閒置時間」屬性必須設定成低於「單一要素權杖最大壽命」和「多重要素重新整理權杖最大壽命」屬性的值。

如需範例，請參閱為 [呼叫 WEB API 的原生應用程式建立原則](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)。

### <a name="single-factor-refresh-token-max-age"></a>單一要素重新整理權杖最大壽命
**字串︰** MaxAgeSingleFactor

**影響：** 重新整理權杖

**摘要︰** 此原則可控制使用者在上次僅以單一要素成功驗證之後，可以持續多久使用重新整理權杖來取得一組新的存取/重新整理權杖。 使用者驗證並接收新的重新整理權杖之後，使用者可以使用重新整理權杖流程一段指定的時間。  (如果目前的重新整理權杖未撤銷，且未使用的時間超過非使用中的時間，則為 true。 ) 屆時，則會強制使用者重新驗證，以接收新的重新整理權杖。

縮短最大壽命將會強制使用者更頻繁地進行驗證。 因為單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此屬性設定為等於或小於「多重要素重新整理權杖最大壽命」屬性的值。

如需範例，請參閱為 [呼叫 WEB API 的原生應用程式建立原則](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)。

### <a name="multi-factor-refresh-token-max-age"></a>多重要素重新整理權杖最大壽命
**字串︰** MaxAgeMultiFactor

**影響：** 重新整理權杖

**摘要︰** 此原則可控制使用者在上次使用多重要素成功驗證之後，可以持續多久使用重新整理權杖來取得一組新的存取/重新整理權杖。 使用者驗證並接收新的重新整理權杖之後，使用者可以使用重新整理權杖流程一段指定的時間。  (如果目前的重新整理權杖未撤銷，且未使用的時間超過非使用中的時間，則為 true。 ) 屆時，系統會強制使用者重新驗證，以接收新的重新整理權杖。

縮短最大壽命將會強制使用者更頻繁地進行驗證。 因為單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此屬性設定為等於或大於「單一要素重新整理權杖最大壽命」屬性的值。

如需範例，請參閱為 [呼叫 WEB API 的原生應用程式建立原則](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)。

### <a name="single-factor-session-token-max-age"></a>單一要素工作階段權杖最大壽命
**字串︰** MaxAgeSessionSingleFactor

**影響：** 工作階段權杖 (持續性和非持續性)

**摘要︰** 此原則可控制使用者在上次僅以單一要素成功驗證之後，可以持續多久使用工作階段權杖來取得新的識別碼和工作階段權杖。 使用者驗證並接收新的工作階段權杖之後，使用者可以使用工作階段權杖流程一段指定的時間。  (如果目前的會話權杖未撤銷，也未過期，則為 true。在指定的時間內 ) ，會強制使用者重新驗證以接收新的會話權杖。

縮短最大壽命將會強制使用者更頻繁地進行驗證。 因為單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此屬性設定為等於或小於「多重要素工作階段權杖最大壽命」屬性的值。

如需範例，請參閱 [建立 web 登入的原則](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)。

### <a name="multi-factor-session-token-max-age"></a>多重要素工作階段權杖最大壽命
**字串︰** MaxAgeSessionMultiFactor

**影響：** 工作階段權杖 (持續性和非持續性)

**摘要︰** 此原則可控制使用者在上次以多重要素成功驗證之後，可以持續多久使用工作階段權杖來取得新的識別碼和工作階段權杖。 使用者驗證並接收新的工作階段權杖之後，使用者可以使用工作階段權杖流程一段指定的時間。  (如果目前的會話權杖未撤銷，也未過期，則為 true。在指定的時間內 ) ，會強制使用者重新驗證以接收新的會話權杖。

縮短最大壽命將會強制使用者更頻繁地進行驗證。 因為單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此屬性設定為等於或大於「單一要素工作階段權杖最大壽命」屬性的值。

## <a name="cmdlet-reference"></a>Cmdlet 參考

這些是 [Azure Active Directory PowerShell For Graph Preview 模組](/powershell/module/azuread/?view=azureadps-2.0-preview#service-principals&preserve-view=true&preserve-view=true)中的 Cmdlet。

### <a name="manage-policies"></a>管理原則

您可以使用下列 Cmdlet 來管理原則。

| Cmdlet | 描述 | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 建立新的原則。 |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 取得所有 Azure AD 原則或指定的原則。 |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | 取得與原則連結的所有應用程式和服務主體。 |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 更新現有的原則。 |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 刪除指定的原則。 |

### <a name="application-policies"></a>應用程式原則
您可以針對應用程式原則使用下列 Cmdlet。</br></br>

| Cmdlet | 描述 | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 將指定的原則連結至應用程式。 |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 取得指派給應用程式的原則。 |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 從應用程式移除原則。 |

### <a name="service-principal-policies"></a>服務主體原則
您可以針對服務主體原則使用下列 Cmdlet。

| Cmdlet | 描述 | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 將指定的原則連結至服務主體。 |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 取得與指定的服務主體連結的任何原則。|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 從指定的服務主體移除原則。|

## <a name="license-requirements"></a>授權需求

使用此方法需要 Azure AD Premium P1 授權。 若要尋找您需求的正確授權，請參閱 [比較免費和 Premium 版本的正式推出功能](https://azure.microsoft.com/pricing/details/active-directory/)。

擁有 [Microsoft 365 商務版授權](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)的客戶也有條件式存取功能的存取權。

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱 [如何設定權杖存留期的範例](configure-token-lifetimes.md)。