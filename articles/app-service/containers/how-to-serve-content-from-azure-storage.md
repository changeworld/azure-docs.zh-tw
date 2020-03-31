---
title: 將內容從 Azure 存儲服務到 Linux 容器
description: 瞭解如何在 Azure 應用服務中將自訂網路共用附加到 Linux 容器。 在應用之間共用檔、遠端系統管理靜態內容和在本地訪問等。
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297912"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>從 Azure 儲存體在 Linux 上的 App Service 中提供內容

> [!NOTE]
> 本文適用于 Linux 容器。 要部署到自訂 Windows 容器，請參閱[在應用服務上的 Windows 容器中配置 Azure 檔](../configure-connect-to-azure-storage.md)。 Linux 上應用服務中的 Azure 存儲是一個**預覽**功能。 生產**方案不支援**此功能。
>

本指南演示如何將 Azure 存儲附加到 Linux 上的應用服務。 優點包括安全內容、內容可攜性、持久存儲、訪問多個應用以及多種傳輸方法。

## <a name="prerequisites"></a>Prerequisites

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 或更新版本)。
- Linux[應用程式上的現有應用程式服務](https://docs.microsoft.com/azure/app-service/containers/)。
- [Azure 存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Azure 檔共用和目錄](../../storage/files/storage-how-to-use-files-cli.md)。


## <a name="limitations-of-azure-storage-with-app-service"></a>使用應用服務的 Azure 存儲限制

- 帶有應用服務的 Azure 存儲在 Linux 上的應用程式服務和容器的 Web 應用的**預覽**版中。 生產**方案****不支援**它。
- 具有應用服務的 Azure 存儲支援安裝**Azure 檔容器**（讀取/寫入）和**Azure Blob 容器**（唯讀）
- 由於基礎結構限制，使用應用服務的 Azure 存儲**不支援**使用**存儲防火牆**配置。
- 使用應用服務的 Azure 存儲允許您指定每個應用**最多五個**裝載點。
- 通過應用服務 FTP/FTP 終結點無法訪問裝載到應用的 Azure 存儲。 使用[Azure 存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)。
- Azure 存儲**不包括**在 Web 應用中，並單獨計費。 瞭解有關[Azure 存儲定價](https://azure.microsoft.com/pricing/details/storage)的更多。

> [!WARNING]
> 使用 Azure Blob 存儲的應用服務配置將在 2020 年 2 月變為唯讀。 [深入了解](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>使用 Azure 存儲配置應用

創建[Azure 存儲帳戶、檔共用和目錄](#prerequisites)後，現在可以使用 Azure 存儲配置應用。

要將存儲帳戶裝載到應用服務應用中的目錄，請使用 該[`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add)命令。 儲存體類型可以是 AzureBlob 或 AzureFiles。 此示例中使用了 Azure 檔。


> [!CAUTION]
> 指定為 Web 應用中裝載路徑的目錄應為空。 添加外部裝載時，將刪除存儲在此目錄中的任何內容。 如果您是移轉現有應用程式的檔案，請先備份您的應用程式和其內容再開始移轉。
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

您應該對想要連結到儲存體帳戶的任何其他目錄執行此作業。

## <a name="verify-azure-storage-link-to-the-web-app"></a>驗證 Azure 存儲連結到 Web 應用

一旦儲存體容器連結至 Web 應用程式，您就可以執行以下命令來確認：

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>在 Docker 撰寫中使用 Azure 存儲

可以使用自訂 id 與多容器應用一起裝載 Azure 存儲。要查看自訂 id 名稱，請查看[`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)。

在*docker-compose.yml*檔中，將`volumes`選項映射到`custom-id`。 例如：

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>後續步驟

- [在 Azure 應用服務 中配置 Web 應用](../configure-common.md)。

