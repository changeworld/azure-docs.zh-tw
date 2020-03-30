---
title: 為具有 Azure 活動目錄的應用程式佈建組聲明 |微軟文檔
description: 有關如何配置組聲明以便與 Azure AD 一起使用的資訊。
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
ms.openlocfilehash: b8708aec1137836516852135412c4c7cec2feba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408397"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>為具有 Azure 活動目錄的應用程式佈建組聲明（公共預覽）

Azure 活動目錄可以為使用者提供權杖中的組成員身份資訊，以便在應用程式中使用。  支援兩種主要模式：

- 由其 Azure 活動目錄物件識別碼 （OID） 屬性標識的組（一般可用）
- 由 sAMAccountName 或"組SID"屬性標識的活動目錄 （AD） 同步組和使用者（公共預覽）

> [!IMPORTANT]
> 對於此預覽功能，需要注意許多注意事項：
>
>- 支援使用從本地同步的 sAMAccountName 和安全識別碼 （SID） 屬性，旨在啟用從 AD FS 和其他標識提供程式移動現有應用程式。 在 Azure AD 中管理的組不包含發出這些聲明所需的屬性。
>- 在較大的組織中，使用者作為其成員的組數可能超過 Azure 活動目錄將添加到權杖的限制。 SAML 權杖的 150 個組和 JWT 的 200 個組。 這可能導致不可預知的結果。 如果使用者擁有大量組成員身份，我們建議使用選項將聲明中發出的組限制為應用程式的相關組。  
>- 對於新的應用程式開發，或者在可以為其配置應用程式以及不需要嵌套組支援的情況下，我們建議應用內授權基於應用程式角色而不是組。  這將限制需要進入權杖的資訊量，更安全，並將使用者分配與應用配置分開。

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>組從 AD FS 和其他標識提供程式遷移的應用程式的聲明

許多配置為使用 AD FS 進行身份驗證的應用程式依賴于 Windows AD 組屬性形式的組成員身份資訊。   這些屬性是組 sAMAccountName（可能是限定的子功能變數名稱）或 Windows 組安全識別碼 （GroupSID）。  當應用程式與 AD FS 聯合時，AD FS 使用權杖組函數檢索使用者的組成員身份。

從 AD FS 移動的應用需要以相同格式的聲明。 組和角色聲明可能從 Azure 活動目錄中發出，其中包含域限定的 sAMAccountName 或從活動目錄而不是組的 Azure 活動目錄物件識別碼 同步的 GroupSID。

組聲明的支援格式包括：

- **Azure 活動目錄組物件 Id（** 適用于所有組）
- **sAMAccount名稱**（適用于從活動目錄同步的組）
- **NetbiosDomain\sAMAccount名稱**（適用于從活動目錄同步的組）
- **DNS功能變數名稱\sAM帳戶名稱**（適用于從活動目錄同步的組）
- **在本機群組安全識別碼**（適用于從活動目錄同步的組）

> [!NOTE]
> sAMAccountName 和本機群組 SID 屬性僅在從活動目錄同步的組物件上可用。   它們不適用於在 Azure 活動目錄或 Office365 中創建的組。   在 Azure 活動目錄中配置為同步的本機群組屬性的應用程式僅為同步組獲取它們。

## <a name="options-for-applications-to-consume-group-information"></a>應用程式使用組資訊的選項

應用程式可以調用 MS Graph 組終結點以獲取經過身份驗證的使用者的組資訊。 此調用可確保使用者是 其成員的所有組都可用，即使涉及大量組也是如此。  然後，組枚舉獨立于權杖大小限制。

但是，如果現有應用程式希望通過聲明使用組資訊，則可以使用許多不同的聲明格式配置 Azure 活動目錄。  請考慮下列選項：

- 在將組成員身份用於應用程式內授權時，最好使用組 ObjectID。 組 ObjectID 不可變且在 Azure 活動目錄中唯一，適用于所有組。
- 如果使用本機群組 sAMAccountName 進行授權，請使用具有域限定的名稱;如果使用本機群組 sAMAccountName 進行授權，請使用具有域限定的名稱; 名字發生衝突的可能性較小。 sAMAccountName 在 Active Directory 域中可能是唯一的，但如果多個活動目錄域與 Azure 活動目錄租戶同步，則多個組可能具有相同的名稱。
- 請考慮使用[應用程式角色](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)在組成員身份和應用程式之間提供一層間接。   然後，應用程式根據權杖中的角色蛤做出內部授權決策。
- 如果應用程式佈建為獲取從 Active Directory 同步的組屬性，並且組不包含這些屬性，則聲明中不會包含該屬性。
- 權杖中的組聲明包括嵌套組，除非使用選項將組聲明限制為分配給應用程式的組。  如果使用者是 GroupB 的成員，而 GroupB 是 GroupA 的成員，則該使用者的組聲明將同時包含 GroupA 和 GroupB。 當組織的使用者擁有大量組成員身份時，權杖中列出的組數可能會增加權杖大小。  Azure 活動目錄將它在權杖中發出的 SAML 斷言中的組數限制為 150，JWT 將限制為 200 個。  如果使用者是更多組的成員，則省略組，並改為包含指向 Graph 終結點以獲取組資訊的連結。

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>使用從活動目錄同步的組屬性的先決條件

如果使用 ObjectId 格式，則可以以任何組的權杖形式發出組成員聲明。 要以組 ObjectId 以外的格式使用組聲明，必須使用 Azure AD 連接從活動目錄同步組。

配置 Azure 活動目錄以發出活動目錄組的組名稱有兩個步驟。

1. **從活動目錄同步組名稱**在 Azure 活動目錄可以在組或角色聲明中發出組名稱或內部部署組 SID 之前，需要從活動目錄同步所需的屬性。  您必須運行 Azure AD 連接版本 1.2.70 或更高版本。   早期版本的 Azure AD Connect 比 1.2.70 將同步來自活動目錄的組物件，但不會包括所需的組名稱屬性。  升級到當前版本。

2. **在 Azure 活動目錄中配置應用程式註冊以在權杖中包括組聲明**組聲明可以在門戶的企業應用程式部分中配置，也可以在應用程式註冊部分中使用應用程式清單。  要在應用程式清單中配置組聲明，請參閱下面的"配置組屬性的 Azure 活動目錄應用程式註冊"。

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>使用 SSO 配置將組聲明添加到 SAML 應用程式的權杖

要為庫或非庫 SAML 應用程式佈建組聲明，請打開**企業應用程式**，按一下清單中的應用程式，選擇**單一登入配置**，然後選擇 **"使用者屬性&聲明**"。

按一下"**添加組聲明**"  

![聲明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

使用選項按鈕選擇權杖中應包含哪些組

![聲明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 選取項目 | 描述 |
|----------|-------------|
| **所有群組** | 發出安全性群組、通訊群組清單和角色。  |
| **安全性群組** | 發出使用者是組聲明的成員的安全性群組 |
| **目錄角色** | 如果使用者被分配了目錄角色，則這些角色將作為"wids"聲明發出（不會發出組聲明） |
| **分配給應用程式的組** | 僅發出顯式分配給應用程式的組，使用者是 |

例如，要發出使用者是的所有安全性群組，請選擇安全性群組

![聲明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

要使用從活動目錄而不是 Azure AD 物件 IT 同步的活動目錄屬性發出組，請從下拉清單中選擇所需的格式。 聲明中僅包括從活動目錄同步的組。

![聲明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

要僅發出分配給應用程式的組，請選擇**分配給應用程式的組**

![聲明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

分配給應用程式的組將包含在權杖中。  使用者是其成員的其他組將被省略。  使用此選項，不包括嵌套組，使用者必須是分配給應用程式的組的直接成員。

要更改分配給應用程式的組，請從 **"企業應用程式"** 清單中選擇應用程式，然後按一下應用程式左側導航功能表中的 **"使用者和組**"。

有關管理組分配到應用程式的詳細資訊，請參閱文檔[將使用者或組分配給企業應用](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)。

### <a name="advanced-options"></a>進階選項

可以通過"高級"選項下的設置修改組聲明的發出方式

自訂群組聲明的名稱：如果選中，可以為組聲明指定不同的聲明類型。   在"名稱"欄位中輸入聲明類型，在命名空間欄位中輸入聲明的可選命名空間。

![聲明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

某些應用程式要求組成員身份資訊顯示在"角色"索賠中。 通過選中"Emit 對角色聲明的 Emit"框，可以選擇將使用者的組作為角色發出。

![聲明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 如果使用角色發出組資料的選項，則角色聲明中只會顯示組。  使用者分配的任何應用程式角色將不會顯示在角色聲明中。

### <a name="edit-the-group-claims-configuration"></a>編輯組聲明配置

將組聲明配置添加到使用者屬性&聲明配置後，添加組聲明的選項將灰顯。 要更改組聲明配置，請按一下 **"其他索賠**"清單中的組聲明。

![聲明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>為組屬性配置 Azure AD 應用程式註冊

組聲明也可以在[應用程式清單](../../active-directory/develop/reference-app-manifest.md)的[可選聲明](../../active-directory/develop/active-directory-optional-claims.md)部分進行配置。

1. 在門戶中 ->Azure 活動目錄 ->應用程式註冊->選擇應用程式->清單

2. 通過更改組成員聲明啟用組成員聲明

有效值為：

| 選取項目 | 描述 |
|----------|-------------|
| **"全部"** | 發出安全性群組、通訊群組清單和角色 |
| **"安全性群組"** | 發出使用者是組聲明的成員的安全性群組 |
| **"目錄角色** | 如果使用者被分配了目錄角色，則這些角色將作為"wids"聲明發出（不會發出組聲明） |
| **"應用程式組** | 僅發出顯式分配給應用程式的組，使用者是 |

   例如：

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   預設情況下，組物件 I 將在組聲明值中發出。  要修改聲明值以包含本機群組屬性，或將聲明類型更改為角色，請使用"可選索賠"配置，如下所示：

3. 設置組名稱配置可選聲明。

   如果希望權杖中的組包含本地 AD 組屬性，請在可選宣告區段中指定應應用於哪個權杖類型可選聲明。  可以列出多種權杖類型：

   - OIDC ID 權杖的 idToken
   - OAuth/OIDC 訪問權杖的訪問權杖
   - SAML 權杖的 Saml2 權杖。

   > [!NOTE]
   > Saml2Token 類型適用于 SAML1.1 和 SAML2.0 格式權杖

   對於每個相關的權杖類型，修改組聲明以在清單中使用"可選聲明"部分。 可選聲明架構如下所示：

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | 可選聲明架構 | 值 |
   |----------|-------------|
   | **名字：** | 必須是"組" |
   | **源：** | 未使用。 省略或指定 null |
   | **基本：** | 未使用。 省略或指定 false |
   | **其他屬性：** | 其他屬性的清單。  有效選項為"sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name"、"emit_as_roles" |

   在附加屬性中，只需要"sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name"之一。  如果存在多個，則使用第一個，忽略任何其他。

   某些應用程式需要有關角色聲明中的使用者的組資訊。  要將聲明類型從組聲明更改為角色聲明，向其他屬性添加"emit_as_roles"。  將在角色聲明中發出組值。

   > [!NOTE]
   > 如果使用"emit_as_roles"，則配置分配給使用者的任何應用程式角色將不會出現在角色聲明中

### <a name="examples"></a>範例

以 dnsDomainName_SAMAccountName 格式在 OAuth 訪問權杖中將組名稱作為組名稱發出

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

要發出要以 netbiosDomain_samAccountName 格式返回的組名稱，作為 SAML 和 OIDC ID 權杖中的角色聲明：

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

[將使用者或群組指派給企業應用程式](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

[設定角色宣告](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
