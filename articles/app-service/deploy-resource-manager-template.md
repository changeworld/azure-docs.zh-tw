---
title: 使用範本部署應用程式
description: 尋找有關建立 Azure Resource Manager 範本以布建和部署 App Service 應用程式的指引。
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1146b5979d81b91c6c6894aa54b2e0ca50c896c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961596"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本部署 Web 應用程式的指引

本文提供建立 Azure Resource Manager 範本以部署 Azure App Service 解決方案的建議。 這些建議可協助您避免常見的問題。

## <a name="define-dependencies"></a>定義相依性

要定義 Web 應用程式的相依性，必須先了解 Web 應用程式中的資源彼此互動的方式。 如果您以不正確的順序指定相依性，將可能會導致部署錯誤，或產生競爭情形而致使部署停止。

> [!WARNING]
> 如果您在範本中納入 MSDeploy 網站延伸模組，則必須將任何組態資源設定為相依於 MSDeploy 資源。 組態變更會導致網站以非同步方式重新啟動。 將組態資源設定為相依於 MSDeploy，可確保在 MSDeploy 完成後，網站才會重新啟動。 若沒有這些相依性，網站即可能在 MSDeploy 的部署程序期間重新啟動。 如需範例範本，請參閱 [WordPress 範本 Web Deploy](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json)相依性。

下圖顯示各種 App Service 資源的相依性順序：

![Web 應用程式相依性](media/web-sites-rm-template-guidance/web-dependencies.png)

您可以依照下列順序部署資源︰

**第 1 層**
* App Service 方案。
* 任何其他相關資源，例如資料庫或儲存體帳戶。

**第 2 層**
* Web 應用程式 - 視 App Service 方案而定。
* 以伺服器陣列為目標的 Azure Application Insights 執行個體 - 視 App Service 方案而定。

**第 3 層**
* 原始檔控制 - 視 Web 應用程式而定。
* MSDeploy 網站延伸模組 - 視 Web 應用程式而定。
* 以 web 應用程式為目標的 Azure 應用程式 Insights 實例-視 web 應用程式而定。

**第 4 層**
* App Service 憑證 - 取決於存在的原始檔控制或 MSDeploy。 若都不存在，則視 Web 應用程式而定。
* 組態設定 (連接字串、Web 組態值、應用程式設定) - 取決於存在的原始檔控制或 MSDeploy。 若都不存在，則視 Web 應用程式而定。

**第 5 層**
* 主機名稱繫結 - 取決於存在的憑證。 若不存在，則取決於較高層級的資源。
* 網站延伸模組 - 取決於存在的組態設定。 若不存在，則取決於較高層級的資源。

一般而言，您的解決方案只會包含前述部分的資源和層級。 對於缺漏的層級，會將較低的資源往高一層對應。

下列範例將說明範本的一部分。 連接字串組態的值取決於 MSDeploy 延伸模組。 MSDeploy 延伸模組則取決於 Web 應用程式和資料庫。 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

如需使用上述程式碼且已就緒可執行的範例，請參閱[範本：建置簡單的 Umbraco Web 應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple) \(英文\)。

## <a name="find-information-about-msdeploy-errors"></a>尋找 MSDeploy 錯誤的相關資訊

如果您的 Resource Manager 範本使用 MSDeploy，部署錯誤訊息可能會難以了解。 若要在部署失敗後取得詳細資訊，請嘗試執行下列步驟：

1. 移至網站的 [Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console)。
2. 瀏覽至位於 D:\home\LogFiles\SiteExtensions\MSDeploy 上的資料夾。
3. 尋找 appManagerStatus.xml 和 appManagerLog.xml 檔案。 第一個檔案會記錄狀態。 第二個檔案會記錄錯誤的相關資訊。 如果錯誤對您而言並不清楚，您可以在要求 [論壇](/answers/topics/azure-webapps.html)的協助時包含此錯誤。

## <a name="choose-a-unique-web-app-name"></a>選擇唯一的 Web 應用程式名稱

Web 應用程式的名稱必須是全域唯一的。 您可以使用很可能是唯一的命名慣例，或使用 [uniqueString 函式](../azure-resource-manager/templates/template-functions-string.md#uniquestring)以利產生唯一的名稱。

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>部署 Key Vault 中的 Web 應用程式憑證

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果您的範本包含適用于 TLS/SSL 系結的 [Microsoft/](/azure/templates/microsoft.web/certificates) certificate 資源，且該憑證儲存在 Key Vault 中，您必須確定 App Service 身分識別可以存取該憑證。

在全域 Azure 中，App Service 服務主體的識別碼為 **abfa0a7c-a6b6-4736-8310-5855508787cd**。 若要授與 App Service 服務主體的 Key Vault 存取，請使用：

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

在 Azure Government 中，App Service 服務主體的識別碼為 **6a02c803-dafd-4136-b4c3-5a6f318b4714**。 使用上述範例中的該識別碼。

在 Key Vault 中，選取 [憑證]**** 和 [產生/匯入]**** 以上傳憑證。

![匯入憑證](media/web-sites-rm-template-guidance/import-certificate.png)

在範本中，提供 `keyVaultSecretName` 的憑證名稱。

如需範例範本，請參閱 [Deploy a Web App certificate from Key Vault secret and use it for creating SSL binding](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault) (從 Key Vault 祕密部署 Web 應用程式憑證，並使用它建立 SSL 繫結)。

## <a name="next-steps"></a>後續步驟

* 如需使用範本部署 Web 應用程式的教學課程，請參閱[透過可預測方式在 Azure 中佈建和部署微服務](deploy-complex-application-predictably.md)。
* 若要深入了解範本中資源類型的 JSON 語法和屬性，請參閱 [Azure Resource Manager 範本參考](/azure/templates/)。