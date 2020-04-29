---
title: 提供從 Azure 儲存體到 Linux 容器的內容
description: 瞭解如何在 Azure App Service 中將自訂網路共用連結至您的 Linux 容器。 在應用程式之間共用檔案、遠端系統管理靜態內容和本機存取等等。
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79297912"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>從 Azure 儲存體在 Linux 上的 App Service 中提供內容

> [!NOTE]
> 本文適用于 Linux 容器。 若要部署至自訂 Windows 容器，請參閱[在 App Service 上的 Windows 容器中設定 Azure 檔案儲存體](../configure-connect-to-azure-storage.md)。 Linux 上的 App Service 中的 Azure 儲存體是**預覽**功能。 **生產案例不支援**這項功能。
>

本指南說明如何將 Azure 儲存體附加至 Linux 上的 App Service。 優點包括安全內容、內容可攜性、持續儲存、存取多個應用程式，以及多個傳輸方法。

## <a name="prerequisites"></a>先決條件

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 或更新版本)。
- 現有的[Linux 上的 App Service 應用程式](https://docs.microsoft.com/azure/app-service/containers/)。
- [Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Azure 檔案共用和目錄](../../storage/files/storage-how-to-use-files-cli.md)。


## <a name="limitations-of-azure-storage-with-app-service"></a>使用 App Service Azure 儲存體的限制

- 具有 App Service 的 Azure 儲存體**在**Linux 上的 App Service 和用於容器的 Web App 的預覽狀態。 **不支援**用於**生產案例**。
- 具有 App Service 的 Azure 儲存體支援裝載**Azure 檔案儲存體容器**（讀取/寫入）和**Azure Blob 容器**（唯讀）
- App Service 的 Azure 儲存體**不支援**使用**儲存體防火牆**設定，因為基礎結構的限制。
- App Service 的 Azure 儲存體可讓您為每個應用程式指定**最多五個**掛接點。
- 無法透過 App Service 的 FTP/FTPs 端點存取裝載至應用程式的 Azure 儲存體。 使用[Azure 儲存體 explorer](https://azure.microsoft.com/features/storage-explorer/)。
- Azure 儲存體**不會包含**在您的 web 應用程式中，而且會分開計費。 深入瞭解[Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage)。

> [!WARNING]
> 使用 Azure Blob 儲存體的 App Service 設定將在2020年2月變成隻讀。 [深入了解](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>使用 Azure 儲存體設定您的應用程式

建立[Azure 儲存體帳戶、檔案共用和目錄](#prerequisites)之後，您現在可以使用 Azure 儲存體來設定您的應用程式。

若要將儲存體帳戶掛接到 App Service 應用程式中的目錄，請使用[`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add)命令。 儲存體類型可以是 AzureBlob 或 AzureFiles。 在此範例中會使用 AzureFiles。


> [!CAUTION]
> 在您的 web 應用程式中指定為掛接路徑的目錄應該是空的。 新增外部掛接時，將會刪除儲存在此目錄中的任何內容。 如果您是移轉現有應用程式的檔案，請先備份您的應用程式和其內容再開始移轉。
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

您應該對想要連結到儲存體帳戶的任何其他目錄執行此作業。

## <a name="verify-azure-storage-link-to-the-web-app"></a>確認 web 應用程式 Azure 儲存體連結

一旦儲存體容器連結至 Web 應用程式，您就可以執行以下命令來確認：

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>在 Docker Compose 中使用 Azure 儲存體

Azure 儲存體可以透過使用自訂識別碼的多容器應用程式來裝載。若要查看自訂識別碼名稱，請[`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)執行。

在您的*docker-compose.dev.debug.yml. yml*檔案中， `volumes`將選項`custom-id`對應至。 例如：

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>後續步驟

- [在 Azure App Service 中設定 web 應用程式](../configure-common.md)。

