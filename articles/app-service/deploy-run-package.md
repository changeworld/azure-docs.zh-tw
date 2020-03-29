---
title: 從 ZIP 包運行應用
description: 以原子性部署應用的 ZIP 包。 提高應用在 ZIP 部署過程中行為的可預測性和可靠性。
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920717"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>直接從 ZIP 包在 Azure 應用服務中運行應用

在[Azure 應用服務](overview.md)中，可以直接從部署 ZIP 包檔運行應用。 本文演示如何在應用中啟用此功能。

App Service 中的所有其他部署方法都有共同之處：您的檔被部署到應用程式中*的 D：\home_site_wwwroot（* 或 */home/site/wwwroot*用於 Linux 應用）。 由於應用在運行時使用相同的目錄，因此部署可能會由於檔鎖定衝突而失敗，並且由於某些檔尚未更新而無法預測地運行。

相反，當您直接從包運行時，包中的檔不會複製到*wwwroot*目錄。 相反，ZIP 包本身直接裝載為唯讀*wwwroot*目錄。 直接從包運行有幾個好處：

- 消除了部署和運行時之間的檔鎖定衝突。
- 確保在任何時候僅運行完全部署的應用。
- 可以部署到生產應用程式 (透過重新啟動)。
- 提升 Azure Resource Manager 部署的效能。
- 可縮短冷啟動時間，特別是針對具有大型 npm 套件樹狀結構的 JavaScript 函式。

> [!NOTE]
> 目前，僅支援 ZIP 包檔。

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>啟用從包運行

應用`WEBSITE_RUN_FROM_PACKAGE`設置允許從包運行。 要設置它，使用 Azure CLI 運行以下命令。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`允許您從本地包運行應用到應用。 您也可以[從遠端包運行](#run-from-external-url-instead)。

## <a name="run-the-package"></a>執行封裝

在應用服務中運行包的最簡單方法是使用 Azure CLI [az Webapp 部署源配置-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip)命令。 例如：

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

由於`WEBSITE_RUN_FROM_PACKAGE`應用設置已設置，因此此命令不會將包內容提取到應用的*D：_home_site_wwwroot*目錄。 相反，它將 ZIP 檔作為正樣上載到*D：\home_data_SitePackage，* 並在同一目錄中創建一個*包name.txt，* 其中包含在運行時載入的 ZIP 包的名稱。 如果以不同的方式（如[FTP）](deploy-ftp.md)上傳 ZIP 包，則需要手動創建*D：_home_data_SitePackage*目錄和*包名.txt*檔。

該命令還會重新開機應用。 由於`WEBSITE_RUN_FROM_PACKAGE`已設置，應用服務將上載的包裝載為唯讀*wwwroot*目錄，並直接從該裝載的目錄運行應用。

## <a name="run-from-external-url-instead"></a>而是從外部 URL 運行

還可以從外部 URL（如 Azure Blob 存儲）運行包。 您可以使用 [Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)將套件檔案上傳至 Blob 儲存體帳戶。 應使用具有[共用訪問簽名 （SAS）](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer)的專用存儲容器，以使應用服務運行時能夠安全地訪問包。 

將檔上載到 Blob 存儲並具有檔的 SAS URL 後，請將`WEBSITE_RUN_FROM_PACKAGE`應用設置設置為 URL。 下面的示例通過使用 Azure CLI 進行：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

如果將具有相同名稱的更新包發佈到 Blob 存儲，則需要重新開機應用，以便將更新的包載入到應用服務中。

## <a name="troubleshooting"></a>疑難排解

- 直接從包運行可使`wwwroot`唯讀。 如果應用嘗試將檔寫入此目錄，則收到錯誤。
- 不支援 TAR 和 GZIP 格式。
- 此功能與[本機快取](overview-local-cache.md)不相容。
- 為提高冷啟動性能，請使用本地 Zip 選項 （#1）。`WEBSITE_RUN_FROM_PACKAGE`

## <a name="more-resources"></a>其他資源

- [Azure 應用服務的連續部署](deploy-continuous-deployment.md)
- [使用 ZIP 或 WAR 檔部署代碼](deploy-zip.md)
