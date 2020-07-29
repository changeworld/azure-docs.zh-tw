---
title: 從 ZIP 套件執行您的應用程式
description: 以不可部分完成的方式部署應用程式的 ZIP 套件。 提升應用程式在 ZIP 部署過程中的行為可預測性和可靠性。
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77920717"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>直接從 ZIP 套件在 Azure App Service 中執行您的應用程式

在[Azure App Service](overview.md)中，您可以直接從部署 ZIP 封裝檔案執行應用程式。 本文說明如何在您的應用程式中啟用這項功能。

App Service 中的所有其他部署方法都有共同的功能：您的檔案會部署到應用程式中的*D:\home\site\wwwroot* （或 Linux 應用程式的 */home/site/wwwroot* ）。 因為您的應用程式在執行時間使用了相同的目錄，所以部署可能會因為檔案鎖定衝突而失敗，而應用程式會因為某些檔案尚未更新而無法預期地運作。

相反地，當您直接從封裝執行時，封裝中的檔案不會複製到*wwwroot*目錄。 相反地，ZIP 套件本身會直接掛接為唯讀*wwwroot*目錄。 直接從封裝執行有幾個優點：

- 消除部署與執行時間之間的檔案鎖定衝突。
- 確保在任何時間都只會執行完整部署的應用程式。
- 可以部署到生產應用程式 (透過重新啟動)。
- 提升 Azure Resource Manager 部署的效能。
- 可縮短冷啟動時間，特別是針對具有大型 npm 套件樹狀結構的 JavaScript 函式。

> [!NOTE]
> 目前僅支援 ZIP 封裝檔案。

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>啟用從封裝執行

`WEBSITE_RUN_FROM_PACKAGE`應用程式設定可讓您從封裝執行。 若要設定，請使用 Azure CLI 執行下列命令。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`可讓您從應用程式本機的套件執行應用程式。 您也可以[從遠端封裝執行](#run-from-external-url-instead)。

## <a name="run-the-package"></a>執行封裝

在您的 App Service 中執行封裝最簡單的方式是使用 Azure CLI [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip)命令。 例如：

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

因為 `WEBSITE_RUN_FROM_PACKAGE` 已設定應用程式設定，所以此命令不會將套件內容解壓縮至您應用程式的*D:\home\site\wwwroot*目錄。 相反地，它會以*D:\home\data\SitePackages*方式將 ZIP 檔案上傳至，並在相同的目錄中建立*packagename.txt* ，其中包含要在執行時間載入的 zip 套件名稱。 如果您以不同的方式（例如[FTP](deploy-ftp.md)）上傳 ZIP 套件，您必須手動建立*D:\home\data\SitePackages*目錄和*packagename.txt*檔案。

此命令也會重新開機應用程式。 因為 `WEBSITE_RUN_FROM_PACKAGE` 已設定，App Service 會將上傳的套件掛接為唯讀*wwwroot*目錄，並直接從該掛接的目錄執行應用程式。

## <a name="run-from-external-url-instead"></a>改為從外部 URL 執行

您也可以從外部 URL 執行封裝，例如 Azure Blob 儲存體。 您可以使用 [Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)將套件檔案上傳至 Blob 儲存體帳戶。 您應該使用具有[共用存取簽章（SAS）](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer)的私人儲存體容器，讓 App Service 執行時間安全地存取封裝。 

將檔案上傳至 Blob 儲存體，並具有該檔案的 SAS URL 後，請將 `WEBSITE_RUN_FROM_PACKAGE` 應用程式設定設為 URL。 下列範例會使用 Azure CLI 來執行此動作：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

如果您將具有相同名稱的更新套件發行至 Blob 儲存體，則需要重新開機應用程式，以便將更新的封裝載入 App Service。

## <a name="troubleshooting"></a>疑難排解

- 直接從封裝執行會使其成為 `wwwroot` 唯讀狀態。 如果您的應用程式嘗試將檔案寫入此目錄，將會收到錯誤。
- 不支援 TAR 和 GZIP 格式。
- 這項功能與[本機](overview-local-cache.md)快取不相容。
- 若要改善冷啟動效能，請使用本機 Zip 選項（ `WEBSITE_RUN_FROM_PACKAGE` = 1）。

## <a name="more-resources"></a>其他資源

- [Azure App Service 的持續部署](deploy-continuous-deployment.md)
- [使用 ZIP 或 WAR 檔案部署程式碼](deploy-zip.md)
