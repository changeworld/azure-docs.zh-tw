---
title: '新增 Azure 儲存體 (容器) '
description: 瞭解如何在 Azure App Service 的容器化應用程式中連接自訂的網路共用。 在應用程式之間共用檔案、從遠端系統管理靜態內容，以及在本機存取等。
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: c240399f1368862a969561409371e075a010e8f2
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435604"
---
# <a name="access-azure-storage-as-a-network-share-from-a-container-in-app-service"></a>從 App Service 中的容器存取 Azure 儲存體為網路共用

::: zone pivot="container-windows"

本指南說明如何將 Azure 儲存體檔案以網路共用的形式附加至 App Service 中的 windows 容器。 僅支援 [Azure 檔案儲存體共用](../storage/files/storage-how-to-use-files-cli.md) 和 [Premium 檔案共用](../storage/files/storage-how-to-create-premium-fileshare.md) 。 好處包括受保護的內容、內容可攜性、可存取多個應用程式，以及多個傳輸方法。

::: zone-end

::: zone pivot="container-linux"

本指南說明如何將 Azure 儲存體連結至 Linux 容器 App Service。 優點包括安全的內容、內容可攜性、持續性儲存體、多個應用程式的存取，以及多個傳輸方法。

::: zone-end

## <a name="prerequisites"></a>必要條件

::: zone pivot="container-windows"

- [Azure App Service 中的現有 Windows 容器應用程式](quickstart-custom-container.md)
- [建立 Azure 檔案共用](../storage/files/storage-how-to-use-files-cli.md)
- [將檔案上傳至 Azure 檔案共用](../storage/files/storage-files-deployment-guide.md)

::: zone-end

::: zone pivot="container-linux"

- 現有的 [Linux 上的 App Service 應用程式](index.yml)。
- [Azure 儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-cli)
- [Azure 檔案共用和目錄](../storage/files/storage-how-to-use-files-cli.md)。

::: zone-end

> [!NOTE]
> Azure 檔案儲存體為非預設儲存體，並分開計費，不包含在 web 應用程式中。 因為基礎結構限制，所以不支援使用防火牆設定。
>

## <a name="limitations"></a>限制

::: zone pivot="container-windows"

- App Service 中的 Azure 儲存體處於**預覽**狀態，且**不支援****生產案例**。
- App Service 中的 Azure 儲存體目前 **不支援** 將您自己的程式碼案例提供給 (非容器化 Windows 應用程式) 。
- App Service 中的 Azure 儲存體 **不支援** 使用 **儲存體防火牆** 設定，因為基礎結構的限制。
- Azure 儲存體與 App Service 可讓您針對每個應用程式指定 **最多五個** 掛接點。
- 無法透過 App Service FTP/FTPs 端點來存取掛接至應用程式 Azure 儲存體。 使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。

::: zone-end

::: zone pivot="container-linux"

- App Service 中的 Azure 儲存體為 Linux 上的 App Service 和用於容器的 Web App 的 **預覽** 狀態。 但**不支援****生產案例**。
- App Service 中的 Azure 儲存體支援裝載 **Azure 檔案儲存體容器** (讀取/寫入) 和 **Azure Blob 容器** (唯讀) 
- App Service 中的 Azure 儲存體 **不支援** 使用 **儲存體防火牆** 設定，因為基礎結構的限制。
- App Service 中的 Azure 儲存體可讓您針對每個應用程式指定 **最多五個** 掛接點。
- 無法透過 App Service FTP/FTPs 端點來存取掛接至應用程式 Azure 儲存體。 使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。

::: zone-end

## <a name="link-storage-to-your-app"></a>將儲存體連結到您的應用程式

::: zone pivot="container-windows"

一旦建立您的 [Azure 儲存體帳戶、檔案共用和目錄](#prerequisites)之後，您現在可以使用 Azure 儲存體來設定您的應用程式。

若要將 Azure 檔案儲存體共用掛接至 App Service 應用程式中的目錄，請使用 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 命令。 儲存體類型必須是 AzureFiles。

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

針對您想要連結至 Azure 檔案儲存體共用的其他目錄，您應該這麼做。

::: zone-end

::: zone pivot="container-linux"

一旦建立您的 [Azure 儲存體帳戶、檔案共用和目錄](#prerequisites)之後，您現在可以使用 Azure 儲存體來設定您的應用程式。

若要將儲存體帳戶掛接至 App Service 應用程式中的目錄，請使用 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 命令。 儲存體類型可以是 AzureBlob 或 AzureFiles。 此範例中使用 AzureFiles。 [掛接路徑] 設定會對應到您想要從 Azure 儲存體掛接的資料夾。 將它設定為 '/' 會裝載整個 Azure 儲存體。


> [!CAUTION]
> 在 web 應用程式中指定為掛接路徑的目錄應為空白。 新增外部掛接時，將會刪除儲存在此目錄中的任何內容。 如果您是移轉現有應用程式的檔案，請先備份您的應用程式和其內容再開始移轉。
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

您應該對想要連結到儲存體帳戶的任何其他目錄執行此作業。

::: zone-end

## <a name="verify-linked-storage"></a>確認連結的儲存體

一旦共用連結至應用程式，您可以執行下列命令來確認這一點：

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>接下來的步驟

::: zone pivot="container-windows"

- [使用自訂容器將自訂軟體遷移至 Azure App Service](tutorial-custom-container.md?pivots=container-windows)。

::: zone-end

::: zone pivot="container-linux"

- [設定自訂容器](configure-custom-container.md?pivots=platform-linux)。

::: zone-end
