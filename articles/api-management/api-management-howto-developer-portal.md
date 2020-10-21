---
title: Azure API 管理中的開發人員入口網站總覽
titleSuffix: Azure API Management
description: 瞭解 API 管理中的開發人員入口網站-可自訂的網站，API 取用者可以在其中探索您的 Api。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 30a6a73768db7b073258487435ddbe6c0daccf16
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92317828"
---
# <a name="overview-of-the-developer-portal"></a>開發人員入口網站總覽

開發人員入口網站是自動產生、可完全自訂的網站，內含 Api 的檔。 API 取用者可以在其中探索您的 Api、瞭解如何使用它們、要求存取權，以及試用。

本文說明「API 管理」中的「開發人員入口網站自我裝載」和「受控」版本之間的差異。 也提供常見問題的解答。

![API Management developer portal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>從舊版入口網站遷移

> [!IMPORTANT]
> 舊版開發人員入口網站現已淘汰，而且只會收到安全性更新。 您可以繼續使用它，就像平常一樣，直到2023年10月淘汰為止（當它從所有 API 管理服務中移除）。

[專用檔文章](developer-portal-deprecated-migration.md)中會說明遷移至新的開發人員入口網站。

## <a name="customization-and-styling"></a>自訂和樣式

開發人員入口網站可以透過內建的拖放視覺效果編輯器來自訂和樣式。 如需詳細資訊，請參閱 [此教學](api-management-howto-developer-portal-customize.md) 課程。

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> 擴充性

您的 API 管理服務包含內建、隨時處於最新狀態的 **受管理** 開發人員入口網站。 您可以從 Azure 入口網站介面進行存取。

如果您需要使用不支援的自訂邏輯來擴充它，您可以修改其程式碼基底。 入口網站的程式碼基底 [可在 GitHub 存放庫中][1]取得。 例如，您可以執行與協力廠商支援系統整合的新 widget。 當您執行新功能時，您可以選擇下列其中一個選項：

- 將產生的入口網站**自我裝載**在您的 API 管理服務外部。 當您自行裝載入口網站時，您會成為其維護程式，並負責其升級。 Azure 支援的協助僅限於自我裝載入口網站的基本設定，如存放 [庫的 Wiki 區段][2]中所述。
- 開啟 API 管理小組的提取要求，以將新功能合併到 **受控** 入口網站的程式碼基底。

如需擴充性的詳細資料和指示，請參閱 [GitHub 存放庫][1] 和 [執行 widget 的教學][3]課程。 [自訂受控入口網站的教學](api-management-howto-developer-portal-customize.md)課程會逐步引導您完成入口網站的系統管理面板，此面板常見於**受控**和**自我**裝載版本。

## <a name="frequently-asked-questions"></a><a name="faq"></a> 常見問題集

在本節中，我們會回答有關開發人員入口網站的常見問題，這些都是一般性質。 如需自我裝載版本的特定問題，請參閱 [GitHub 存放庫的 wiki 區段](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> 如何從入口網站的預覽版本進行遷移？

當您第一次啟動開發人員入口網站的預覽版本時，您已在 API 管理服務中布建其預設內容的預覽版本。 已在正式推出的版本中大幅修改了預設內容。 例如，預覽版本的預設內容不會在登入頁面中包含 OAuth 按鈕，它會使用不同的 widget 來顯示 Api，並且依賴有限的功能來結構化開發人員入口網站頁面。 雖然內容有差異，但入口網站的引擎 (包括基礎 widget) 會在您每次發佈開發人員入口網站時自動更新。

如果您是根據預覽版本的內容而高度自訂入口網站，您可以繼續使用它，並在入口網站的頁面上手動放置新的小工具。 否則，建議您以新的預設內容取代您的入口網站內容。

若要在受控入口網站中重設內容，請在 [**作業**] 功能表區段中選取 [**重設內容**]。 這項作業會移除入口網站的所有內容，並提供新的預設內容。 您將會遺失所有開發人員入口網站的自訂和變更。 **您無法復原此動作**。

![重設入口網站內容](media/api-management-howto-developer-portal/reset-content.png)

如果您使用自我裝載版本，請 `scripts.v2/cleanup.bat` `scripts.v2/generate.bat` 從 GitHub 存放庫執行和腳本，以移除現有的內容並布建新的內容。 請務必事先將您入口網站的程式碼升級至 GitHub 存放庫中的最新版本。

如果您在2019年11月的正式運作公告之後第一次存取入口網站，它應該已有新的預設內容，且不需要採取進一步的動作。

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>入口網站不支援我所需的功能

您可以在 [GitHub 存放庫][1] 中開啟功能要求，或 [自行執行遺漏的功能][3]。 如需詳細資訊，請參閱上面的擴充 **性一節** 。

### <a name="how-can-i-automate-portal-deployments"></a><a id="automate"></a> 如何自動化入口網站部署？

您可以透過 REST API，以程式設計方式存取和管理開發人員入口網站的內容，不論您使用的是受控或自我裝載版本。

此 API 記載于 [GitHub 存放庫的 wiki 區段][2]中。 它可用來自動在環境之間遷移入口網站內容，例如，從測試環境到實際執行環境。 您可以在 GitHub 上的 [這篇檔文章中](https://aka.ms/apimdocs/migrateportal) 深入瞭解此程式。

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>如何? 從受控版本移至自我裝載版本？

請參閱 [GitHub 上開發人員入口網站存放庫 Wiki 區段][2]中的詳細文章。

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>我可以在一個 API 管理服務中有多個開發人員入口網站嗎？

您可以有一個受控入口網站和多個自我裝載的入口網站。 所有入口網站的內容都會儲存在相同的 API 管理服務中，因此它們會是相同的。 如果您想要區分入口網站的外觀和功能，您可以使用自己的自訂小工具自我裝載，以在執行時間動態自訂頁面，例如根據 URL。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>入口網站支援 Azure Resource Manager 範本和/或相容于 API 管理 DevOps 資源套件嗎？

不正確。

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>入口網站的內容是否以「API 管理」中的備份/還原功能儲存？

不正確。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>我是否需要為受控入口網站相依性啟用額外的 VNet 連線？

在大部分情況下-否。

如果您的 API 管理服務在內部 VNet 中，則只能從網路記憶體取您的開發人員入口網站。 管理端點的主機名稱必須從您用來存取入口網站的系統管理介面的機器解析為服務的內部 VIP。 請確定已在 DNS 中註冊管理端點。 如果設定錯誤，您會看到錯誤： `Unable to start the portal. See if settings are specified correctly in the configuration (...)` 。

如果您的 API 管理服務在內部 VNet 中，而您是透過來自網際網路的應用程式閘道來存取它，請務必啟用與開發人員入口網站的連線，以及 API 管理的管理端點。 您可能需要停用 Web 應用程式防火牆規則。 如需詳細資訊，請參閱 [這篇檔文章](api-management-howto-integrate-internal-vnet-appgateway.md) 。

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>我已指派自訂 API 管理網域，且發佈的入口網站無法運作

更新網域之後，您必須重新 [發佈入口網站](api-management-howto-developer-portal-customize.md#publish) ，變更才會生效。

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>我已新增身分識別提供者，但在入口網站中看不到它

設定身分識別提供者之後 (例如 Azure AD Azure AD B2C) ，您需要重新 [發佈入口網站](api-management-howto-developer-portal-customize.md#publish) ，變更才會生效。 請確定您的開發人員入口網站頁面包含 OAuth 按鈕 widget。

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>我已設定委派，但入口網站未使用它

設定委派之後，您必須重新 [發佈入口網站](api-management-howto-developer-portal-customize.md#publish) ，變更才會生效。

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>我的其他 API 管理設定變更尚未傳播至開發人員入口網站

大部分的設定變更 (例如，VNet、登入、產品條款) 需要重新 [發佈入口網站](api-management-howto-developer-portal-customize.md#publish)。

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> 我在使用互動式主控台時遇到 CORS 錯誤

互動式主控台會向瀏覽器提出用戶端 API 要求。 藉由在您的 API (s) 上新增 [CORS 原則](api-management-cross-domain-policies.md#CORS) 來解決 cors 問題。

您可以在 Azure 入口網站中的 API 管理服務的 **入口網站總覽** 區段中，檢查 CORS 原則的狀態。 警告方塊表示原則不存在或設定不正確。

![API Management developer portal](media/api-management-howto-developer-portal/cors-azure-portal.png)

按一下 [ **啟用 cors** ] 按鈕，自動套用 cors 原則。

您也可以手動啟用 CORS。

1. 選取 [在 **全域層級上手動** 套用] 連結，以查看產生的原則代碼。
2. 在 Azure 入口網站的 API 管理服務的 [ **api** ] 區段中，流覽至 [api] 區段中的**所有 api** 。
3. 選取 **</>** [ **輸入處理** ] 區段中的圖示。
4. 在 XML 檔案的區段中插入原則 **<inbound>** 。 請確定 **<origin>** 值符合開發人員入口網站的網域。

> [!NOTE]
> 
> 如果您在產品範圍中套用 CORS 原則，而不是 API (的) 範圍內，而您的 API 使用透過標頭的訂用帳戶金鑰驗證，則您的主控台將無法運作。
>
> 瀏覽器會自動發出 OPTIONS HTTP 要求，但不包含具有訂用帳戶金鑰的標頭。 由於缺少訂用帳戶金鑰，API 管理無法將選項呼叫與產品建立關聯，因此無法套用 CORS 原則。
>
> 因應措施是，您可以在查詢參數中傳遞訂用帳戶金鑰。

> [!NOTE]
> 
> 只會執行一個 CORS 原則。 如果您指定多個 CORS 原則 (例如，在 API 層級和所有 Api 層級上) ，您的互動式主控台可能無法如預期般運作。

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>我需要哪些許可權才能編輯開發人員入口網站？

如果您 `Oops. Something went wrong. Please try again later.` 在系統管理模式中開啟入口網站時看到錯誤，可能是因為 AZURE RBAC)  (缺少必要的許可權。

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

## <a name="next-steps"></a>後續步驟

深入瞭解新的開發人員入口網站：

- [存取和自訂受控開發人員入口網站](api-management-howto-developer-portal-customize.md)
- [設定入口網站的自我裝載版本][2]
- [執行您自己的 widget][3]

流覽其他資源：

- [具有原始程式碼的 GitHub 存放庫][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
