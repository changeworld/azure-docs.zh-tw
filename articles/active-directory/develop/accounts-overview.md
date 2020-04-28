---
title: Microsoft 身分識別平臺帳戶 & Android 上的租使用者設定檔 |Azure
description: 瞭解適用于 Android 的 Microsoft 身分識別平臺帳戶
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77611880"
---
# <a name="accounts--tenant-profiles-android"></a>帳戶和租用戶設定檔 (Android)

本文概要說明 Microsoft 身分識別平臺的`account`功能。

Microsoft 驗證程式庫（MSAL） API 會以「*帳戶*」詞彙取代「*使用者*」一詞。 其中一個原因是使用者（人類或軟體代理程式）可能會有或可以使用多個帳戶。 這些帳戶可能位於使用者自己的組織中，以及（或）使用者為其成員的其他組織中。

Microsoft 身分識別平臺中的帳戶包含：

- 唯一識別碼。  
- 一或多個用來示範帳戶擁有權/控制的認證。
- 包含屬性的一或多個設定檔，例如：
  - 圖片，名字，系列名稱，標題，辦公室位置
- 帳戶具有「授權」或「記錄」系統的來源。 這是建立帳戶的系統，以及與該帳戶相關聯之認證的儲存位置。 在像是 Microsoft 身分識別平臺的多租使用者系統中，記錄的系統`tenant`是建立帳戶的位置。 此租使用者也稱為`home tenant`。
- Microsoft 身分識別平臺中的帳戶具有下列記錄系統：
  - Azure Active Directory，包括 Azure Active Directory B2C。
  - Microsoft 帳戶（即時）。
- Microsoft 身分識別平臺外部記錄的系統帳戶會在 Microsoft 身分識別平臺中呈現，包括：
  - 來自已連線內部部署目錄的身分識別（Windows Server Active Directory）
  - LinkedIn、GitHub 等的外部身分識別。
  在這些情況下，帳戶在 Microsoft 身分識別平臺內有記錄的原始系統和記錄系統。
- Microsoft 身分識別平臺可讓您使用一個帳戶來存取屬於多個組織（Azure Active Directory 租使用者）的資源。
  - 若要記錄某個記錄系統（AAD 租使用者 A）中的帳戶可存取另一個記錄系統（AAD 租使用者 B）中的資源，該帳戶必須在定義資源的租使用者中表示。 這是藉由從系統 B 中的系統 A 建立帳戶的本機記錄來完成。
  - 此本機記錄（這是帳戶的標記法）會系結至原始帳戶。
  - MSAL 會將`Tenant Profile`此本機記錄公開為。
  - 租使用者設定檔可以有適用于本機內容的不同屬性，例如作業標題、辦公室位置、連絡人資訊等。
- 因為一個或多個租使用者中可能有一個帳戶，所以一個帳戶可能會有一個以上的設定檔。

> [!NOTE]
> MSAL 會將 Microsoft 帳戶系統（Live、MSA）視為 Microsoft 身分識別平臺中的另一個租使用者。 Microsoft 帳戶租使用者的租使用者識別碼是：`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>帳戶總覽圖表

![帳戶總覽圖表](./media/accounts-overview/accounts-overview.svg)

在上圖中：

- 帳戶`bob@contoso.com`會建立于內部部署 Windows Server Active Directory 中（記錄的原始內部部署系統）。
- 帳戶`tom@live.com`會建立在 Microsoft 帳戶租使用者中。
- `bob@contoso.com`具有下列 Azure Active Directory 租使用者中至少一個資源的存取權：
  - contoso.com （記錄的雲端系統-連結至記錄的內部部署系統）
  - fabrikam.com
  - woodgrovebank.com
  - 中的租使用者`bob@contoso.com`設定檔存在於每個租使用者中。
- `tom@live.com`具有下列 Microsoft 租使用者中資源的存取權：
  - contoso.com
  - fabrikam.com
  - 中的租使用者`tom@live.com`設定檔存在於每個租使用者中。
- 有關其他租使用者中的 Tom 和 Bob 的資訊，可能會與記錄系統中的不同。 它們可能會因諸如職稱、辦公室位置等屬性而有所不同。 它們可能是每個組織（Azure Active Directory 租使用者）中的群組和（或）角色的成員。 我們將此資訊稱為bob@contoso.com租使用者設定檔。

在圖表中， bob@contoso.com和tom@live.com可以存取不同 Azure Active Directory 租使用者中的資源。 如需詳細資訊，請參閱[在 Azure 入口網站中新增 AZURE ACTIVE DIRECTORY B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)共同作業使用者。

## <a name="accounts-and-single-sign-on-sso"></a>帳戶和單一登入（SSO）

MSAL 權杖快取會針對每個帳戶儲存單一重新整理*權杖*。 該重新整理權杖可用於以無訊息方式向多個 Microsoft 身分識別平臺租使用者要求存取權杖。 當代理程式安裝在裝置上時，該帳戶會由訊息代理程式管理，而全裝置的單一登入則會變成可行。

> [!IMPORTANT]
> 企業對消費者（B2C）帳戶和重新整理權杖行為不同于 Microsoft 身分識別平臺的其他部分。 如需詳細資訊，請參閱[B2C 原則 & 帳戶](#b2c-policies--accounts)。

## <a name="account-identifiers"></a>帳戶識別碼

MSAL 帳戶識別碼不是帳戶物件識別碼。 它不是用來剖析及/或依賴在 Microsoft 身分識別平臺中傳達唯一性以外的任何專案。

為了與 Azure AD 驗證程式庫（ADAL）相容，並可讓您輕鬆地從 ADAL 遷移至 MSAL，MSAL 可以針對 MSAL 快取中可用的帳戶，使用任何有效的識別碼來查詢帳戶。  例如，下列程式一律會抓取的相同帳戶物件， tom@live.com因為每個識別碼都是有效的：

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>存取帳戶的相關宣告

除了要求存取權杖之外，MSAL 也一律會要求來自每個租使用者的識別碼權杖。 它會藉由一律要求下列範圍來執行此動作：

- openid
- profile

識別碼權杖包含宣告的清單。 `Claims`是關於帳戶的名稱/值組，用來提出要求。

如先前所述，帳戶所在的每個租使用者都可能會儲存有關帳戶的不同資訊，包括但不限於下列屬性：職稱、辦公室位置等等。

雖然帳戶可能是多個組織中的成員或來賓，但 MSAL 並不會查詢服務來取得帳戶為其成員的租使用者清單。 相反地，MSAL 會建立帳戶所在的租使用者清單，這是已提出的權杖要求所產生的結果。

帳戶物件上公開的宣告一律是帳戶的「主要租使用者」/{authority} 中的宣告。 如果該帳戶尚未用來要求其主租使用者的權杖，MSAL 就無法透過帳戶物件提供宣告。  例如：

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> 若要查看帳戶物件中可用的宣告清單，請參閱[id_token 中的宣告](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> 若要在您的 id_token 中加入其他宣告，請參閱[如何：為您的 Azure AD 應用程式提供選擇性宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)中的選擇性宣告檔

### <a name="access-tenant-profile-claims"></a>存取租使用者設定檔宣告

若要存取帳戶出現在其他租使用者中的相關宣告，您必須先將您的帳戶物件`IMultiTenantAccount`轉換成。 所有帳戶都可以是多租使用者，但可透過 MSAL 取得的租使用者設定檔數目，是根據您已向使用目前帳戶的權杖所要求的 tenant。  例如：

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C 原則 & 帳戶

帳戶的重新整理權杖不會在 B2C 原則之間共用。 因此，無法使用權杖來進行單一登入。 這並不表示無法進行單一登入。 這表示單一登入必須使用互動式體驗，其中有可用的 cookie 可啟用單一登入。

這也表示，在 MSAL 的案例中，如果您使用不同的 B2C 原則取得權杖，則會將這些視為個別的帳戶，而每個都有自己的識別碼。 如果您想要使用帳戶來要求使用`acquireTokenSilent`的權杖，則必須從符合您要與權杖要求搭配使用之原則的帳戶清單中選取帳戶。 例如：

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
