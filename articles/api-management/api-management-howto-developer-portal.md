---
title: Azure API 管理開發人員門戶概述
titleSuffix: Azure API Management
description: 瞭解 API 管理中的開發人員門戶。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335901"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API 管理開發人員入口網站概觀

開發人員門戶是一個自動生成的、完全可自訂的網站，包含 API 的文檔。 在這裡，API 消費者可以發現 API、瞭解如何使用它們、請求訪問並嘗試它們。

本文介紹了 API 管理中開發人員門戶的自託管版本和託管版本之間的差異。 它還解釋了其體系結構，並提供了常見問題的解答。

![API Management developer portal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>託管和自託管版本

您可以通過兩種方式構建開發人員門戶：

- **託管版本**- 通過編輯和自訂門戶，該門戶內置於 API 管理實例中，可通過 URL`<your-api-management-instance-name>.developer.azure-api.net`進行訪問。 請參閱[本文檔文章](api-management-howto-developer-portal-customize.md)，瞭解如何訪問和自訂託管門戶。
- **自託管版本**- 通過在 API 管理實例之外部署和自託管門戶。 此方法允許您編輯門戶的代碼庫並擴展提供的核心功能 - 例如，實現自訂小部件以便與協力廠商系統集成。 在這種情況下，您是門戶的維護者，您負責將門戶升級到最新版本。 有關詳細資訊和說明，請參閱[包含門戶原始程式碼的 GitHub 存儲庫][1]以及[有關實現小部件的教程][3]。 [託管版本的教程](api-management-howto-developer-portal-customize.md)將介紹門戶的管理面板，這是託管版本和自託管版本常見的。

## <a name="portal-architectural-concepts"></a>門戶體系結構概念

門戶元件在邏輯上可以分為兩類：*代碼*和*內容*。

*代碼*在[GitHub 存儲庫][1]中維護，包括：

- 小部件 - 表示可視元素，並結合 HTML、JavaScript、樣式設置、設置和內容映射。 示例包括圖像、文本段落、表單、API 清單等。
- 樣式定義 - 指定小部件的樣式設置
- 引擎 - 從門戶內容生成靜態網頁，並在 JavaScript 中編寫
- 視覺化編輯器 - 允許瀏覽器內自訂和創作體驗

*內容*分為兩個子類別：*門戶內容和* *API 管理內容*。

*門戶內容*特定于門戶，包括：

- 頁面 - 例如，著陸頁、API 教程、博客文章
- 媒體 - 圖像、動畫和其他基於檔的內容
- 佈局 - 範本，與 URL 匹配並定義頁面的顯示方式
- 樣式 - 樣式定義的值，例如字體、顏色、邊框
- 設置 - 配置，例如 favicon，網站中繼資料

*門戶內容*（媒體除外）表示為 JSON 文檔。

*API 管理內容*包括 API、操作、產品、訂閱等實體。

該門戶基於[Paperbit 框架](https://paperbits.io/)的自我調整分叉。 原始 Paperbit 功能已擴展，以提供特定于 API 管理的小部件（例如，API 清單、產品清單）和用於保存和檢索內容的 API 管理服務的連接器。

## <a name="frequently-asked-questions"></a><a name="faq"></a>常見問題

在本節中，我們將回答有關開發人員門戶的常見問題，這些問題具有一般性質。 有關特定于自託管版本的問題，請參閱[GitHub 存儲庫的 wiki 部分](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/>如何從門戶的預覽版本遷移？

通過使用開發人員門戶的預覽版本，您可以在 API 管理服務中預配預覽內容。 在通常可用的版本中，預設內容已顯著修改，以便更好的使用者體驗。 它還包括新的小部件。

如果您使用的是託管版本，請按一下"**操作"** 功能表部分中的 **"重置內容**"來重置門戶的內容。 確認此操作將刪除門戶的所有內容並預配新的預設內容。 門戶的引擎已在 API 管理服務中自動更新。

![重置門戶內容](media/api-management-howto-developer-portal/reset-content.png)

如果您使用的是自託管版本，請使用`scripts/cleanup.bat`和`scripts/generate.bat`從 GitHub 存儲庫刪除現有內容和預配新內容。 確保事先將門戶的代碼升級到 GitHub 存儲庫的最新版本。

如果不想重置門戶的內容，可以考慮在整個頁面中使用新可用的小部件。 現有小部件已自動更新到最新版本。

如果您的門戶是在常規可用性公告之後預配的，則門戶應該已經具有新的預設內容。 不需要從側面執行任何操作。

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>如何從舊的開發人員門戶遷移到開發人員門戶？

門戶不相容，您需要手動遷移內容。

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>門戶是否具有舊門戶的所有功能？

開發人員門戶不再支援*應用程式*和*問題*。

不支援在互動式開發人員主控台中使用 OAuth 進行身份驗證。 您可以通過[GitHub 問題](https://github.com/Azure/api-management-developer-portal/issues/208)跟蹤進度。

### <a name="has-the-old-portal-been-deprecated"></a>舊門戶已被棄用了嗎？

舊的開發人員和發行者門戶現在是*遺留*功能 - 它們將僅接收安全更新。 新功能將僅在新的開發人員門戶中實現。

舊門戶的棄用將單獨公佈。 如果您有疑問、疑慮或意見，則[在專用 GitHub 問題中](https://github.com/Azure/api-management-developer-portal/issues/121)提出這些問題、

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>門戶不支援我需要的功能

您可以打開[功能請求](https://aka.ms/apimwish)或[自行實現缺少的功能][3]。 如果自己實現該功能，則可以自行託管開發人員門戶，或在 GitHub 上打開拉取請求，以在託管版本中包含更改。

### <a name="how-can-i-automate-portal-deployments"></a>如何自動執行門戶部署？

您可以通過 REST API 以程式設計方式訪問和管理開發人員門戶的內容，而不管您使用的是託管版本還是自託管版本。

API 記錄在[GitHub 存儲庫的 wiki 部分][2]。 它可用於自動在環境之間遷移門戶內容，例如，從測試環境到生產環境。 您可以在有關 GitHub[的本文檔文章中](https://aka.ms/apimdocs/migrateportal)瞭解有關此過程的更多詳細資訊。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>門戶是否支援 Azure 資源管理器範本和/或是否與 API 管理 DevOps 資源工具組相容？

否。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>是否需要為託管門戶依賴項啟用其他 VNet 連接？

在大多數情況下 - 否。

如果您的 API 管理服務位於內部 VNet 中，則開發人員門戶只能通過網路內訪問。 管理終結點的主機名稱必須從用於訪問門戶的管理介面的電腦解析為服務的內部 VIP。 確保管理終結點在 DNS 中註冊。 如果配置錯誤，您將看到一個錯誤： `Unable to start the portal. See if settings are specified correctly in the configuration (...)`。

如果您的 API 管理服務位於內部 VNet 中，並且您正在通過 Internet 應用程式閘道訪問該服務，請確保啟用與開發人員門戶和 API 管理管理終結點的連接。

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>我已分配了自訂 API 管理域，而發佈的門戶不起作用

更新域後，需要[重新發佈門戶](api-management-howto-developer-portal-customize.md#publish)，以便更改生效。

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>我添加了一個標識提供程式，並且無法在門戶中看到它

配置標識提供程式（例如 AAD、AAD B2C）後，需要[重新發佈門戶](api-management-howto-developer-portal-customize.md#publish)，以便更改生效。

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>我已經設置了委派，門戶不使用它

設置委派後，需要[重新發佈門戶](api-management-howto-developer-portal-customize.md#publish)，以便更改生效。

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>我的其他 API 管理配置更改尚未在開發人員門戶中傳播

大多數配置更改（例如 VNet、登錄和產品術語）都需要[重新發佈門戶](api-management-howto-developer-portal-customize.md#publish)。

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>使用互動式主控台時，我收到 CORS 錯誤

互動式主控台從瀏覽器發出用戶端 API 請求。 通過在 API 上添加[CORS 策略來解決 CORS](api-management-cross-domain-policies.md#CORS)問題。

您可以在 Azure 門戶中的 API 管理服務的**門戶概述**部分中檢查 CORS 策略的狀態。 警告框指示策略不存在或配置錯誤。

![API Management developer portal](media/api-management-howto-developer-portal/cors-azure-portal.png)

按一下啟用 CORS 按鈕，自動應用**CORS**策略。

您還可以手動啟用 CORS。

1. 按一下"**手動將其應用於全域級別**"連結以查看生成的策略代碼。
2. 導航到 Azure 門戶中 API 管理服務**API**部分中的所有**API。**
3. 按一下**</>****"入站處理**"部分中的圖示。
4. 將策略插入 XML**<inbound>** 檔部分。 確保**<origin>** 該值與開發人員門戶的域匹配。

> [!NOTE]
> 
> 如果在"產品"作用域（而不是 API）作用域中應用 CORS 策略，並且 API 通過標頭使用訂閱金鑰身份驗證，則主控台將不起作用。
>
> 瀏覽器會自動發出選項 HTTP 要求，該請求不包含包含訂閱金鑰的標頭。 由於缺少訂閱金鑰，API 管理無法將 OPTIONS 調用與產品相關聯，因此無法應用 CORS 策略。
>
> 作為解決方法，您可以在查詢參數中傳遞訂閱金鑰。

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>編輯開發人員門戶需要哪些許可權？

如果在管理模式下打開門戶時`Oops. Something went wrong. Please try again later.`看到錯誤，則可能缺少所需的許可權 （RBAC）。

舊門戶需要服務作用域 （ `Microsoft.ApiManagement/service/getssotoken/action` `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`） 的許可權才能允許使用者管理員訪問門戶。 新門戶需要作用域`Microsoft.ApiManagement/service/users/token/action``/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`的許可權。

您可以使用以下 PowerShell 腳本創建具有所需許可權的角色。 請記住更改參數`<subscription-id>`。 

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
 
創建角色後，可以從 Azure 門戶中的**存取控制 （IAM）** 部分授予任何使用者。 將此角色指派給使用者將在服務作用域分配許可權。 使用者將能夠代表服務*中的任何使用者生成*SAS 權杖。 至少需要將此角色指派給服務的管理員。 以下 PowerShell 命令演示如何在最低作用域內將角色指派給`user1`使用者，以避免向使用者授予不必要的許可權： 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

向使用者授予許可權後，使用者必須登出並再次登錄到 Azure 門戶，以便新許可權生效。

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>我看到錯誤`Unable to start the portal. See if settings are specified correctly (...)`

當`GET`調用`https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview`失敗時，將顯示此錯誤。 調用由門戶的管理介面從瀏覽器發出。

如果您的 API 管理服務位於 VNet 中 - 請參閱上面的 VNet 連接問題。

呼叫失敗也可能由 TLS/SSL 憑證引起，該證書分配給自訂域，並且不受瀏覽器信任。 作為緩解措施，您可以刪除管理終結點自訂域 - API 管理將回退到具有受信任證書的預設終結點。

### <a name="whats-the-browser-support-for-the-portal"></a>瀏覽器對門戶的支援是什麼？

| 瀏覽器                     | 支援       |
|-----------------------------|-----------------|
| Apple Safari                | 是<sup>1</sup> |
| Google Chrome               | 是<sup>1</sup> |
| Microsoft Edge              | 是<sup>1</sup> |
| Microsoft Internet Explorer | 否              |
| Mozilla Firefox             | 是<sup>1</sup> |

 <small><sup>1</sup>在兩個最新的生產版本中支援。</small>

## <a name="next-steps"></a>後續步驟

瞭解有關新開發人員門戶的更多內容：

- [訪問並自訂託管開發人員門戶](api-management-howto-developer-portal-customize.md)
- [設置門戶的自託管版本][2]
- [實現您自己的小部件][3]

流覽其他資源：

- [帶有原始程式碼的 GitHub 存儲庫][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend