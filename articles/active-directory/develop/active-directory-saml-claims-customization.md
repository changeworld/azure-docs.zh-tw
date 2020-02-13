---
title: 自訂 Azure AD 應用程式 SAML 權杖宣告
titleSuffix: Microsoft identity platform
description: 了解如何針對 Azure AD 中的企業應用程式，自訂 SAML 權杖中發出的宣告
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 52779b7ffea0f33676426f145a700c7181cf0bf1
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161251"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>如何：針對企業應用程式自訂 SAML 權杖中發出的宣告

現今，Azure Active Directory （Azure AD）支援大部分企業應用程式的單一登入（SSO），包括 Azure AD 應用程式資源庫中預先整合的應用程式，以及自訂應用程式。 當使用者利用 SAML 2.0 通訊協定來透過 Azure AD 向應用程式驗證時，Azure AD 會將權杖傳送給應用程式 (透過 HTTP POST)。 然後，應用程式會驗證並使用權杖將使用者登入，而不會提示輸入使用者名稱和密碼。 這些 SAML 權杖包含使用者的相關資訊，稱為*宣告*。

*宣告*是身分識別提供者核發權杖給使用者時，所提供的權杖內部使用者相關資訊。 在 [SAML 權杖](https://en.wikipedia.org/wiki/SAML_2.0)中，此資料通常包含在 SAML 屬性陳述式中。 使用者的唯一識別碼通常在 SAML Subject 中表示，也稱為「名稱識別碼」。

根據預設，Azure AD 會將 SAML 權杖發行至您的應用程式，其中包含的 `NameIdentifier` 宣告具有使用者在 Azure AD 中的使用者名稱（也稱為使用者主體名稱）的值，可唯一識別使用者。 SAML 權杖也包含額外的宣告，其包含使用者的電子郵件地址、名字和姓氏。

若要檢視或編輯在 SAML 權杖中對應用程式發出的宣告，請在 Azure 入口網站中開啟應用程式。 然後，& 宣告 區段中開啟 **使用者屬性**。

![在 Azure 入口網站中開啟 [使用者屬性 & 宣告] 區段](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

編輯在 SAML 權杖中簽發的宣告有兩個可能原因：

* 應用程式要求 `NameIdentifier` 或 NameID 宣告必須是儲存在 Azure AD 中的使用者名稱（或使用者主體名稱）以外的專案。
* 應用程式是設計為要求不同的宣告 URI 組或宣告值。

## <a name="editing-nameid"></a>編輯 nameID

若要編輯 NameID （名稱識別碼值）：

1. 開啟 [**名稱識別碼值**] 頁面。
1. 選取您想要套用至屬性的屬性或轉換。 （選擇性）您可以指定要讓 NameID 宣告具有的格式。

   ![編輯 NameID （名稱識別碼）值](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID 格式

如果 SAML 要求包含以特定格式 NameIDPolicy 的元素，則 Azure AD 會接受要求中的格式。

如果 SAML 要求未包含 NameIDPolicy 的元素，則 Azure AD 會以您指定的格式發出 NameID。 如果未指定格式，Azure AD 將會使用與所選宣告來源相關聯的預設來源格式。

從 [**選擇名稱識別碼格式**] 下拉式清單中，您可以選取下列其中一個選項。

| NameID 格式 | 描述 |
|---------------|-------------|
| **預設值** | Azure AD 將會使用預設的來源格式。 |
| **持續** | Azure AD 將使用持續性作為 NameID 格式。 |
| **EmailAddress** | Azure AD 會使用 EmailAddress 作為 NameID 格式。 |
| **識別** | Azure AD 將使用 [未指定] 做為 NameID 格式。 |
| **Windows 網域限定名稱** | Azure AD 會使用 WindowsDomainQualifiedName 作為 NameID 格式。 |

此外也支援暫時性 NameID，但無法在下拉式清單中進行設定，也無法在 Azure 端進行設定。 若要深入瞭解 NameIDPolicy 屬性，請參閱[單一登入 SAML 通訊協定](single-sign-on-saml-protocol.md)。

### <a name="attributes"></a>屬性

選取 `NameIdentifier` (或 NameID) 宣告的所需來源。 您可以從下列選項選取。

| 名稱 | 描述 |
|------|-------------|
| 電子郵件 | 使用者的電子郵件地址 |
| userprincipalName | 使用者的使用者主體名稱（UPN） |
| onpremisessamaccount | 已從內部部署 Azure AD 同步處理的 SAM 帳戶名稱 |
| objectid | 使用者在 Azure AD 中的 Objectid |
| employeeid | 使用者的員工識別碼 |
| 目錄擴充 | 目錄擴充[從使用 Azure AD Connect 同步的內部部署 Active Directory 同步處理](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| 擴充屬性 1-15 | 內部部署擴充屬性，用來擴充 Azure AD 結構描述 |

如需詳細資訊，請參閱[表3：每個來源的有效識別碼值](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)。

您也可以將任何常數（靜態）值指派給您在 Azure AD 中定義的任何宣告。 請遵循下列步驟來指派常數值：

1. 在  [Azure 入口網站](https://portal.azure.com/)的 **使用者屬性 & 宣告** 區段上，按一下 [**編輯**] 圖示來編輯宣告。

1. 按一下您要修改的必要宣告。

1. 根據您的組織，在**Source 屬性**中輸入不含引號的常數值，然後按一下 [**儲存**]。

    ![在 Azure 入口網站中開啟 [使用者屬性 & 宣告] 區段](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. 常數值會顯示如下。

    ![在 Azure 入口網站中開啟 [使用者屬性 & 宣告] 區段](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>特殊宣告-轉換

您也可以使用宣告轉換函式。

| 函式 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 從電子郵件地址或使用者主體名稱中移除網域尾碼。 這只會擷取使用者名稱的第一個部分 (例如，"joe_smith" 而不是 joe_smith@contoso.com)。 |
| **Join （）** | 加入具有已驗證網域的屬性。 如果選取的使用者識別碼值具有網域，它會擷取使用者名稱以附加所選的已驗證網域。 例如，如果您選取電子郵件 (joe_smith@contoso.com) 作為使用者識別碼值，並選取 contoso.onmicrosoft.com 作為已驗證的網域，這樣會產生 joe_smith@contoso.onmicrosoft.com。 |
| **ToLower()** | 將所選取屬性中的字元轉換成小寫字元。 |
| **ToUpper()** | 將所選取屬性中的字元轉換成大寫字元。 |

## <a name="adding-application-specific-claims"></a>新增應用程式特定的宣告

若要新增應用程式特定的宣告：

1. 在 [**使用者屬性 & 宣告**] 中，選取 [**加入新**宣告] 以開啟 [**管理使用者宣告**] 頁面。
1. 輸入宣告的**名稱**。 根據 SAML 規格，此值不一定需要遵循 URI 模式。如果您需要 URI 模式，可以將它放在 [**命名空間**] 欄位中。
1. 選取宣告要取得其值的**來源**。 您可以從 [來源屬性] 下拉式清單中選取使用者屬性，或將轉換套用至使用者屬性，再將其發出為宣告。

### <a name="claim-transformations"></a>宣告轉換

若要將轉換套用至使用者屬性：

1. 在 [**管理**宣告] 中，選取 [*轉換*為宣告來源] 以開啟 [**管理轉換**] 頁面。
2. 從 [轉換] 下拉式清單中選取函數。 視選取的函式而定，您必須提供參數和常數值以在轉換中進行評估。 如需可用功能的詳細資訊，請參閱下表。
3. 若要套用多個轉換，請按一下 [**加入轉換**]。您最多可以對一個宣告套用兩個轉換。 例如，您可以先解壓縮 `user.mail`的電子郵件首碼。 然後，將字串設為大寫。

   ![編輯 NameID （名稱識別碼）值](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

您可以使用下列函數來轉換宣告。

| 函式 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 從電子郵件地址或使用者主體名稱中移除網域尾碼。 這只會擷取使用者名稱的第一個部分 (例如，"joe_smith" 而不是 joe_smith@contoso.com)。 |
| **Join （）** | 藉由聯結兩個屬性來建立新的值。 （選擇性）您可以在這兩個屬性之間使用分隔符號。 針對 NameID 宣告轉換，聯結僅限於已驗證的網域。 如果選取的使用者識別碼值具有網域，它會擷取使用者名稱以附加所選的已驗證網域。 例如，如果您選取電子郵件 (joe_smith@contoso.com) 作為使用者識別碼值，並選取 contoso.onmicrosoft.com 作為已驗證的網域，這樣會產生 joe_smith@contoso.onmicrosoft.com。 |
| **ToLower()** | 將所選取屬性中的字元轉換成小寫字元。 |
| **ToUpper()** | 將所選取屬性中的字元轉換成大寫字元。 |
| **Contains （）** | 如果輸入符合指定的值，則輸出屬性或常數。 否則，如果沒有符合的結果，您可以指定另一個輸出。<br/>例如，如果您想要發出宣告，其中的值為使用者的電子郵件地址（如果它包含網域 "@contoso.com"），則為，否則您會想要輸出使用者主體名稱。 若要這樣做，您可以設定下列值：<br/>*參數1（輸入）* ：使用者。電子郵件<br/>*值*： "@contoso.com"<br/>參數2（輸出）：使用者電子郵件<br/>參數3（如果沒有符合的輸出）： user. userprincipalname |
| **EndWith()** | 如果輸入的結尾是指定的值，則會輸出屬性或常數。 否則，如果沒有符合的結果，您可以指定另一個輸出。<br/>例如，如果您想要發出宣告，其中的員工識別碼結尾為 "000"，則此值為使用者的員工識別碼，否則您會想要輸出擴充屬性。 若要這樣做，您可以設定下列值：<br/>*參數1（輸入）* ：使用者. 員工。<br/>*值*： "000"<br/>參數2（輸出）：使用者. 員工。<br/>參數3（如果沒有符合的輸出）： user. extensionattribute1 |
| **與 startwith （）** | 如果輸入的開頭為指定的值，則會輸出屬性或常數。 否則，如果沒有符合的結果，您可以指定另一個輸出。<br/>例如，如果您想要發出宣告，其中的值為使用者的員工識別碼（如果國家/地區開頭為 "US"），否則您會想要輸出擴充屬性。 若要這樣做，您可以設定下列值：<br/>*參數1（輸入）* ：使用者 country<br/>*值*： "US"<br/>參數2（輸出）：使用者. 員工。<br/>參數3（如果沒有符合的輸出）： user. extensionattribute1 |
| **解壓縮（）-比對之後** | 傳回符合指定值的子字串。<br/>例如，如果輸入的值為 "Finance_BSimon"，則相符的值為 "Finance_"，而宣告的輸出為 "BSimon"。 |
| **解壓縮（）-比對之前** | 傳回子字串，直到它符合指定的值為止。<br/>例如，如果輸入的值為 "BSimon_US"，則相符的值為 "_US"，而宣告的輸出為 "BSimon"。 |
| **解壓縮（）-比對之間** | 傳回子字串，直到它符合指定的值為止。<br/>例如，如果輸入的值是 "Finance_BSimon_US"，第一個相符的值是 "Finance_"，第二個符合的值是 "_US"，則宣告的輸出會是 "BSimon"。 |
| **ExtractAlpha （）-Prefix** | 傳回字串的前置詞字母部分。<br/>例如，如果輸入的值為 "BSimon_123"，則會傳回 "BSimon"。 |
| **ExtractAlpha （）-尾碼** | 傳回字串的後置詞字母部分。<br/>例如，如果輸入的值為 "123_Simon"，則會傳回 "Simon"。 |
| **ExtractNumeric （）-Prefix** | 傳回字串的前置數位部分。<br/>例如，如果輸入的值為 "123_BSimon"，則會傳回 "123"。 |
| **ExtractNumeric （）-尾碼** | 傳回字串的尾碼數值部分。<br/>例如，如果輸入的值為 "BSimon_123"，則會傳回 "123"。 |
| **Defaultifempty （）** | 如果輸入為 null 或空白，則輸出屬性或常數。<br/>例如，如果您想要輸出儲存在 system.runtime.compilerservices.extensionattribute 中的屬性（如果指定使用者的員工識別碼是空的）。 若要這樣做，您可以設定下列值：<br/>參數1（輸入）：使用者. 員工。<br/>參數2（輸出）：使用者. extensionattribute1<br/>參數3（如果沒有符合的輸出）：使用者. 員工。 |
| **IfNotEmpty()** | 如果輸入不是 null 或空白，則輸出屬性或常數。<br/>例如，如果您想要輸出儲存在 system.runtime.compilerservices.extensionattribute 中的屬性（如果指定使用者的員工識別碼不是空的）。 若要這樣做，您可以設定下列值：<br/>參數1（輸入）：使用者. 員工。<br/>參數2（輸出）：使用者. extensionattribute1 |

如果您需要額外的轉換，請在 [ *SaaS 應用程式*] 類別底下[Azure AD 的意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599)中提交您的想法。

## <a name="emitting-claims-based-on-conditions"></a>根據條件發出宣告

您可以根據使用者類型和使用者所屬的群組，來指定宣告的來源。 

使用者類型可以是：
- **Any**：允許所有使用者存取應用程式。
- **成員**：租使用者的原生成員
- **所有來賓**：使用者會從具有或不 Azure AD 的外部組織進入。
- **AAD 來賓**：來賓使用者屬於另一個使用 Azure AD 的組織。
- **外部來賓**：來賓使用者屬於沒有 Azure AD 的外部組織。


當來賓的來源和存取應用程式的員工不同時，這會很有説明的一個案例。 您可能想要指定，如果使用者是一個員工，NameID 就是來自使用者。電子郵件，但如果使用者是來賓，則 NameID 會來自 extensionattribute1。

若要新增宣告條件：

1. 在 [**管理**宣告] 中，展開 [宣告條件]。
2. 選取 [使用者類型]。
3. 選取使用者應隸屬的群組。 您最多可以在指定的應用程式的所有宣告中選取10個唯一群組。 
4. 選取宣告要取得其值的**來源**。 您可以從 [來源屬性] 下拉式清單中選取使用者屬性，或將轉換套用至使用者屬性，再將其發出為宣告。

您新增條件的順序很重要。 Azure AD 會評估從上到下的條件，以決定要在宣告中發出哪一個值。 

例如，Brita Simon 是 Contoso 租使用者中的來賓使用者。 她屬於另一個也使用 Azure AD 的組織。 針對 Fabrikam 應用程式的下列設定，當 Brita 嘗試登入 Fabrikam 時，Azure AD 會依照下列方式評估條件。

首先，Azure AD 會驗證是否 `All guests`Brita 的使用者類型。 自起，這是 true，Azure AD 會將宣告的來源指派給 `user.extensionattribute1`。 第二，Azure AD 會驗證是否 `AAD guests`Brita 的使用者類型，因為這也是 true，因此 Azure AD 會將宣告的來源指派給 `user.mail`。 最後，會發出宣告，並將 Brita 的值 `user.email`。

![宣告條件式設定](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>後續步驟

* [Azure AD 中的應用程式管理](../manage-apps/what-is-application-management.md)
* [對不存在於 Azure AD 應用程式庫的應用程式設定單一登入](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [針對 SAML 型單一登入進行疑難排解](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
