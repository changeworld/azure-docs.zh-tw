---
title: 使用 Azure Active Directory 設定應用程式的群組宣告 |Microsoft Docs
description: 有關如何設定群組宣告以與 Azure AD 搭配使用的資訊。
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 78b36e1f5ababf2551bd69682807a8ed308ae24d
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298440"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>使用 Azure Active Directory 設定應用程式的群組宣告（公開預覽）

Azure Active Directory 可以在權杖中提供使用者群組成員資格資訊，以便在應用程式中使用。  支援兩種主要模式：

- 依其 Azure Active Directory 物件識別元（OID）屬性所識別的群組（已正式運作）
- Active Directory （AD）同步處理的群組和使用者的 sAMAccountName 或 GroupSID 屬性所識別的群組（公開預覽）

> [!IMPORTANT]
> 針對此預覽功能，有一些需要注意的事項：
>
>- 支援使用從內部部署同步處理的 sAMAccountName 和安全識別碼（SID）屬性，其設計目的是要讓現有應用程式從 AD FS 和其他身分識別提供者移動。 Azure AD 中受管理的群組不包含發出這些宣告所需的屬性。
>- 在較大型組織中，使用者所屬的群組數目可能會超過 Azure Active Directory 將新增至權杖的限制。 150適用于 SAML 權杖的群組，以及200的 JWT。 這可能會導致無法預期的結果。 如果您的使用者有大量的群組成員資格，建議使用選項，將宣告中發出的群組限制為應用程式的相關群組。  
>- 針對新的應用程式開發，或在可以為其設定應用程式的情況下，以及不需要的嵌套群組支援，我們建議以應用程式角色（而非群組）為基礎的應用程式內授權。  這會限制需要進入權杖、較安全，以及將使用者指派與應用程式設定分開的資訊量。

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>從 AD FS 和其他身分識別提供者遷移之應用程式的群組宣告

許多設定為使用 AD FS 進行驗證的應用程式，都是以 Windows AD 群組屬性的形式，依賴群組成員資格資訊。   這些屬性是群組 sAMAccountName，可能是以功能變數名稱或 Windows 群組安全識別碼（GroupSID）來限定。  當應用程式與 AD FS 同盟時，AD FS 會使用 TokenGroups 函式來取得使用者的群組成員資格。

已從 AD FS 移動的應用程式需要相同格式的宣告。 群組和角色宣告可能會從包含網域限定 sAMAccountName 的 Azure Active Directory，或從 Active Directory 同步處理的 GroupSID （而不是從群組的 Azure Active Directory objectID）發出。

群組宣告支援的格式如下：

- **Azure Active Directory 群組 ObjectId** （適用于所有群組）
- **sAMAccountName** （適用于從 Active Directory 同步處理的群組）
- **NetbiosDomain\sAMAccountName** （適用于從 Active Directory 同步處理的群組）
- **DNSDomainName\sAMAccountName** （適用于從 Active Directory 同步處理的群組）
- **內部部署群組安全識別碼**（可用於從 Active Directory 同步處理的群組）

> [!NOTE]
> sAMAccountName 和內部部署群組 SID 屬性僅適用于從 Active Directory 同步處理的群組物件。   它們無法在 Azure Active Directory 或 Office365 中建立的群組上使用。   在 Azure Active Directory 中設定來取得同步內部部署群組屬性的應用程式，只會取得同步處理的群組。

## <a name="options-for-applications-to-consume-group-information"></a>應用程式使用群組資訊的選項

應用程式可以呼叫 MS Graph 群組端點來取得已驗證使用者的群組資訊。 此呼叫可確保使用者所屬的所有群組都可供使用，即使牽涉到大量群組也是如此。  然後，群組列舉會與權杖大小限制無關。

不過，如果現有的應用程式預期透過宣告取用群組資訊，Azure Active Directory 可以使用許多不同的宣告格式來設定。  請考慮下列選項：

- 針對應用程式內授權用途使用群組成員資格時，最好使用群組 ObjectID。 群組 ObjectID 在 Azure Active Directory 中是不可變且唯一的，而且適用于所有群組。
- 如果使用內部部署群組 sAMAccountName 進行授權，請使用網域限定名稱; 名稱衝突的可能性較少。 sAMAccountName 在 Active Directory 網域中可能是唯一的，但如果有一個以上的 Active Directory 網域與 Azure Active Directory 的租使用者同步，則可能有多個群組具有相同的名稱。
- 請考慮使用[應用程式角色](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)來提供群組成員資格和應用程式之間的間接取值層。   然後，應用程式會根據權杖中的角色 clams，進行內部授權決策。
- 如果應用程式設定為取得從 Active Directory 同步處理的群組屬性，而且群組不包含這些屬性，則宣告中不會包含該屬性。
- 權杖中的群組宣告包含嵌套群組，但使用選項將群組宣告限制為指派給應用程式的群組時除外。  如果使用者是 GroupB 的成員，而 GroupB 是 GroupA 的成員，則使用者的群組宣告將同時包含 GroupA 和 GroupB。 當組織的使用者有大量的群組成員資格時，權杖中列出的群組數目可能會增加權杖大小。  Azure Active Directory 會限制針對 SAML 判斷提示，將會在權杖中發出為150的群組數目，以及200（適用于 JWT）。  如果使用者是大量群組的成員，則會省略群組，而會包含圖形端點的連結以取得群組資訊。

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>使用從 Active Directory 同步處理群組屬性的必要條件

如果您使用 ObjectId 格式，就可以在任何群組的權杖中發出群組成員資格宣告。 若要使用群組 ObjectId 以外的格式的群組宣告，必須使用 Azure AD Connect 從 Active Directory 同步處理群組。

有兩個步驟可設定 Azure Active Directory 來發出 Active Directory 群組的組名。

1. **從 Active Directory 同步處理組名**在 Azure Active Directory 可以在群組或角色宣告中發出組名或內部部署群組 SID 之前，需要從 Active Directory 同步處理必要的屬性。  您必須執行 Azure AD Connect 1.2.70 或更新版本。   早于1.2.70 的 Azure AD Connect 版本會從 Active Directory 同步處理群組物件，但不會包含必要的組名屬性。  升級為目前的版本。

2. **在 Azure Active Directory 中設定應用程式註冊，以在權杖中包含群組宣告**您可以在入口網站的 [企業應用程式] 區段中，或在 [應用程式註冊] 區段中使用應用程式資訊清單來設定群組宣告。  若要在應用程式資訊清單中設定群組宣告，請參閱下面的「設定群組屬性的 Azure Active Directory 應用程式註冊」。

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>使用 SSO 設定將群組宣告新增至 SAML 應用程式的權杖

若要設定資源庫或非資源庫 SAML 應用程式的群組宣告，請開啟 [**企業應用程式**]，按一下清單中的應用程式，選取 [**單一登入**設定]，然後選取 [**使用者屬性 & 宣告**]。

按一下 [**新增群組**宣告]  

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

使用選項按鈕來選取要包含在權杖中的群組

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 選取項目 | 描述 |
|----------|-------------|
| **所有群組** | 發出安全性群組和通訊群組清單與角色。  |
| **安全性群組** | 在群組宣告中發出使用者所屬的安全性群組 |
| **目錄角色** | 如果使用者被指派目錄角色，則會以 ' wids ' 宣告的形式發出，而不會發出群組宣告） |
| **指派給應用程式的群組** | 只發出明確指派給應用程式的群組，以及使用者的成員 |

例如，若要發出使用者所屬的所有安全性群組，請選取 [安全性群組]

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

若要使用同步自 Active Directory 的 Active Directory 屬性來發出群組，而不是 Azure AD Objectid，請從下拉式選單選取所需的格式。 只有從 Active Directory 同步處理的群組才會包含在宣告中。

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

若只要發出指派給應用程式的群組，請選取**指派給應用程式的群組**

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

指派給應用程式的群組將會包含在權杖中。  使用者所屬的其他群組將會被省略。  使用此選項時，不會包含 [嵌套群組]，而且使用者必須是指派給應用程式之群組的直接成員。

若要變更指派給應用程式的群組，請從 [**企業應用程式**] 清單中選取應用程式，然後按一下應用程式的左側導覽功能表中的 [**使用者和群組**]。

如需管理應用程式之群組指派的詳細資訊，請參閱將[使用者和群組指派給應用](../../active-directory/manage-apps/methods-for-assigning-users-and-groups.md#assign-groups)程式的檔方法。

### <a name="advanced-options"></a>進階選項

群組宣告的發出方式可由 [Advanced options] 底下的設定來修改。

自訂群組宣告的名稱：如果已選取，則可以為群組宣告指定不同的宣告類型。   在 [名稱] 欄位中輸入宣告類型，並在 [命名空間] 欄位中輸入宣告的選擇性命名空間。

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

某些應用程式需要群組成員資格資訊才會出現在「角色」宣告中。 您可以藉由勾選 [發出群組角色宣告] 方塊，選擇性地將使用者群組當做角色來發出。

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 如果使用 [將群組資料發出為角色] 選項，則只有群組會出現在角色宣告中。  指派給使用者的任何應用程式角色都不會出現在角色宣告中。

### <a name="edit-the-group-claims-configuration"></a>編輯群組宣告設定

一旦將群組宣告設定新增至 [使用者屬性] & 宣告設定，新增群組宣告的選項將會呈現灰色。 若要變更群組宣告設定，請按一下 [**其他宣告**] 清單中的群組宣告。

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>設定群組屬性的 Azure AD 應用程式註冊

群組宣告也可以在[應用程式資訊清單](../../active-directory/develop/reference-app-manifest.md)的[選擇性宣告](../../active-directory/develop/active-directory-optional-claims.md)區段中設定。

1. 在入口網站中 > Azure Active Directory-> 應用程式註冊-> 選取 [應用程式-> 資訊清單]

2. 藉由變更 groupMembershipClaim 來啟用群組成員資格宣告

有效值為：

| 選取項目 | 描述 |
|----------|-------------|
| **這** | 發出安全性群組、通訊群組清單和角色 |
| **SecurityGroup** | 在群組宣告中發出使用者所屬的安全性群組 |
| **「DirectoryRole** | 如果使用者被指派目錄角色，則會以 ' wids ' 宣告的形式發出，而不會發出群組宣告） |
| **「ApplicationGroup** | 只發出明確指派給應用程式的群組，以及使用者的成員 |

   例如：

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   根據預設，會在群組宣告值中發出群組 Objectid。  若要將宣告值修改為包含內部部署群組屬性，或將宣告類型變更為角色，請使用 OptionalClaims 設定，如下所示：

3. 設定組名 configuration 選擇性宣告。

   如果您想要讓權杖中的群組包含內部部署 AD 群組屬性，請在 [選擇性宣告] 區段中指定應套用哪一個權杖類型的選擇性宣告。  可以列出多個權杖類型：

   - OIDC 識別碼權杖的 idToken
   - OAuth/OIDC 存取權杖的 accessToken
   - SAML 權杖的 Saml2Token。

   > [!NOTE]
   > Saml2Token 類型同時適用于 SAML 1.1 和 SAML 2.0 格式權杖

   針對每個相關的權杖類型，修改群組宣告以使用資訊清單中的 OptionalClaims 區段。 OptionalClaims 架構如下所示：

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | 選擇性宣告架構 | 值 |
   |----------|-------------|
   | **檔案名** | 必須是「群組」 |
   | **來源** | 未使用。 省略或指定 null |
   | **基本** | 未使用。 省略或指定 false |
   | **AdditionalProperties** | 其他屬性的清單。  有效的選項為「sam_account_name」、「dns_domain_and_sam_account_name」、「netbios_domain_and_sam_account_name」、「emit_as_roles」 |

   在 additionalProperties 中，只需要「sam_account_name」、「dns_domain_and_sam_account_name」、「netbios_domain_and_sam_account_name」其中一個。  如果有多個，則會使用第一個，並忽略其他任何專案。

   某些應用程式需要角色宣告中的使用者群組資訊。  若要將宣告類型從群組宣告變更為角色宣告，請將 "emit_as_roles" 新增至其他屬性。  群組值會在角色宣告中發出。

   > [!NOTE]
   > 如果使用「emit_as_roles」，則設定為使用者指派的任何應用程式角色都不會出現在角色宣告中。

### <a name="examples"></a>範例

以 dnsDomainName\SAMAccountName 格式在 OAuth 存取權杖中以組名的形式發出群組

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

若要以 netbiosDomain\samAccountName 格式發出要傳回的組名作為 SAML 和 OIDC 識別碼權杖中的角色宣告：

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>後續步驟

[將使用者和群組指派給應用程式的方法](../../active-directory/manage-apps/methods-for-assigning-users-and-groups.md#assign-groups)

[設定角色宣告](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
