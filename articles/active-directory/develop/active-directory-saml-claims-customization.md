---
title: 自訂 Azure AD 應用 SAML 權杖聲明
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263084"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>如何：為企業應用程式自訂 SAML 權杖中發出的聲明

如今，Azure 活動目錄 （Azure AD） 支援大多數企業應用程式的單一登入 （SSO），包括預先集成在 Azure AD 應用庫中的應用程式以及自訂應用程式。 當使用者利用 SAML 2.0 通訊協定來透過 Azure AD 向應用程式驗證時，Azure AD 會將權杖傳送給應用程式 (透過 HTTP POST)。 然後，應用程式會驗證並使用權杖將使用者登入，而不會提示輸入使用者名稱和密碼。 這些 SAML 權杖包含有關稱為*聲明*的使用者的資訊片段。

*宣告*是身分識別提供者核發權杖給使用者時，所提供的權杖內部使用者相關資訊。 在 [SAML 權杖](https://en.wikipedia.org/wiki/SAML_2.0)中，此資料通常包含在 SAML 屬性陳述式中。 使用者的唯一識別碼通常在 SAML Subject 中表示，也稱為「名稱識別碼」。

預設情況下，Azure AD 向應用程式發出 SAML 權杖，其中包含具有`NameIdentifier`Azure AD 中使用者名（也稱為使用者主體名稱）的值的聲明，該聲明可以唯一標識使用者。 SAML 權杖也包含額外的宣告，其包含使用者的電子郵件地址、名字和姓氏。

若要檢視或編輯在 SAML 權杖中對應用程式發出的宣告，請在 Azure 入口網站中開啟應用程式。 然後打開 **"使用者屬性&聲明**"部分。

![在 Azure 門戶中打開使用者屬性&宣告區段](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

編輯在 SAML 權杖中簽發的宣告有兩個可能原因：

* 應用程式要求`NameIdentifier`或 NameID 聲明是存儲在 Azure AD 中的使用者名（或使用者主體名稱）以外的內容。
* 應用程式是設計為要求不同的宣告 URI 組或宣告值。

## <a name="editing-nameid"></a>編輯名稱 ID

要編輯 NameID（名稱識別碼值）：

1. 打開**名稱識別碼值**頁。
1. 選擇要應用於該屬性的屬性或轉換。 或者，您可以指定希望 NameID 聲明具有的格式。

   ![編輯 NameID（名稱識別碼）值](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>名稱 ID 格式

如果 SAML 請求包含具有特定格式的元素 NameID 策略，則 Azure AD 將遵守請求中的格式。

如果 SAML 請求不包含 NameID 策略的元素，則 Azure AD 將發出名稱 ID 與您指定的格式。 如果未指定格式，則 Azure AD 將使用與所選聲明源關聯的預設源格式。

從 **"選擇名稱識別碼"格式**下拉下拉清單，您可以選擇以下選項之一。

| 名稱 ID 格式 | 描述 |
|---------------|-------------|
| **預設** | Azure AD 將使用預設源格式。 |
| **持續性** | Azure AD 將使用"持久"作為 NameID 格式。 |
| **EmailAddress** | Azure AD 將使用電子郵件地址作為 NameID 格式。 |
| **未指定** | Azure AD 將使用"未指定"格式作為 NameID 格式。 |
| **視窗功能變數名稱限定名稱** | Azure AD 將使用 WindowsDomain 資格名稱作為名稱 ID 格式。 |

臨時 NameID 也受支援，但在下拉清單中不可用，無法在 Azure 端配置。 要瞭解有關 NameID 策略屬性的更多詳細資訊，請參閱[單一登入 SAML 協定](single-sign-on-saml-protocol.md)。

### <a name="attributes"></a>屬性

選取 `NameIdentifier` (或 NameID) 宣告的所需來源。 您可以從下列選項選取。

| 名稱 | 描述 |
|------|-------------|
| 電子郵件 | 使用者的電子郵件地址 |
| userprincipalName | 使用者的主名 （UPN） |
| onpremisessamaccount | 已從內部部署 Azure AD 同步處理的 SAM 帳戶名稱 |
| objectid | Azure AD 中使用者的物件識別碼 |
| employeeid | 使用者的員工 ID |
| 目錄擴充 | 目錄擴充[從使用 Azure AD Connect 同步的內部部署 Active Directory 同步處理](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| 擴充屬性 1-15 | 內部部署擴充屬性，用來擴充 Azure AD 結構描述 |

有關詳細資訊，請參閱表[3：每個源的有效 ID 值](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)。

您還可以為在 Azure AD 中定義的任何聲明分配任何常量（靜態）值。 請按照以下步驟分配常量值：

1. 在[Azure 門戶](https://portal.azure.com/)中，在 **"使用者屬性&聲明"** 部分，按一下 **"編輯"** 圖示編輯聲明。

1. 按一下要修改所需的聲明。

1. 根據您的組織在 **"源"屬性**中輸入無引號的常量值，然後按一下"**保存**"。

    ![在 Azure 門戶中打開使用者屬性&宣告區段](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. 常量值將顯示如下。

    ![在 Azure 門戶中打開使用者屬性&宣告區段](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>特殊索賠 - 轉換

您還可以使用聲明轉換函數。

| 函式 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 從電子郵件地址或使用者主體名稱中刪除域尾碼。 這只會擷取使用者名稱的第一個部分 (例如，"joe_smith" 而不是 joe_smith@contoso.com)。 |
| **加入（）** | 加入具有已驗證網域的屬性。 如果選取的使用者識別碼值具有網域，它會擷取使用者名稱以附加所選的已驗證網域。 例如，如果您選取電子郵件 (joe_smith@contoso.com) 作為使用者識別碼值，並選取 contoso.onmicrosoft.com 作為已驗證的網域，這樣會產生 joe_smith@contoso.onmicrosoft.com。 |
| **到下（）** | 將所選取屬性中的字元轉換成小寫字元。 |
| **到上（）** | 將所選取屬性中的字元轉換成大寫字元。 |

## <a name="adding-application-specific-claims"></a>添加特定于應用程式的聲明

要添加特定于應用程式的聲明：

1. 在 **"使用者屬性&聲明**"中，選擇 **"添加新聲明**"以打開 **"管理使用者聲明**"頁。
1. 輸入聲明**的名稱**。 根據 SAML 規範，該值不需要嚴格遵循 URI 模式。如果需要 URI 模式，可以把它放在**命名空間**欄位中。
1. 選擇聲明要檢索其值的**源**。 您可以從源屬性下拉清單中選擇使用者屬性，或在將轉換作為聲明發出之前對使用者屬性應用轉換。

### <a name="claim-transformations"></a>宣告轉換

要將轉換應用於使用者屬性，請執行：

1. 在 **"管理索賠"** 中，選擇 *"轉換*"作為聲明源以打開 **"管理轉換**"頁。
2. 從轉換下拉清單中選擇函數。 根據所選的函數，您必須提供參數和常量值才能在轉換中計算。 有關可用函數的詳細資訊，請參閱下表。
3. 要應用多個轉換，請按一下"**添加轉換**"。最多可以對索賠應用兩個轉換。 例如，您可以首先提取 的電子郵件`user.mail`首碼。 然後，使字串大寫。

   ![編輯 NameID（名稱識別碼）值](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

您可以使用以下函數轉換聲明。

| 函式 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 從電子郵件地址或使用者主體名稱中刪除域尾碼。 這只會擷取使用者名稱的第一個部分 (例如，"joe_smith" 而不是 joe_smith@contoso.com)。 |
| **加入（）** | 通過聯接兩個屬性創建新值。 或者，您可以在兩個屬性之間使用分隔符號。 對於 NameID 聲明轉換，聯接僅限於已驗證的域。 如果選取的使用者識別碼值具有網域，它會擷取使用者名稱以附加所選的已驗證網域。 例如，如果您選取電子郵件 (joe_smith@contoso.com) 作為使用者識別碼值，並選取 contoso.onmicrosoft.com 作為已驗證的網域，這樣會產生 joe_smith@contoso.onmicrosoft.com。 |
| **到下（）** | 將所選取屬性中的字元轉換成小寫字元。 |
| **到上（）** | 將所選取屬性中的字元轉換成大寫字元。 |
| **包含（）** | 如果輸入與指定值匹配，則輸出屬性或常量。 否則，如果沒有匹配，可以指定另一個輸出。<br/>例如，如果要發出值是使用者的電子郵件地址的聲明（如果它包含域""），@contoso.com則要輸出使用者主體名稱。 為此，您可以配置以下值：<br/>*參數 1（輸入）*：使用者.電子郵件<br/>*值*："@contoso.com"<br/>參數 2（輸出）：使用者.電子郵件<br/>參數 3（如果沒有匹配項，則輸出）：使用者.user主名稱 |
| **結束與（）** | 如果輸入以指定值結束，則輸出屬性或常量。 否則，如果沒有匹配，可以指定另一個輸出。<br/>例如，如果要發出值是使用者的員工 ID 的聲明（如果員工 ID 以"000"結尾），則要輸出擴充屬性。 為此，您可以配置以下值：<br/>*參數 1（輸入）*：使用者.員工id<br/>*值*："000"<br/>參數 2（輸出）：使用者.員工 ID<br/>參數 3（如果沒有匹配時輸出）：使用者.擴充屬性1 |
| **從（） 開始** | 如果輸入以指定值開頭，則輸出屬性或常量。 否則，如果沒有匹配，可以指定另一個輸出。<br/>例如，如果要發出值是使用者的員工 ID 的聲明（如果國家/地區以"US"開頭），則要輸出擴充屬性。 為此，您可以配置以下值：<br/>*參數 1（輸入）*：使用者.國家/地區<br/>*值*："美國"<br/>參數 2（輸出）：使用者.員工 ID<br/>參數 3（如果沒有匹配時輸出）：使用者.擴充屬性1 |
| **提取（） - 匹配後** | 在子字串與指定值匹配後返回該子字串。<br/>例如，如果輸入的值為"Finance_BSimon"，則匹配值為"Finance_"，則聲明的輸出為"BSimon"。 |
| **提取（） - 匹配前** | 返回子字串，直到它與指定值匹配。<br/>例如，如果輸入的值為"BSimon_US"，則匹配值為"_US"，則聲明的輸出為"BSimon"。 |
| **提取（） - 匹配之間** | 返回子字串，直到它與指定值匹配。<br/>例如，如果輸入的值為"Finance_BSimon_US"，則第一個匹配值為"Finance_"，第二個匹配值為"_US"，則聲明的輸出為"BSimon"。 |
| **提取Alpha（） - 首碼** | 返回字串的首碼字母部分。<br/>例如，如果輸入的值為"BSimon_123"，則返回"BSimon"。 |
| **提取Alpha（） - 尾碼** | 返回字串的尾碼字母部分。<br/>例如，如果輸入的值為"123_Simon"，則返回"Simon"。 |
| **提取數位（） - 首碼** | 返回字串的首碼數位部分。<br/>例如，如果輸入的值為"123_BSimon"，則返回"123"。 |
| **提取數位（） - 尾碼** | 返回字串的尾碼數值部分。<br/>例如，如果輸入的值為"BSimon_123"，則返回"123"。 |
| **如果空（）** | 如果輸入為空或為空，則輸出屬性或常量。<br/>例如，如果要輸出存儲在擴充屬性中的屬性，如果給定使用者的員工 ID 為空。 為此，您可以配置以下值：<br/>參數 1（輸入）：使用者.員工 ID<br/>參數 2（輸出）：使用者.擴充屬性1<br/>參數 3（如果沒有匹配項，則輸出）：使用者.employeeid |
| **如果不為空（）** | 如果輸入不是空或空，則輸出屬性或常量。<br/>例如，如果要輸出存儲在擴充屬性中的屬性，如果給定使用者的員工 ID 不為空。 為此，您可以配置以下值：<br/>參數 1（輸入）：使用者.員工 ID<br/>參數 2（輸出）：使用者.擴充屬性1 |

如果需要其他轉換，請在*SaaS 應用程式*類別下的[Azure AD 回饋論壇中](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599)提交您的想法。

## <a name="emitting-claims-based-on-conditions"></a>根據條件發出聲明

您可以根據使用者類型和使用者所屬的組指定聲明的來源。 

使用者類型可以是：
- **任何**：允許所有使用者訪問該應用程式。
- **成員**： 租戶的本機成員
- **所有來賓**：使用者是從具有或沒有 Azure AD 的外部組織帶來的。
- **AAD 來賓**：來賓使用者屬於使用 Azure AD 的另一個組織。
- **外部來賓**：來賓使用者屬於沒有 Azure AD 的外部組織。


其中一種情況是，對於訪問應用程式的來賓和員工，索賠的來源是不同的。 您可能希望指定，如果使用者是員工，NameID 來自 user.email，但如果使用者是來賓，則 NameID 來自使用者。

要添加聲明條件：

1. 在**管理索賠**中，展開索賠條件。
2. 選擇使用者類型。
3. 選擇使用者應所屬的組。 您最多可以在給定應用程式的所有聲明中選擇 10 個唯一組。 
4. 選擇聲明要檢索其值的**源**。 您可以從源屬性下拉清單中選擇使用者屬性，或在將轉換作為聲明發出之前對使用者屬性應用轉換。

添加條件的順序很重要。 Azure AD 從上到下評估條件，以決定在索賠中釋放哪個值。 

例如，Brita Simon 是 Contoso 租戶中的來賓使用者。 她屬於另一個也使用 Azure AD 的組織。 給定 Fabrikam 應用程式的以下配置，當 Brita 嘗試登錄到 Fabrikam 時，Azure AD 將按如下條件評估條件。

首先，Azure AD 驗證 Brita 的使用者類型是否為`All guests`。 由於，這是事實，然後 Azure AD 將聲明的源分配給`user.extensionattribute1`。 其次，Azure AD 驗證 Brita 的使用者類型是否為`AAD guests`，因為這也是 true，然後 Azure AD 將聲明的源`user.mail`分配給 。 最後，聲明以"Brita"`user.email`的值發出。

![聲明條件配置](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>後續步驟

* [Azure AD 中的應用程式管理](../manage-apps/what-is-application-management.md)
* [對不存在於 Azure AD 應用程式庫的應用程式設定單一登入](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [針對 SAML 型單一登入進行疑難排解](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
