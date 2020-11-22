---
title: 使用 Azure Active Directory 設定應用程式的群組宣告 |Microsoft Docs
description: 有關如何設定群組宣告以搭配 Azure AD 使用的資訊。
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: cb828eeb408a170b93ffc73b58f14b3f7a883cc4
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2020
ms.locfileid: "95247229"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>使用 Azure Active Directory 設定應用程式的群組宣告

Azure Active Directory 可以在權杖中提供使用者群組成員資格資訊，以便在應用程式中使用。  支援兩種主要模式：

- Azure Active Directory 物件識別碼 (OID) 屬性所識別的群組
- Active Directory (AD) 同步處理的群組和使用者的 sAMAccountName 或 GroupSID 屬性所識別的群組

> [!IMPORTANT]
> 這項功能有一些值得注意的事項：
>
>- 支援使用 sAMAccountName 和安全性識別元 (SID) 屬性從內部部署同步處理的目的，是為了讓您能夠從 AD FS 和其他身分識別提供者移動現有的應用程式。 在 Azure AD 中管理的群組不包含發出這些宣告所需的屬性。
>- 在大型組織中，使用者所屬的群組數目可能會超過 Azure Active Directory 將新增至權杖的限制。 150適用于 SAML 權杖的群組，以及適用于 JWT 的200。 這可能會導致無法預期的結果。 如果您的使用者有大量的群組成員資格，建議您使用選項，將宣告中發出的群組限制為應用程式的相關群組。  
>- 若為新的應用程式開發，或可為其設定應用程式的情況，以及不需要嵌套群組支援的情況下，我們建議應用程式內授權是以應用程式角色為基礎，而不是群組。  這會限制必須移入權杖的資訊數量、更安全，以及將使用者指派與應用程式設定隔開。

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>從 AD FS 和其他身分識別提供者遷移的應用程式群組宣告

許多設定為使用 AD FS 進行驗證的應用程式，都會依賴 Windows AD 群組屬性的格式的群組成員資格資訊。   這些屬性是群組 sAMAccountName，可依功能變數名稱或 Windows 群組安全識別碼 (GroupSID) 。  當應用程式與 AD FS 同盟時，AD FS 會使用 TokenGroups 函式來取得使用者的群組成員資格。

已從 AD FS 移出的應用程式需要相同格式的宣告。 群組和角色宣告可能會從包含網域合格 sAMAccountName 的 Azure Active Directory，或從 Active Directory （而不是從群組的 Azure Active Directory objectID）同步處理的 GroupSID 發出。

支援的群組宣告格式如下：

- **Azure Active Directory 群組 ObjectId** (適用于所有群組) 
- **sAMAccountName** (可用於從 Active Directory 同步處理的群組) 
- **NetbiosDomain\sAMAccountName** (適用于從 Active Directory) 同步的群組
- **DNSDomainName\sAMAccountName** (適用于從 Active Directory) 同步的群組
- **內部部署群組安全識別碼** (可用於從 Active Directory 同步處理的群組) 

> [!NOTE]
> sAMAccountName 和內部部署群組 SID 屬性僅適用于從 Active Directory 同步的群組物件。   它們無法用於 Azure Active Directory 或 Office365 中建立的群組。   在 Azure Active Directory 中設定來取得同步內部部署群組屬性的應用程式只會將其用於已同步處理的群組。

## <a name="options-for-applications-to-consume-group-information"></a>使用群組資訊的應用程式選項

應用程式可以呼叫 MS Graph 群組端點，以取得已驗證使用者的群組資訊。 此呼叫可確保即使涉及大量群組，使用者所屬的所有群組仍可供使用。  群組列舉接著會獨立于權杖大小限制之外。

但是，如果現有的應用程式預期透過宣告取用群組資訊，Azure Active Directory 可以使用數種不同的宣告格式來設定。  請考慮下列選項：

- 使用群組成員資格進行應用程式內授權時，最好使用群組 ObjectID。 群組 ObjectID 是不可變的，且在 Azure Active Directory 中是唯一的，而且適用于所有群組。
- 如果使用內部部署群組 sAMAccountName 進行授權，請使用網域限定名稱; 名稱的機率較低衝突。 sAMAccountName 在 Active Directory 網域內可能是唯一的，但如果有一個以上的 Active Directory 網域與 Azure Active Directory 租使用者同步，則可能有一個以上的群組具有相同的名稱。
- 請考慮使用 [應用程式角色](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) ，以提供群組成員資格和應用程式之間的間接取值層。   然後，應用程式會根據權杖中的角色 clams 來進行內部授權決策。
- 如果應用程式設定為取得從 Active Directory 同步處理的群組屬性，而群組未包含這些屬性，則宣告中將不會包含該屬性。
- 除非使用選項將群組宣告限制為指派給應用程式的群組，否則權杖中的群組宣告會包含嵌套群組。  如果使用者是 GroupB 的成員，而 GroupB 是 GroupA 的成員，則使用者的群組宣告會包含 GroupA 和 GroupB。 當組織的使用者有大量的群組成員資格時，權杖中列出的群組數目可能會成長權杖大小。  Azure Active Directory 會將在權杖中發出的群組數目限制為 SAML 判斷提示的150和200（JWT）。  如果使用者是更大量群組的成員，則會省略這些群組，而是會包含圖形端點的連結以取得群組資訊。

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>使用從 Active Directory 同步處理群組屬性的必要條件

如果您使用 ObjectId 格式，就可以在任何群組的權杖中發出群組成員資格宣告。 若要在群組 ObjectId 以外的格式中使用群組宣告，必須使用 Azure AD Connect 從 Active Directory 同步處理群組。

有兩個步驟可設定 Azure Active Directory 來發出 Active Directory 群組的組名。

1. **從 Active Directory 同步處理組名** 在 Azure Active Directory 可以發出群組或角色宣告中的組名或內部部署群組 SID 之前，需要從 Active Directory 同步處理必要的屬性。  您必須執行 Azure AD Connect 1.2.70 版或更新版本。   比1.2.70 舊版的 Azure AD Connect 將會從 Active Directory 同步處理群組物件，但不會包含必要的組名屬性。  升級至目前的版本。

2. **在 Azure Active Directory 中設定應用程式註冊，以在權杖中包含群組宣告** 您可以在入口網站的 [企業應用程式] 區段中，或在 [應用程式註冊] 區段中使用應用程式資訊清單來設定群組宣告。  若要在應用程式資訊清單中設定群組宣告，請參閱下面的「設定群組屬性的 Azure Active Directory 應用程式註冊」。

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>使用 SSO 設定將群組宣告新增至 SAML 應用程式的權杖

若要設定資源庫或非資源庫 SAML 應用程式的群組宣告，請開啟 [ **企業應用程式**]，按一下清單中的應用程式，選取 [ **單一登入** 設定]，然後選取 [ **使用者屬性 & 宣告**]。

按一下 [**新增群組** 宣告]  

![顯示已選取 [新增群組宣告] 的 [使用者屬性 & 宣告] 頁面的螢幕擷取畫面。](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

使用選項按鈕選取哪些群組應該包含在權杖中

![螢幕擷取畫面，顯示已選取 [安全性群組] 的 [群組宣告] 視窗。](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 選取項目 | 描述 |
|----------|-------------|
| **所有群組** | 發出安全性群組和通訊群組清單和角色。  |
| **安全性群組** | 在群組宣告中發出使用者所屬的安全性群組 |
| **目錄角色** | 如果使用者被指派目錄角色，則會以 ' wids ' 宣告的形式發出， (不會發出群組宣告)  |
| **指派給應用程式的群組** | 只發出明確指派給應用程式且使用者為其成員的群組 |

例如，若要發出使用者所屬的所有安全性群組，請選取安全性群組

![顯示 [群組宣告] 視窗的螢幕擷取畫面，其中已選取 [安全性群組]，並開啟 [來源屬性] 下拉式功能表。](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

若要使用從 Active Directory 同步的 Active Directory 屬性（而非 Azure AD Objectid）發出群組，請從下拉式清單中選取所需的格式。 宣告中只會包含從 Active Directory 同步處理的群組。

![顯示 [來源屬性] 下拉式功能表開啟的螢幕擷取畫面。](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

若只要發出指派給應用程式的群組，請選取 **指派給應用程式的群組**

![顯示 [群組宣告] 視窗的螢幕擷取畫面，其中已選取 [指派給應用程式的群組]。](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

指派給應用程式的群組會包含在權杖中。  使用者所屬的其他群組將會被忽略。  若未包含這個選項的嵌套群組，使用者必須是指派給應用程式之群組的直接成員。

若要變更指派給應用程式的群組，請從 [ **企業應用程式** ] 清單中選取該應用程式，然後從應用程式的左側導覽功能表中按一下 [ **使用者和群組** ]。

請參閱檔 [將使用者或群組指派給企業應用程式](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) ，以取得管理應用程式群組指派的詳細資料。

### <a name="advanced-options"></a>進階選項

群組宣告的發出方式可由 [Advanced options] 下的設定修改

自訂群組宣告的名稱：若選取此選項，可以為群組宣告指定不同的宣告類型。   在 [名稱] 欄位中輸入宣告類型，並在 [命名空間] 欄位中輸入宣告的選擇性命名空間。

![顯示 [Advanced options] 區段的螢幕擷取畫面，其中已選取 [自訂群組宣告的名稱] 以及輸入的「名稱」和「命名空間」值。](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

某些應用程式需要群組成員資格資訊才會出現在「角色」宣告中。 您可以藉由核取 [發出群組的角色宣告] 方塊，選擇性地以角色形式發出使用者的群組。

![顯示 [Advanced options] 區段的螢幕擷取畫面，其中包含 [自訂群組宣告的名稱] 和 [發出群組作為角色宣告]。](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 如果使用選項來發出群組資料作為角色，則只有群組會出現在角色宣告中。  指派給使用者的任何應用程式角色都不會出現在角色宣告中。

### <a name="edit-the-group-claims-configuration"></a>編輯群組宣告設定

一旦將群組宣告設定新增至使用者屬性 & 宣告設定之後，新增群組宣告的選項將會呈現灰色。 若要變更群組宣告設定，請按一下 [ **其他宣告** ] 清單中的群組宣告。

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>設定群組屬性的 Azure AD 應用程式註冊

您也可以在[應用程式資訊清單](../../active-directory/develop/reference-app-manifest.md)的[選擇性宣告](../../active-directory/develop/active-directory-optional-claims.md)區段中設定群組宣告。

1. 在入口網站中 >Azure Active Directory-> 應用程式註冊->選取應用程式->資訊清單

2. 藉由變更 groupMembershipClaim 來啟用群組成員資格宣告

有效值為：

| 選取項目 | 描述 |
|----------|-------------|
| **All** | 發出安全性群組、通訊群組清單和角色 |
| **"SecurityGroup"** | 在群組宣告中發出使用者所屬的安全性群組 |
| **"DirectoryRole"** | 如果使用者被指派目錄角色，則會以 ' wids ' 宣告的形式發出， (不會發出群組宣告)  |
| **ApplicationGroup** | 只發出明確指派給應用程式且使用者為其成員的群組 |
| **以上** | 不會傳回任何群組。 (不 sensetive 的情況下，也不會有任何作用，而且可以直接在應用程式資訊清單中設定。 )  |

   例如：

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   預設會在群組宣告值中發出群組 ObjectID。  若要將宣告值修改為包含內部部署群組屬性，或將宣告類型變更為角色，請使用 OptionalClaims 設定，如下所示：

3. 設定群組名稱設定的選擇性宣告。

   如果您想要權杖中的群組包含內部部署 AD 群組屬性，請在選用宣告區段中指定應套用選用宣告的權杖類型。  可以列出多個權杖類型：

   - idToken，代表 OIDC 識別碼權杖
   - OAuth/OIDC 存取權杖的 accessToken
   - Saml2Token，代表 SAML 權杖。

   > [!NOTE]
   > Saml2Token 類型適用於 SAML1.1 和 SAML2.0 格式的權杖

   針對每個相關的權杖類型，將群組宣告修改為使用資訊清單中的 OptionalClaims 區段。 OptionalClaims 結構描述如下所示：

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
   | **name:** | 必須是 "groups" |
   | **source:** | 未使用。 省略或指定 null |
   | **essential:** | 未使用。 省略或指定 false |
   | **additionalProperties:** | 額外屬性的清單。  有效的選項為「sam_account_name」、「dns_domain_and_sam_account_name」、「netbios_domain_and_sam_account_name」、「emit_as_roles」 |

   在 additionalProperties 中，只需要一個 "sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name"。  如果出現多個，則會使用第一個，其他都忽略。

   某些應用程式在角色宣告中需要使用者的群組資訊。  若要將宣告類型從群組宣告變更為角色宣告，請將 "emit_as_roles" 新增至其他屬性。  角色宣告中會發出群組值。

   > [!NOTE]
   > 如果使用 "emit_as_roles"，則任何已設定指派給使用者的應用程式角色，將不會出現在角色宣告中。

### <a name="examples"></a>範例

以 dnsDomainName\SAMAccountName 格式將群組以 OAuth 存取權杖中的組名形式發出

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

發出以 netbiosDomain\samAccountName 格式傳回的組名，作為 SAML 和 OIDC 識別碼權杖中的角色宣告：

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

- [使用群組新增授權 & 將宣告新增至 ASP.NET Core web 應用程式 (程式碼範例) ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [將使用者或群組指派給企業應用程式](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [設定角色宣告](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
