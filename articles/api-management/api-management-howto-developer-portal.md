---
title: Azure API 管理開發人員入口網站的總覽
titleSuffix: Azure API Management
description: 瞭解 API 管理中的開發人員入口網站。 開發人員入口網站可讓取用者找到您的 Api。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/28/2020
ms.author: apimpm
ms.openlocfilehash: 068f15cc06a76412836176dc13237741ea14d135
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614054"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API 管理開發人員入口網站概觀

開發人員入口網站是自動產生、可完全自訂的網站，內含 Api 的檔。 API 取用者可以在其中探索您的 Api、瞭解如何使用它們、要求存取權，以及試用。

本文說明「API 管理」中的「開發人員入口網站自我裝載」和「受控」版本之間的差異。 此外也會說明其架構，並提供常見問題的解答。

![API Management developer portal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> <a name="migrate-from-legacy"></a> 新的開發人員入口網站與舊版開發人員入口網站不相容，因此無法進行自動化的遷移。 您必須手動重新建立 (頁面、文字、媒體檔案) 的內容，並自訂新入口網站的外觀。 如需指引，請參閱 [開發人員入口網站教學](api-management-howto-developer-portal-customize.md) 課程。

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a> 受控和自我裝載版本

您可以透過兩種方式來建立開發人員入口網站：

- **受控版本** -藉由編輯和自訂入口網站（內建在您的 API 管理實例中），並可透過 URL 存取 `<your-api-management-instance-name>.developer.azure-api.net` 。 請參閱 [這篇檔文章](api-management-howto-developer-portal-customize.md) ，以瞭解如何存取和自訂受控入口網站。
- **自我裝載版本** -藉由在 API 管理實例外部部署和自我裝載您的入口網站。 這種方法可讓您編輯入口網站的程式碼基底，並擴充提供的核心功能，例如，執行自訂 widget 來與協力廠商系統整合。 在此案例中，您是入口網站的維護程式，而您必須負責將入口網站升級至最新版本。 如需詳細資訊和指示，請參閱 [GitHub 存放庫，其中包含入口網站的原始程式碼][1] 和 [執行 widget 的教學][3]課程。 [受控版本的教學](api-management-howto-developer-portal-customize.md)課程會逐步解說入口網站的系統管理面板，此面板常見於 managed 和自我裝載版本。

## <a name="portal-architectural-concepts"></a>入口網站架構概念

入口網站元件可以邏輯方式分成兩種類別：程式 *代碼* 和 *內容*。

程式*代碼*會保留在[GitHub 存放庫][1]中，並包含：

- Widget-代表視覺元素，並結合 HTML、JavaScript、樣式能力、設定和內容對應。 範例包括影像、文欄位落、表單、Api 清單等等。
- 樣式定義-指定如何設定 widget 的樣式
- 引擎-這會從入口網站內容產生靜態網頁，並以 JavaScript 撰寫
- 視覺化編輯器-允許瀏覽器內自訂和撰寫體驗

*內容* 分為兩個子類別： *入口網站內容* 和 *API 管理內容*。

*入口網站內容* 是入口網站專屬的內容，包括：

- 頁面-例如登陸頁面、API 教學課程、blog 文章
- 媒體-影像、動畫和其他以檔案為基礎的內容
- 版面配置-與 URL 比對的範本，並定義頁面的顯示方式
- 樣式-樣式定義的值，例如字型、色彩、框線
- 設定-設定，例如 favicon、網站中繼資料

*入口網站內容*（媒體除外）會表示為 JSON 檔。

*Api 管理內容* 包含 Api、作業、產品、訂用帳戶等實體。

入口網站是以 [Paperbits 架構](https://paperbits.io/)的調整分支為基礎。 原始的 Paperbits 功能已擴充，以提供 API 管理專屬的 widget (例如 Api 清單、) 的產品清單，以及用來儲存和抓取內容的 API 管理服務連接器。

## <a name="frequently-asked-questions"></a><a name="faq"></a> 常見問題集

在本節中，我們會回答有關開發人員入口網站的常見問題，這些都是一般性質。 如需自我裝載版本的特定問題，請參閱 [GitHub 存放庫的 wiki 區段](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> 如何從入口網站的預覽版本進行遷移？

當您第一次啟動開發人員入口網站的預覽版本時，您已在 API 管理服務中布建其預設內容的預覽版本。 已在正式推出的版本中大幅修改了預設內容。 例如，預覽版本的預設內容不會在登入頁面中包含 OAuth 按鈕，它會使用不同的 widget 來顯示 Api，並且依賴有限的功能來結構化開發人員入口網站頁面。 雖然內容有差異，但入口網站的引擎 (包括基礎 widget) 會在您每次發佈開發人員入口網站時自動更新。

如果您是根據預覽版本的內容而高度自訂入口網站，您可以繼續使用它，並在入口網站的頁面上手動放置新的小工具。 否則，建議您以新的預設內容取代您的入口網站內容。

若要在受控入口網站中重設內容，請按一下 [**作業**] 功能表區段中的 [**重設內容**]。 這項作業會移除入口網站的所有內容，並提供新的預設內容。 您將會遺失所有開發人員入口網站的自訂和變更。 **您無法復原此動作**。

![重設入口網站內容](media/api-management-howto-developer-portal/reset-content.png)

如果您使用自我裝載版本，請 `scripts.v2/cleanup.bat` `scripts.v2/generate.bat` 從 GitHub 存放庫執行和腳本，以移除現有的內容並布建新的內容。 請務必事先將您入口網站的程式碼升級至 GitHub 存放庫中的最新版本。

如果您在2019年11月的正式運作公告之後第一次存取入口網站，它應該已有新的預設內容，且不需要採取進一步的動作。

### <a name="does-the-portal-have-all-the-features-of-the-legacy-portal"></a>入口網站是否有舊版入口網站的所有功能？

開發人員入口網站已不再支援與 Facebook、Microsoft、Twitter 和 Google 的 *應用程式*、 *問題*和直接整合作為身分識別提供者 (您可以改用 Azure AD B2C 來) 。

### <a name="has-the-legacy-portal-been-deprecated"></a>舊版入口網站是否已淘汰？

舊版開發人員和發行者入口網站現在是 *舊版* 的功能，只會接收到安全性更新。 新的功能只會在新的開發人員入口網站中執行。

舊版入口網站的淘汰將會分開宣佈。 如果您有任何疑問、疑慮或意見，請 [在專屬的 GitHub 問題中](https://github.com/Azure/api-management-developer-portal/issues/121)提出。

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>入口網站不支援我所需的功能

您可以開啟 [功能要求](https://aka.ms/apimwish) ，或 [自行執行遺漏的功能][3]。 如果您自行實行此功能，您可以自行裝載開發人員入口網站，或在 GitHub 上開啟提取要求，以包含受管理版本中的變更。

### <a name="how-can-i-automate-portal-deployments"></a>如何自動化入口網站部署？

您可以透過 REST API，以程式設計方式存取和管理開發人員入口網站的內容，不論您使用的是受控或自我裝載版本。

此 API 記載于 [GitHub 存放庫的 wiki 區段][2]中。 它可用來自動在環境之間遷移入口網站內容，例如，從測試環境到實際執行環境。 您可以在 GitHub 上的 [這篇檔文章中](https://aka.ms/apimdocs/migrateportal) 深入瞭解此程式。

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>如何? 從受控版本移至自我裝載版本？

請參閱 [GitHub 上開發人員入口網站存放庫 Wiki 區段][2]中的詳細文章。

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>我可以在一個 API 管理服務中有多個開發人員入口網站嗎？

您可以有一個受控入口網站和多個自我裝載的入口網站。 所有入口網站的內容都會儲存在相同的 API 管理服務中，因此它們會是相同的。 如果您想要區分入口網站的外觀和功能，您可以使用自己的自訂小工具自我裝載，以在執行時間動態自訂頁面，例如根據 URL。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>入口網站支援 Azure Resource Manager 範本和/或相容于 API 管理 DevOps 資源套件嗎？

不會。

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>入口網站的內容是否以「API 管理」中的備份/還原功能儲存？

不會。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>我是否需要為受控入口網站相依性啟用額外的 VNet 連線？

在大部分情況下-否。

如果您的 API 管理服務在內部 VNet 中，則只能從網路記憶體取您的開發人員入口網站。 管理端點的主機名稱必須從您用來存取入口網站的系統管理介面的機器解析為服務的內部 VIP。 請確定已在 DNS 中註冊管理端點。 如果設定錯誤，您會看到錯誤： `Unable to start the portal. See if settings are specified correctly in the configuration (...)` 。

如果您的 API 管理服務在內部 VNet 中，而您是透過來自網際網路的應用程式閘道來存取它，請務必啟用與開發人員入口網站的連線，以及 API 管理的管理端點。

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>我已指派自訂 API 管理網域，且發佈的入口網站無法運作

更新網域之後，您必須重新 [發佈入口網站](api-management-howto-developer-portal-customize.md#publish) ，變更才會生效。

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>我已新增身分識別提供者，但在入口網站中看不到它

設定身分識別提供者之後 (例如，AAD、AAD B2C) ，您需要重新 [發佈入口網站](api-management-howto-developer-portal-customize.md#publish) ，變更才會生效。

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>我已設定委派，但入口網站未使用它

設定委派之後，您必須重新 [發佈入口網站](api-management-howto-developer-portal-customize.md#publish) ，變更才會生效。

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>我的其他 API 管理設定變更尚未傳播至開發人員入口網站

大部分的設定變更 (例如，VNet、登入和產品條款) 需要重新 [發佈入口網站](api-management-howto-developer-portal-customize.md#publish)。

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> 我在使用互動式主控台時遇到 CORS 錯誤

互動式主控台會向瀏覽器提出用戶端 API 要求。 藉由在您的 API (s) 上新增 [CORS 原則](api-management-cross-domain-policies.md#CORS) 來解決 cors 問題。

您可以在 Azure 入口網站中的 API 管理服務的 **入口網站總覽** 區段中，檢查 CORS 原則的狀態。 警告方塊表示原則不存在或設定不正確。

![API Management developer portal](media/api-management-howto-developer-portal/cors-azure-portal.png)

按一下 [ **啟用 cors** ] 按鈕，自動套用 cors 原則。

您也可以手動啟用 CORS。

1. 按一下 [以 **手動方式將它套用到全域層級** ] 連結，以查看產生的原則代碼。
2. 在 Azure 入口網站的 API 管理服務的 [ **api** ] 區段中，流覽至 [api] 區段中的**所有 api** 。
3. 按一下 **</>** [ **輸入處理** ] 區段中的圖示。
4. 在 XML 檔案的區段中插入原則 **<inbound>** 。 請確定 **<origin>** 值符合開發人員入口網站的網域。

> [!NOTE]
> 
> 如果您在產品範圍中套用 CORS 原則，而不是 API (的) 範圍內，而您的 API 使用透過標頭的訂用帳戶金鑰驗證，則您的主控台將無法運作。
>
> 瀏覽器會自動發出 OPTIONS HTTP 要求，但不包含具有訂用帳戶金鑰的標頭。 由於缺少訂用帳戶金鑰，API 管理無法將選項呼叫與產品建立關聯，因此無法套用 CORS 原則。
>
> 因應措施是，您可以在查詢參數中傳遞訂用帳戶金鑰。

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>我需要哪些許可權才能編輯開發人員入口網站？

如果您 `Oops. Something went wrong. Please try again later.` 在系統管理模式中開啟入口網站時看到錯誤，您可能缺少必要的許可權 (RBAC) 。

舊版入口網站需要 `Microsoft.ApiManagement/service/getssotoken/action` 服務範圍的許可權 (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) ，才能讓使用者系統管理員存取入口網站。 新的入口網站需要範圍的許可權 `Microsoft.ApiManagement/service/users/token/action` `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` 。

您可以使用下列 PowerShell 腳本來建立具有必要許可權的角色。 請記得變更 `<subscription-id>` 參數。 

```powershell
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
 
角色建立之後，就可以從 Azure 入口網站中的 **存取控制 (IAM) ** 區段授與給任何使用者。 將此角色指派給使用者，將會在服務範圍指派許可權。 使用者將能夠代表服務中的 *任何* 使用者產生 SAS 權杖。 至少需要將此角色指派給服務的系統管理員。 下列 PowerShell 命令示範如何在最低範圍將角色指派給使用者 `user1` ，以避免授與不必要的許可權給使用者： 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

授與許可權給使用者之後，使用者必須登出並再次登入 Azure 入口網站，新的許可權才會生效。

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>我看到 `Unable to start the portal. See if settings are specified correctly (...)` 錯誤

當呼叫失敗時，會顯示此錯誤 `GET` `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` 。 呼叫是由入口網站的系統管理介面由瀏覽器發出。

如果您的 API 管理服務位於 VNet 中，請參閱上述的 VNet 連線問題。

呼叫失敗也可能是因為 TLS/SSL 憑證（指派給自訂網域且不受瀏覽器信任）所造成。 作為緩和措施，您可以移除管理端點自訂網域-API 管理會切換回具有受信任憑證的預設端點。

### <a name="whats-the-browser-support-for-the-portal"></a>入口網站的瀏覽器支援為何？

| 瀏覽器                     | 支援       |
|-----------------------------|-----------------|
| Apple Safari                | 是<sup>1</sup> |
| Google Chrome               | 是<sup>1</sup> |
| Microsoft Edge              | 是<sup>1</sup> |
| Microsoft Internet Explorer | 否              |
| Mozilla Firefox             | 是<sup>1</sup> |

 <small><sup>1</sup> 兩個最新的生產版本支援。</small>

## <a name="next-steps"></a>接下來的步驟

深入瞭解新的開發人員入口網站：

- [存取和自訂受控開發人員入口網站](api-management-howto-developer-portal-customize.md)
- [設定入口網站的自我裝載版本][2]
- [執行您自己的 widget][3]

流覽其他資源：

- [具有原始程式碼的 GitHub 存放庫][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
