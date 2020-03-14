---
title: Azure API 管理開發人員入口網站的總覽
titleSuffix: Azure API Management
description: 深入瞭解 API 管理中的開發人員入口網站。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: b6b11242831e68787fe225d4d0b66638f1388de6
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297980"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API 管理開發人員入口網站總覽

開發人員入口網站是一個自動產生、可完全自訂的網站，其中包含您 Api 的檔。 API 取用者可以在此探索您的 Api、瞭解如何使用它們、要求存取權，以及如何試用。

本文說明「API 管理」中的開發人員入口網站自我裝載和受管理版本之間的差異。 它也會說明它的架構，並提供常見問題的解答。

![API Management developer portal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>受控和自我裝載版本

您可以透過兩種方式來建立開發人員入口網站：

- **受控版本**-藉由編輯和自訂入口網站（內建于您的 API 管理實例中），並可透過 `<your-api-management-instance-name>.developer.azure-api.net`的 URL 存取。 請參閱[此檔文章](api-management-howto-developer-portal-customize.md)，以瞭解如何存取和自訂受管理的入口網站。
- **自我裝載版本**-藉由在 API 管理實例外部部署和自我裝載您的入口網站。 這種方法可讓您編輯入口網站的程式碼基底，並擴充提供的核心功能，例如，為與協力廠商系統整合而執行自訂 widget。 在此案例中，您是入口網站的維護程式，而且您負責將入口網站升級至最新版本。 如需詳細資訊和指示，請參閱[GitHub 存放庫，其中包含入口網站的原始程式碼][1]和[執行 widget 的教學][3]課程。 [受控版本的教學](api-management-howto-developer-portal-customize.md)課程會逐步解說入口網站的系統管理面板，這是受控和自我裝載版本的常見程式。

## <a name="portal-architectural-concepts"></a>入口網站架構概念

入口網站元件可以邏輯方式分成兩個類別：程式*代碼*和*內容*。

程式*代碼*會保留在[GitHub 存放庫][1]中，並包含：

- Widget-代表視覺元素，並結合 HTML、JavaScript、樣式功能、設定和內容對應。 範例包括影像、文欄位落、表單、Api 清單等等。
- 樣式定義-指定 widget 如何設定樣式
- 引擎-這會從入口網站內容產生靜態網頁，並以 JavaScript 撰寫
- 視覺化編輯器-允許在瀏覽器內自訂和撰寫體驗

*內容*分為兩個子類別：*入口網站內容*和*API 管理內容*。

入口網站的*內容*專屬於入口網站，其中包括：

- 頁面-例如登陸頁面、API 教學課程、blog 文章
- 媒體-影像、動畫及其他檔案型內容
- 版面配置-範本，會與 URL 比對，並定義頁面的顯示方式
- 樣式-樣式定義的值，例如字型、色彩、框線
- 設定-例如 favicon、網站中繼資料

*入口網站內容*（媒體除外）會以 JSON 檔表示。

*Api 管理內容*包括 Api、作業、產品、訂用帳戶等實體。

入口網站是以[Paperbits 架構](https://paperbits.io/)的調整分支為基礎。 原始的 Paperbits 功能已擴充，可提供 API 管理特定的 widget （例如 Api 清單、產品清單），以及用於儲存和抓取內容的 API 管理服務連接器。

## <a name="faq"></a>常見問題

在本節中，我們會回答關於開發人員入口網站的常見問題，這些是一般性質的問題。 如需自我裝載版本的特定問題，請參閱[GitHub 存放庫的 wiki 一節](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> 如何從入口網站的預覽版本進行遷移？

藉由使用開發人員入口網站的預覽版本，您可以在 API 管理服務中布建預覽內容。 在正式推出版本中，已大幅修改預設內容以獲得更佳的使用者體驗。 它也包含新的小工具。

如果您使用的是受控版本，請按一下 [**作業**] 功能表區段中的 [**重設內容**]，重設入口網站的內容。 確認此操作將會移除入口網站的所有內容，並布建新的預設內容。 入口網站的引擎已自動在您的 API 管理服務中更新。

![重設入口網站內容](media/api-management-howto-developer-portal/reset-content.png)

如果您使用自我裝載的版本，請使用 GitHub 存放庫中的 `scripts/cleanup.bat` 和 `scripts/generate.bat` 來移除現有的內容，並布建新的內容。 請務必事先從 GitHub 存放庫將入口網站的程式碼升級至最新版本。

如果您不想重設入口網站的內容，可以考慮在整個頁面中使用新的小工具。 現有 widget 已自動更新為最新版本。

如果您的入口網站是在公開上市後公告之後布建，則它應該已有新的預設內容功能。 您的端不需要採取任何動作。

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>我要如何從舊的開發人員入口網站遷移到開發人員入口網站？

入口網站不相容，您必須手動遷移內容。

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>入口網站是否具有舊版入口網站的所有功能？

開發人員入口網站不再支援*應用程式*和*問題*。

尚不支援在互動式開發人員主控台中使用 OAuth 進行驗證。 您可以透過[GitHub 問題](https://github.com/Azure/api-management-developer-portal/issues/208)來追蹤進度。

### <a name="has-the-old-portal-been-deprecated"></a>舊的入口網站已被取代嗎？

舊的開發人員和發行者入口網站現在是*舊版*的功能-他們只會接收安全性更新。 新功能只會在新的開發人員入口網站中執行。

舊版入口網站的淘汰將會分開宣告。 如果您有任何疑問、疑慮或意見，請[在專屬的 GitHub 問題中](https://github.com/Azure/api-management-developer-portal/issues/121)提出。

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>入口網站不支援我所需的功能

您可以開啟[功能要求](https://aka.ms/apimwish)，或[自行執行遺漏的功能][3]。 如果您自行執行此功能，您可以自行裝載開發人員入口網站，或在 GitHub 上開啟提取要求，以包含受控版本中的變更。

### <a name="how-can-i-automate-portal-deployments"></a>如何自動進行入口網站部署？

您可以透過 REST API 以程式設計方式存取和管理開發人員入口網站的內容，不論您使用的是受控或自我裝載版本。

此 API 記載于[GitHub 存放庫的 wiki 一節][2]。 它可以用來自動化環境之間的入口網站內容遷移-例如，從測試環境到生產環境。 您可以在 GitHub 上的[此檔文章中](https://aka.ms/apimdocs/migrateportal)深入瞭解此程式。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>入口網站是否支援 Azure Resource Manager 範本和（或）是否與 API 管理 DevOps 資源套件相容？

否。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>我是否需要針對受管理的入口網站相依性啟用額外的 VNet 連線？

在大部分情況下-[否]。

如果您的 API 管理服務位於內部 VNet 中，您的開發人員入口網站只能從網路記憶體取。 管理端點的主機名稱必須從您用來存取入口網站系統管理介面的電腦解析為服務的內部 VIP。 請確定管理端點已在 DNS 中註冊。 在設定錯誤的情況下，您會看到錯誤： `Unable to start the portal. See if settings are specified correctly in the configuration (...)`。

如果您的 API 管理服務位於內部 VNet 中，而您是透過來自網際網路的應用程式閘道來存取它，請務必啟用與開發人員入口網站的連線，以及 API 管理的管理端點。

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>我已指派自訂 API 管理網域，而已發佈的入口網站無法使用

更新網域之後，您必須重新[發佈入口網站](api-management-howto-developer-portal-customize.md#publish)，變更才會生效。

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>我已新增身分識別提供者，但在入口網站中看不到它

設定身分識別提供者（例如 AAD、AAD B2C）之後，您必須重新[發佈入口網站](api-management-howto-developer-portal-customize.md#publish)，變更才會生效。

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>我已設定委派，但入口網站並未使用它

設定委派之後，您必須重新[發佈入口網站](api-management-howto-developer-portal-customize.md#publish)，變更才會生效。

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>我的其他 API 管理設定變更尚未在開發人員入口網站中傳播

大部分的設定變更（例如，VNet、登入和產品條款）都需要重新[發佈入口網站](api-management-howto-developer-portal-customize.md#publish)。

### <a name="cors"></a>我在使用互動式主控台時遇到 CORS 錯誤

互動式主控台會從瀏覽器提出用戶端 API 要求。 您可以藉由在您的 API 上新增[cors 原則](api-management-cross-domain-policies.md#CORS)來解決 CORS 問題。 您可以手動指定所有參數，或使用萬用字元 `*` 值。 例如：

```XML
<cors allow-credentials="true">
    <allowed-origins>
        <origin>https://contoso.com</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>*</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

在全域範圍上套用 CORS，以確保它已針對所有 Api 啟用。

1. 在 Azure 入口網站的 API 管理服務的 [ **api** ] 區段中，流覽至 [**所有 api** ]。
2. 在 [**輸入處理**] 區段中，按一下 [ **</>** ] 圖示。
3. 將原則插入 XML 檔案的 **<inbound>** 區段中。 請確定 **<origin>** 值符合開發人員入口網站的網域。

> [!NOTE]
> 
> 如果您在產品範圍中套用 CORS 原則，而不是在 API 範圍內套用，而您的 API 會透過標頭使用訂用帳戶金鑰驗證，則您的主控台將無法使用。
>
> 瀏覽器會自動發出選項 HTTP 要求，其中未包含含有訂用帳戶金鑰的標頭。 由於遺漏了訂用帳戶金鑰，因此 API 管理無法將選項呼叫與產品產生關聯，因此無法套用 CORS 原則。
>
> 因應措施是，您可以在查詢參數中傳遞訂用帳戶金鑰。

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>編輯開發人員入口網站需要哪些許可權？

如果您在系統管理模式中開啟入口網站時看到 `Oops. Something went wrong. Please try again later.` 錯誤，您可能缺少必要的許可權（RBAC）。

舊版入口網站需要在服務範圍（`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`） `Microsoft.ApiManagement/service/getssotoken/action` 的許可權，以允許使用者系統管理員存取入口網站。 新的入口網站需要 `Microsoft.ApiManagement/service/users/token/action` 範圍 `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`的許可權。

您可以使用下列 PowerShell 腳本來建立具有必要許可權的角色。 請記得變更 `<subscription-id>` 參數。 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
角色建立之後，就可以從 Azure 入口網站的 [**存取控制（IAM）** ] 區段中，授與給任何使用者。 將此角色指派給使用者，將會在服務範圍指派許可權。 使用者將能夠代表服務中的*任何*使用者產生 SAS 權杖。 此角色至少須指派給服務的系統管理員。 下列 PowerShell 命令示範如何將角色指派給使用者 `user1` 在最低範圍，以避免將不必要的許可權授與使用者： 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

授與使用者許可權之後，使用者必須登出再重新登入 Azure 入口網站，新的許可權才會生效。

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>我看到 `Unable to start the portal. See if settings are specified correctly (...)` 錯誤

當 `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` 的 `GET` 呼叫失敗時，就會顯示此錯誤。 呼叫是由入口網站的系統管理介面從瀏覽器發出。

如果您的 API 管理服務位於 VNet 中，請參閱上述的 VNet 連線問題。

呼叫失敗也可能是因為 SSL 憑證已指派至自訂網域，而不受瀏覽器信任。 作為緩和措施，您可以移除管理端點自訂網域-API 管理會切換回具有受信任憑證的預設端點。

### <a name="whats-the-browser-support-for-the-portal"></a>入口網站的瀏覽器支援為何？

| 瀏覽器                     | 支援       |
|-----------------------------|-----------------|
| Apple Safari                | 是<sup>1</sup> |
| Google Chrome               | 是<sup>1</sup> |
| Microsoft Edge              | 是<sup>1</sup> |
| Microsoft Internet Explorer | 否              |
| Mozilla Firefox             | 是<sup>1</sup> |

 <small><sup>1</sup>在兩個最新的生產版本中都有支援。</small>

## <a name="next-steps"></a>後續步驟

深入瞭解新的開發人員入口網站：

- [存取和自訂受管理的開發人員入口網站](api-management-howto-developer-portal-customize.md)
- [設定入口網站的自我裝載版本][2]
- [執行您自己的小工具][3]

流覽其他資源：

- [具有原始程式碼的 GitHub 存放庫][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend