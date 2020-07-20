---
title: 新增自訂儲存體（Windows 容器）
description: 瞭解如何在 Azure App Service 的自訂 Windows 容器中附加自訂網路共用。 在應用程式之間共用檔案、遠端系統管理靜態內容和本機存取等等。
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77120679"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>在 App Service 上的 Windows 容器中設定 Azure 檔案儲存體

> [!NOTE]
> 本文適用于自訂 Windows 容器。 若要部署至_Linux_上的 App Service，請參閱[從 Azure 儲存體提供內容](./containers/how-to-serve-content-from-azure-storage.md)。
>

本指南說明如何在 Windows 容器中存取 Azure 儲存體。 僅支援[Azure 檔案儲存體共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)和[Premium 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare)。 您在本操作說明中使用 Azure 檔案儲存體共用。 好處包括受保護的內容、內容可攜性、可存取多個應用程式，以及多個傳輸方法。

## <a name="prerequisites"></a>必要條件

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 或更新版本)。
- [Azure App Service 中的現有 Windows 容器應用程式](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [建立 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [將檔案上傳至 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure 檔案儲存體為非預設儲存體，並會分開計費，不包含在 web 應用程式中。 因為基礎結構的限制，所以不支援使用防火牆設定。
>

## <a name="limitations"></a>限制

- Windows 容器中的 Azure 儲存體處於**預覽**狀態，**不支援**用於**生產案例**。
- Windows 容器中的 Azure 儲存體僅支援裝載**Azure 檔案儲存體容器**（讀取/寫入）。
- Windows 容器中的 Azure 儲存體目前**不支援**在 windows App Service 方案上攜帶您自己的程式碼案例。
- 由於基礎結構的限制，Windows 容器中的 Azure 儲存體**不支援**使用**存放裝置防火牆**設定。
- Windows 容器中的 Azure 儲存體可讓您指定每個應用程式**最多五個**掛接點。
- 無法透過 App Service 的 FTP/FTPs 端點存取裝載至應用程式的 Azure 儲存體。 使用[Azure 儲存體 explorer](https://azure.microsoft.com/features/storage-explorer/)。
- Azure 儲存體會獨立計費，而且**不會包含**在您的 web 應用程式中。 深入瞭解[Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage)。

## <a name="link-storage-to-your-web-app-preview"></a>將儲存體連結到您的 Web 應用程式 (預覽)

 若要將 Azure 檔案儲存體共用掛接至 App Service 應用程式中的目錄，請使用 [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 命令。 儲存體類型必須是 AzureFiles。

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

您應該針對想要連結到 Azure 檔案儲存體共用的其他目錄執行此動作。

## <a name="verify"></a>確認

當 Azure 檔案儲存體共用連結至 web 應用程式之後，您可以執行下列命令來確認：

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>後續步驟

- [使用 Windows 容器（預覽）將 ASP.NET 應用程式遷移至 Azure App Service](app-service-web-tutorial-windows-containers-custom-fonts.md)。
