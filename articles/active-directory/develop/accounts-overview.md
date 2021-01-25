---
title: Android 上的 Microsoft 身分識別平臺帳戶 & 租使用者設定檔 |蔚藍
description: 適用于 Android 的 Microsoft 身分識別平臺帳戶總覽
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
ms.custom: aaddev, devx-track-java
ms.reviewer: shoatman
ms.openlocfilehash: fac66e8f82ea4c04e866b28fed5f8d0860ab81ef
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755962"
---
# <a name="accounts--tenant-profiles-android"></a>帳戶和租用戶設定檔 (Android)

本文概述 `account` Microsoft 身分識別平臺中的內容。

Microsoft 驗證程式庫 (MSAL) API 會以「詞彙」*帳戶* 取代「*使用者*」一詞。 其中一個原因是使用者 (的人或軟體代理程式) 可能會有多個帳戶，或可使用多個帳戶。 這些帳戶可能位於使用者的組織，以及/或使用者所屬的其他組織中。

Microsoft 身分識別平臺中的帳戶包含：

- 唯一識別碼。  
- 用來示範帳戶擁有權/控制權的一或多個認證。
- 由下列屬性組成的一或多個設定檔：
  - 圖片，指定名稱、系列名稱、標題、辦公室位置
- 帳戶有授權來源或記錄系統。 這是建立帳戶的系統，以及與該帳戶相關聯之認證的儲存位置。 在像是 Microsoft 身分識別平臺的多租使用者系統中，記錄的系統是 `tenant` 建立帳戶的位置。 此租使用者也稱為 `home tenant` 。
- Microsoft 身分識別平臺中的帳戶具有下列記錄系統：
  - Azure Active Directory，包括 Azure Active Directory B2C。
  - Microsoft 帳戶 (即時) 。
- Microsoft 身分識別平臺外部記錄系統的帳戶會在 Microsoft 身分識別平臺中表示，包括：
  - 來自已連線內部部署目錄的身分識別 (Windows Server Active Directory) 
  - LinkedIn、GitHub 等的外部身分識別。
  在這些情況下，帳戶在 Microsoft 身分識別平臺內都有記錄的原始系統和記錄系統。
- Microsoft 身分識別平臺可讓您使用一個帳戶來存取屬於多個組織 (Azure Active Directory 租使用者) 的資源。
  - 若要將帳戶從一個記錄系統記錄 (AAD 租使用者 A) 有權存取另一個記錄系統 (AAD 租使用者 B) 中的資源，則該帳戶必須在定義資源的租使用者中表示。 這是藉由從系統 B 中的系統 A 建立帳戶的本機記錄來完成。
  - 此本機記錄（即帳戶的標記法）會系結至原始帳戶。
  - MSAL 會將此本機記錄公開為 `Tenant Profile` 。
  - 租使用者設定檔可以有適合本機內容的不同屬性，例如職稱、辦公室位置、連絡人資訊等。
- 由於帳戶可能存在於一或多個租使用者中，因此帳戶可能會有一個以上的設定檔。

> [!NOTE]
> MSAL 會將 Microsoft 帳戶系統 (Live、MSA) 視為 Microsoft 身分識別平臺中的另一個租使用者。 Microsoft 帳戶租使用者的租使用者識別碼為： `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>帳戶總覽圖表

![帳戶總覽圖表](./media/accounts-overview/accounts-overview.svg)

在上圖中：

- 帳戶 `bob@contoso.com` 會建立在內部部署 Windows Server Active Directory (源內部部署系統的記錄) 。
- 帳戶 `tom@live.com` 會建立在 Microsoft 帳戶租使用者中。
- `bob@contoso.com` 可以存取下列 Azure Active Directory 租使用者中至少一個資源：
  - contoso.com (的記錄連結至記錄) 的內部部署系統
  - fabrikam.com
  - woodgrovebank.com
  - 的租使用者設定檔會存在於這些租使用者 `bob@contoso.com` 中。
- `tom@live.com` 具有下列 Microsoft 租使用者中資源的存取權：
  - contoso.com
  - fabrikam.com
  - 的租使用者設定檔會存在於這些租使用者 `tom@live.com` 中。
- 其他租使用者中的 Tom 和 Bob 相關資訊可能與記錄系統中的資訊不同。 它們可能會因諸如職稱、辦公室位置等等的屬性而不同。 它們可能是每個組織內群組和/或角色的成員 (Azure Active Directory 租使用者) 。 我們將此資訊稱為 bob@contoso.com 租使用者設定檔。

在圖表中， bob@contoso.com 並 tom@live.com 可存取不同 Azure Active Directory 租使用者中的資源。 如需詳細資訊，請參閱 [Azure 入口網站中的新增 AZURE ACTIVE DIRECTORY B2B](../external-identities/add-users-administrator.md)共同作業使用者。

## <a name="accounts-and-single-sign-on-sso"></a>帳戶和單一登入 (SSO) 

MSAL 權杖快取會為每個帳戶儲存單一重新整理 *權杖* 。 您可以使用該重新整理權杖，以無訊息方式向多個 Microsoft 身分識別平臺租使用者要求存取權杖。 當訊息代理程式安裝在裝置上時，此帳戶是由訊息代理程式所管理，而且可以進行全裝置的單一登入。

> [!IMPORTANT]
> 企業對消費者 (B2C) 帳戶和重新整理權杖行為與 Microsoft 身分識別平臺的其餘部分不同。 如需詳細資訊，請參閱 [B2C 原則 & 帳戶](#b2c-policies--accounts)。

## <a name="account-identifiers"></a>帳戶識別碼

MSAL 帳戶識別碼不是帳戶物件識別碼。 它不是為了剖析及/或依賴，以傳達 Microsoft 身分識別平臺內唯一性以外的任何資訊。

為了與 Azure AD 驗證程式庫 (ADAL) 的相容性，並可讓您輕鬆地從 ADAL 遷移至 MSAL，MSAL 可以針對 MSAL 快取中提供的帳戶使用任何有效的識別碼來查詢帳戶。  例如，下列程式一律會取出相同的帳戶物件， tom@live.com 因為每個識別碼都有效：

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>存取有關帳戶的宣告

除了要求存取權杖，MSAL 也一律會要求每個租使用者的識別碼權杖。 它會藉由一律要求下列範圍來執行此動作：

- openid
- profile

識別碼權杖包含宣告清單。 `Claims` 是帳戶的名稱/值組，用來提出要求。

如先前所述，帳戶存在的每個租使用者都可能會儲存與帳戶不同的資訊，包括但不限於下列屬性（例如：職稱、辦公室位置等等）。

雖然帳戶可以是多個組織的成員或來賓，但 MSAL 並不會查詢服務來取得帳戶所屬的租使用者清單。 相反地，MSAL 會建立帳戶存在的租使用者清單，因為已提出權杖要求。

帳戶物件上公開的宣告一律是帳戶之「主要租使用者」/{authority} 中的宣告。 如果該帳戶尚未用來要求其 home 租使用者的權杖，則 MSAL 無法透過 account 物件提供宣告。  例如：

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> 若要查看帳戶物件中可用的宣告清單，請參閱 [id_token 中的宣告](./id-tokens.md#claims-in-an-id_token)

> [!TIP]
> 若要在 id_token 中包含其他宣告，請參閱[如何：為您的 Azure AD 應用程式提供選擇性宣告](./active-directory-optional-claims.md)的選擇性宣告檔

### <a name="access-tenant-profile-claims"></a>存取租使用者設定檔宣告

若要存取與其他租使用者中的帳戶相關的宣告，您必須先將您的帳戶物件轉換成 `IMultiTenantAccount` 。 所有帳戶都可以是多租使用者，但可透過 MSAL 使用的租使用者設定檔數目是根據您使用目前帳戶要求權杖的租使用者。  例如：

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C 原則 & 帳戶

帳戶的重新整理權杖不會跨 B2C 原則共用。 因此，不可能使用權杖進行單一登入。 這並不表示無法進行單一登入。 這表示單一登入必須使用可用的 cookie 來啟用單一登入的互動式體驗。

這也表示在 MSAL 的情況下，如果您使用不同的 B2C 原則取得權杖，則會將這些帳戶視為個別的帳戶，而每個帳戶都有自己的識別碼。 如果您想要使用帳戶來要求使用的權杖 `acquireTokenSilent` ，您必須從符合您使用權杖要求之原則的帳戶清單中選取帳戶。 例如：

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