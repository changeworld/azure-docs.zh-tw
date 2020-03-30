---
title: 微軟身份平臺帳戶& Android 上的租戶設定檔 |蔚藍
description: Android 的 Microsoft 標識平臺帳戶概述
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: d0497ad68e7b29e6d8c83dd860ba8f509e229579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611880"
---
# <a name="accounts--tenant-profiles-android"></a>帳戶和租用戶設定檔 (Android)

本文概述了 Microsoft 標識平臺中`account`的內容。

微軟身份驗證庫 （MSAL） API 將術語*使用者*替換為術語*帳戶*。 原因之一是使用者（人工或軟體代理）可能具有或可以使用多個帳戶。 這些帳戶可能位於使用者自己的組織中，和/或使用者所在的其他組織中。

Microsoft 標識平臺中的帳戶包括：

- 唯一識別碼。  
- 用於演示帳戶的擁有權/控制的一個或多個憑據。
- 一個或多個設定檔，由以下屬性組成：
  - 圖片， 給定名稱， 姓氏， 標題， 辦公室位置
- 帳戶具有權威來源或記錄系統。 這是創建帳戶的系統，存儲與該帳戶關聯的憑據。 在多租戶系統中，如 Microsoft 標識平臺，記錄系統是創建帳戶`tenant`的位置。 此租戶也稱為 。 `home tenant`
- Microsoft 標識平臺中的帳戶具有以下記錄系統：
  - Azure 活動目錄，包括 Azure 活動目錄 B2C。
  - 微軟帳戶（即時）。
- Microsoft 標識平臺外部的記錄系統的帳戶在 Microsoft 標識平臺中表示，包括：
  - 連接的本地目錄（Windows 伺服器活動目錄）的標識
  - 來自LinkedIn、GitHub 等的外部標識。
  在這些情況下，帳戶在 Microsoft 標識平臺中既有源記錄系統和記錄系統。
- Microsoft 標識平臺允許使用一個帳戶訪問屬於多個組織的資源（Azure 活動目錄租戶）。
  - 要記錄來自一個記錄系統 （AAD 租戶 A） 的帳戶有權訪問另一個記錄系統 （AAD 租戶 B） 中的資源，該帳戶必須在定義資源的租戶中表示。 這是通過在系統 B 中的系統 A 創建帳戶的本地記錄來實現的。
  - 此本地記錄（即帳戶的表示形式）綁定到原始帳戶。
  - MSAL 將此本地記錄公開為`Tenant Profile`。
  - 租戶設定檔可以具有適合本地上下文的不同屬性，如職務、辦公室位置、聯繫資訊等。
- 由於帳戶可能存在於一個或多個租戶中，因此帳戶可能具有多個設定檔。

> [!NOTE]
> MSAL 將 Microsoft 帳戶系統（即時、MSA）視為 Microsoft 標識平臺中的另一個租戶。 Microsoft 帳戶租戶的租戶 ID 是：`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>客戶概覽圖

![帳戶概述圖](./media/accounts-overview/accounts-overview.svg)

在上圖中：

- 該帳戶`bob@contoso.com`在本地 Windows 伺服器活動目錄（源本地記錄系統）中創建。
- 該帳戶`tom@live.com`在 Microsoft 帳戶租戶中創建。
- `bob@contoso.com`有權訪問以下 Azure 活動目錄租戶中的至少一個資源：
  - contoso.com（雲記錄系統 - 連結到本地記錄系統）
  - fabrikam.com
  - woodgrovebank.com
  - 每個租戶中都存在`bob@contoso.com`的租戶設定檔。
- `tom@live.com`有權訪問以下 Microsoft 租戶中的資源：
  - contoso.com
  - fabrikam.com
  - 每個租戶中都存在`tom@live.com`的租戶設定檔。
- 其他租戶中有關 Tom 和 Bob 的資訊可能與記錄系統中的資訊不同。 它們可能因屬性而異，例如職務、辦公室位置等。 他們可能是每個組織（Azure 活動目錄租戶）中的組和/或角色的成員。 我們將此資訊稱為bob@contoso.com租戶設定檔。

在關係圖中bob@contoso.com，tom@live.com並有權訪問不同 Azure 活動目錄租戶中的資源。 有關詳細資訊，請參閱在[Azure 門戶中添加 Azure 活動目錄 B2B 協作使用者](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。

## <a name="accounts-and-single-sign-on-sso"></a>帳戶和單一登入 （SSO）

MSAL 權杖緩存存儲每個帳戶*的單個刷新權杖*。 該刷新權杖可用於靜默請求來自多個 Microsoft 標識平臺租戶的訪問權杖。 當代理安裝在設備上時，該帳戶由代理管理，並且可以跨設備進行單一登入。

> [!IMPORTANT]
> 企業對消費者 （B2C） 帳戶和刷新權杖行為不同于 Microsoft 標識平臺的其餘部分。 有關詳細資訊，請參閱[B2C 策略&帳戶](#b2c-policies--accounts)。

## <a name="account-identifiers"></a>帳戶識別碼

MSAL 帳戶 ID 不是帳戶物件識別碼。 它並不意味著被解析和/或依賴來傳達 Microsoft 身份平臺中除了唯一性之外的任何其他內容。

為了與 Azure AD 身份驗證庫 （ADAL） 相容，並為了簡化從 ADAL 到 MSAL 的遷移，MSAL 可以使用 MSAL 緩存中可用的帳戶的任何有效識別碼查找帳戶。  例如，以下將始終檢索同一帳戶物件，tom@live.com因為每個識別碼都有效：

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>訪問有關帳戶的聲明

除了請求訪問權杖外，MSAL 還始終請求每個租戶的 ID 權杖。 它通過始終請求以下作用域來進行此工作：

- openid
- 設定檔

ID 權杖包含聲明清單。 `Claims`是帳戶的名稱/值對，用於發出請求。

如前所述，存在帳戶的每個租戶可能會存儲有關帳戶的不同資訊，包括但不限於屬性，如：職務、辦公室位置等。

雖然帳戶可能是多個組織中的成員或來賓，但 MSAL 不會查詢服務以獲取帳戶成員的租戶清單。 相反，MSAL 會生成帳戶存在的租戶清單，這是已發出權杖請求的結果。

在帳戶物件上公開的聲明始終是來自帳戶的"家庭租戶"/{許可權"的聲明。 如果該帳戶尚未用於為其家庭租戶請求權杖，MSAL 無法通過帳戶物件提供聲明。  例如：

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> 要查看帳戶物件中可用的聲明清單，請參閱[id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> 要在id_token中包括其他聲明，請參閱["如何：向 Azure AD 應用提供可選聲明"](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)中的可選聲明文檔

### <a name="access-tenant-profile-claims"></a>訪問租戶設定檔聲明

要訪問有關帳戶的聲明，因為它們出現在其他租戶中，首先需要將您的帳戶物件強制轉換為`IMultiTenantAccount`。 所有帳戶都可以是多租戶，但通過 MSAL 提供的租戶設定檔的數量取決於您請求使用當前帳戶的權杖的租戶。  例如：

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C 策略&帳戶

帳戶的刷新權杖不會跨 B2C 策略共用。 因此，無法使用權杖進行單一登入。 這並不意味著單一登入是不可能的。 這意味著單一登入必須使用互動式體驗，其中 Cookie 可用於啟用單一登入。

這也意味著，在 MSAL 中，如果您使用不同的 B2C 策略獲取權杖，則這些權杖將被視為單獨的帳戶 - 每個帳戶都有自己的識別碼。 如果要使用 帳戶使用`acquireTokenSilent`請求權杖，則需要從與權杖請求一起使用的策略的帳戶清單中選擇該帳戶。 例如：

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
