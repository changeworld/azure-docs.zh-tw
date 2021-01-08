---
title: 自訂應用程式 SAML 權杖宣告
titleSuffix: Microsoft identity platform
description: 瞭解如何在企業應用程式的 SAML 權杖中自訂 Microsoft 身分識別平臺所發出的宣告。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 9fb5e229882532fed076f2e0d800f32acfcdbf4c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013782"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>如何：針對企業應用程式自訂 SAML 權杖中發出的宣告

現今，Microsoft 身分識別平臺支援單一登入 (SSO) 與大部分的企業應用程式，包括 Azure AD 應用程式資源庫中預先整合的應用程式，以及自訂應用程式。 當使用者透過 Microsoft 身分識別平臺使用 SAML 2.0 通訊協定驗證應用程式時，Microsoft 身分識別平臺會透過 HTTP POST) ，將權杖傳送給應用程式 (。 然後，應用程式會驗證並使用權杖將使用者登入，而不會提示輸入使用者名稱和密碼。 這些 SAML 權杖包含關於使用者的資訊片段，稱為「宣告」。

*宣告* 是身分識別提供者核發權杖給使用者時，所提供的權杖內部使用者相關資訊。 在 [SAML 權杖](https://en.wikipedia.org/wiki/SAML_2.0)中，此資料通常包含在 SAML 屬性陳述式中。 使用者的唯一識別碼通常在 SAML Subject 中表示，也稱為「名稱識別碼」。

根據預設，Microsoft 身分識別平臺會將 SAML 權杖簽發給您的應用程式，其中包含 `NameIdentifier` 具有使用者使用者名稱值的宣告 (也稱為 Azure AD 中的使用者主體名稱) ，可唯一識別使用者。 SAML 權杖也包含額外的宣告，其包含使用者的電子郵件地址、名字和姓氏。

若要檢視或編輯在 SAML 權杖中對應用程式發出的宣告，請在 Azure 入口網站中開啟應用程式。 然後開啟 [使用者屬性與宣告] 區段。

![在 Azure 入口網站中開啟 [使用者屬性與宣告] 區段](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

編輯在 SAML 權杖中簽發的宣告有兩個可能原因：

* 應用程式要求 `NameIdentifier` 或 NameID 宣告必須是儲存在 Azure AD 中之使用者名稱 (或使用者主體名稱 ) 以外的項目。
* 應用程式是設計為要求不同的宣告 URI 組或宣告值。

## <a name="editing-nameid"></a>編輯 nameID

若要編輯 NameID (名稱識別碼值)：

1. 開啟 [名稱識別碼值] 頁面。
1. 選取您想要套用到該屬性的屬性或轉換。 您也可以選擇性地針對 NameID 宣告指定所需的格式。

   ![編輯 NameID (名稱識別碼) 值](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID 格式

如果 SAML 要求包含具有特定格式的專案 NameIDPolicy，則 Microsoft 身分識別平臺會接受要求中的格式。

如果 SAML 要求未包含 NameIDPolicy 的元素，Microsoft 身分識別平臺將會使用您指定的格式來發出 NameID。 如果未指定任何格式，Microsoft 身分識別平臺將會使用與所選宣告來源相關聯的預設來源格式。

從 [選擇名稱識別碼格式] 下拉式清單中，您可以選取下列其中一個選項。

| NameID 格式 | 描述 |
|---------------|-------------|
| **預設值** | Microsoft 身分識別平臺會使用預設來源格式。 |
| **持續** | Microsoft 身分識別平臺將會使用持續性作為 NameID 格式。 |
| **EmailAddress** | Microsoft 身分識別平臺會使用 EmailAddress 作為 NameID 格式。 |
| **未指定** | Microsoft 身分識別平臺將使用未指定的做為 NameID 格式。 |

也支援暫時性 NameID，但其並未在下拉式清單中提供，也無法在 Azure 端設定。 若要深入了解 NameIDPolicy 屬性，請參閱[單一登入 SAML 通訊協定](single-sign-on-saml-protocol.md)。

### <a name="attributes"></a>屬性

選取 `NameIdentifier` (或 NameID) 宣告的所需來源。 您可以從下列選項選取。

| 名稱 | 描述 |
|------|-------------|
| 電子郵件 | 使用者的電子郵件地址 |
| userprincipalName | 使用者的使用者主體名稱 (UPN) |
| onpremisessamaccount | 已從內部部署 Azure AD 同步處理的 SAM 帳戶名稱 |
| objectid | Azure AD 中使用者的 objectid |
| employeeid | 使用者的員工識別碼 |
| 目錄擴充 | 目錄擴充[從使用 Azure AD Connect 同步的內部部署 Active Directory 同步處理](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| 擴充屬性 1-15 | 內部部署擴充屬性，用來擴充 Azure AD 結構描述 |

如需詳細資訊，請參閱[表 3：每個來源的有效識別碼值](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)。

您也可以將任何常數 (靜態) 值指派給您在 Azure AD 中定義的任何宣告。 請遵循下列步驟來指派常數值：

1. 在 [ <a href="https://portal.azure.com/" target="_blank">Azure 入口網站 <span class="docon docon-navigate-external x-hidden-focus"></span></a>] 的 [**使用者屬性] & [宣告**] 區段上，按一下 [**編輯**] 圖示以編輯宣告。

1. 按一下您想要修改的必要宣告。

1. 依據您的組織，在 [來源屬性] 中輸入不含引號的常數值，然後按一下 [儲存]。

    ![Azure 入口網站中 & 宣告區段的組織屬性](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. 常數值會以下列方式顯示。

    ![在 Azure 入口網站中編輯 & 宣告區段的屬性](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>特殊宣告 - 轉換

您也可以使用宣告轉換函式。

| 函式 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 從電子郵件地址或使用者主體名稱中移除網域尾碼。 這只會擷取使用者名稱的第一個部分 (例如，"joe_smith" 而不是 joe_smith@contoso.com)。 |
| **Join()** | 加入具有已驗證網域的屬性。 如果選取的使用者識別碼值具有網域，它會擷取使用者名稱以附加所選的已驗證網域。 例如，如果您選取電子郵件 (joe_smith@contoso.com) 作為使用者識別碼值，並選取 contoso.onmicrosoft.com 作為已驗證的網域，這樣會產生 joe_smith@contoso.onmicrosoft.com。 |
| **ToLower()** | 將所選取屬性中的字元轉換成小寫字元。 |
| **ToUpper()** | 將所選取屬性中的字元轉換成大寫字元。 |

## <a name="adding-application-specific-claims"></a>新增應用程式特定宣告

若要新增應用程式特定宣告：

1. 在 [使用者屬性與宣告] 中，選取 [新增宣告] 以開啟 [管理使用者宣告] 窗格。
1. 輸入宣告的 [名稱]。 根據 SAML 規格，該值並不一定需要遵循 URI 模式。如果您需要 URI 模式，請將其置於 [命名空間] 欄位。
1. 選取宣告要擷取其值的 [來源]。 您可以從來源屬性下拉式清單選取使用者屬性，或在將使用者屬性發出為宣告之前對其套用轉換。

### <a name="claim-transformations"></a>宣告轉換

若要將轉換套用到使用者屬性：

1. 在 [管理宣告] 中，選取 [轉換] 作為宣告來源以開啟 [管理轉換] 頁面。
2. 從轉換下拉式清單選取函式。 視您所選取的函式而定，您將必須提供參數與常數值以在轉換中進行評估。 請參閱下表以取得可用函式的詳細資訊。
3. 若要套用多個轉換，請按一下 [新增轉換]。您最多可以針對宣告套用兩個轉換。 例如，您可以先擷取 `user.mail` 的電子郵件前置詞。 接著將字串設定為大寫。

   ![多個宣告轉換](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

您可以使用下列函式來轉換宣告。

| 函式 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 從電子郵件地址或使用者主體名稱中移除網域尾碼。 這只會擷取使用者名稱的第一個部分 (例如，"joe_smith" 而不是 joe_smith@contoso.com)。 |
| **Join()** | 透過聯結兩個屬性來建立新值。 您也可以選擇性地在兩個屬性之間使用分隔符號。 針對 NameID 宣告轉換，聯結僅限用於已驗證的網域。 如果選取的使用者識別碼值具有網域，它會擷取使用者名稱以附加所選的已驗證網域。 例如，如果您選取電子郵件 (joe_smith@contoso.com) 作為使用者識別碼值，並選取 contoso.onmicrosoft.com 作為已驗證的網域，這樣會產生 joe_smith@contoso.onmicrosoft.com。 |
| **ToLowercase ( # B1** | 將所選取屬性中的字元轉換成小寫字元。 |
| **ToUppercase ( # B1** | 將所選取屬性中的字元轉換成大寫字元。 |
| **Contains()** | 如果輸入符合指定的值，則輸出屬性或常數。 否則，您可以在沒有相符結果時指定另一個輸出。<br/>例如，假設您想要發出宣告，其中如果值包含網域 “@contoso.com”，便代表其為使用者的電子郵件地址，否則您便想要輸出使用者主體名稱。 若要這麼做，您必須設定下列值：<br/>參數 1 (輸入)：user.email<br/>值："@contoso.com"<br/>參數 2 (輸出)：user.email<br/>參數 3 (在沒有相符項目下的輸出)：user.userprincipalname |
| **EndWith()** | 如果輸入的結尾是指定的值，則輸出屬性或常數。 否則，您可以在沒有相符結果時指定另一個輸出。<br/>例如，假設您想要發出宣告，其中如果員工識別碼是以 “000” 作為結尾，便代表值為使用者的員工識別碼，否則您便想要輸出分機屬性。 若要這麼做，您必須設定下列值：<br/>參數 1 (輸入)：user.employeeid<br/>*值*："000"<br/>參數 2 (輸出)：user.employeeid<br/>參數 3 (在沒有相符項目下的輸出)：user.extensionattribute1 |
| **StartWith()** | 如果輸入的開頭是指定的值，則輸出屬性或常數。 否則，您可以在沒有相符結果時指定另一個輸出。<br/>例如，假設您想要發出宣告，其中如果國家/地區是以 "US" 作為開頭，便代表值為使用者的員工識別碼，否則您便想要輸出分機屬性。 若要這麼做，您必須設定下列值：<br/>參數 1 (輸入)：user.country<br/>*值*："US"<br/>參數 2 (輸出)：user.employeeid<br/>參數 3 (在沒有相符項目下的輸出)：user.extensionattribute1 |
| **Extract() - 比對之後** | 在子字串符合指定值之後加以傳回。<br/>例如，假設輸入的值是 "Finance_BSimon"，相符的值是 "Finance_"，則宣告的輸出便是 "BSimon"。 |
| **Extract() - 比對之前** | 傳回子字串，直到其符合指定值為止。<br/>例如，假設輸入的值是 "BSimon_US"，相符的值是 "_US"，則宣告的輸出便是 "BSimon"。 |
| **Extract() - 比對之間** | 傳回子字串，直到其符合指定值為止。<br/>例如，如果輸入的值為 "Finance_BSimon_US"，則第一個相符的值為 "財務 \_ "，第二個相符值為 " \_ US"，則宣告的輸出為 ">bsimon"。 |
| **ExtractAlpha() - 前置詞** | 傳回字串的前置詞字母部分。<br/>例如，如果輸入的值是 "BSimon_123"，其便會傳回 "BSimon"。 |
| **ExtractAlpha() - 後置詞** | 傳回字串的後置詞字母部分。<br/>例如，如果輸入的值是 "123_Simon"，其便會傳回 "Simon"。 |
| **ExtractNumeric() - 前置詞** | 傳回字串的前置詞數值部分。<br/>例如，如果輸入的值是 "123_BSimon"，其便會傳回 "123"。 |
| **ExtractNumeric() - 後置詞** | 傳回字串的後置詞數值部分。<br/>例如，如果輸入的值是 "BSimon_123"，其便會傳回 "123"。 |
| **IfEmpty()** | 如果輸入為 Null 或空白，則輸出屬性或常數。<br/>例如，假設您想要在指定使用者的員工識別碼為空白時，輸出儲存在 extensionattribute 中的屬性。 若要這麼做，您必須設定下列值：<br/>參數 1 (輸入)：user.employeeid<br/>參數 2 (輸出)：user.extensionattribute1<br/>參數 3 (在沒有相符項目下的輸出)：user.employeeid |
| **IfNotEmpty()** | 如果輸入不是 Null 或空白，則輸出屬性或常數。<br/>例如，假設您想要在指定使用者的員工識別碼不是空白時，輸出儲存在 extensionattribute 中的屬性。 若要這麼做，您必須設定下列值：<br/>參數 1 (輸入)：user.employeeid<br/>參數 2 (輸出)：user.extensionattribute1 |

如果您需要額外的轉換，請將您的想法提交到 [Azure AD 中意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) \(英文\) 的 [SaaS Applications] \(SaaS 應用程式\) 類別底下。

## <a name="emitting-claims-based-on-conditions"></a>根據條件發出宣告

您可以根據使用者類型和使用者所屬的群組來指定宣告的來源。 

使用者類型可以是：
- **任何**：所有使用者都可以存取應用程式。
- **成員**：租用戶的原生成員
- **所有來賓**：使用者來自具有或沒有 Azure AD 的外部組織。
- **AAD 來賓**：來賓使用者屬於使用 Azure AD 的另一個組織。
- **外部來賓**：來賓使用者屬於沒有 Azure AD 的外部組織。


這會很有用的其中一個案例，便是當宣告的來源對於存取應用程式的來賓和員工而言是不一樣的時候。 您可以指定如果使用者是員工，便將 NameID 的來源設定為 user.email，但如果使用者是來賓，便將 NameID 的來源設定為 user.extensionattribute1。

若要新增宣告條件：

1. 在 [管理宣告] 中，展開 [宣告條件]。
2. 選取使用者類型。
3. 選取該使用者應隸屬的群組。 您可以針對指定的應用程式，在所有宣告中選取最多50個唯一的群組。 
4. 選取宣告要擷取其值的 [來源]。 您可以從來源屬性下拉式清單選取使用者屬性，或在將使用者屬性發出為宣告之前對其套用轉換。

您新增條件的順序很重要。 Azure AD 會從上到下評估條件，以決定要在宣告中發出哪一個值。 符合運算式的最後一個值將會在宣告中發出。

例如，Britta Simon 是 Contoso 租用戶中的來賓使用者。 她隸屬同樣也使用 Azure AD 的另一個組織。 基於 Fabrikam 應用程式的下列設定，當 Britta 嘗試登入 Fabrikam 時，Microsoft 身分識別平臺將會依下列方式評估條件。

首先，Microsoft 身分識別平臺會驗證 Britta 的使用者類型是否為 `All guests` 。 因為這是如此，所以 Microsoft 身分識別平臺會將宣告的來源指派給 `user.extensionattribute1` 。 其次，Microsoft 身分識別平臺會驗證 Britta 的使用者類型是否為 `AAD guests` ，因為這也是如此，所以 microsoft 身分識別平臺會將宣告的來源指派給 `user.mail` 。 最後，系統會針對 Britta 使用 `user.mail` 值發出宣告。

![宣告條件式設定](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>後續步驟

* [Azure AD 中的應用程式管理](../manage-apps/what-is-application-management.md)
* [對不存在於 Azure AD 應用程式庫的應用程式設定單一登入](../manage-apps/configure-saml-single-sign-on.md)
* [針對 SAML 型單一登入進行疑難排解](../manage-apps/debug-saml-sso-issues.md)
