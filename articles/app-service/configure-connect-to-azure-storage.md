---
title: 添加自訂存儲（Windows 容器）
description: 瞭解如何在 Azure 應用服務中的自訂 Windows 容器中附加自訂網路共用。 在應用之間共用檔、遠端系統管理靜態內容和在本地訪問等。
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120679"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>在應用服務上的 Windows 容器中配置 Azure 檔

> [!NOTE]
> 本文適用于自訂 Windows 容器。 要部署到_Linux_上的應用服務，請參閱[從 Azure 存儲中提供內容](./containers/how-to-serve-content-from-azure-storage.md)。
>

本指南演示如何訪問 Windows 容器中的 Azure 存儲。 僅支援[Azure 檔共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)和[高級檔共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare)。 在此操作操作中使用 Azure 檔共用。 好處包括受保護的內容、內容可攜性、可存取多個應用程式，以及多個傳輸方法。

## <a name="prerequisites"></a>Prerequisites

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 或更新版本)。
- [Azure 應用服務中的現有 Windows 容器應用](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [創建 Azure 檔共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [將檔上載到 Azure 檔共用](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure 檔是非預設存儲，單獨計費，未包含在 Web 應用中。 由於基礎結構限制，它不支援使用防火牆配置。
>

## <a name="limitations"></a>限制

- Windows 容器中的 Azure 存儲**處於預覽狀態**，**不支援****生產方案**。
- Windows 容器中的 Azure 存儲僅支援安裝**Azure 檔容器**（讀/寫）。
- 當前**不支援**在 Windows 應用服務方案上攜帶自己的代碼方案在 Windows 容器中的 Azure 存儲。
- 由於基礎結構限制，Windows 容器中的 Azure 存儲**不支援**使用**存儲防火牆**配置。
- Windows 容器中的 Azure 存儲允許您指定每個應用**最多五個**裝載點。
- 通過應用服務 FTP/FTP 終結點無法訪問裝載到應用的 Azure 存儲。 使用[Azure 存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)。
- Azure 存儲是獨立計費的，**不包括**在 Web 應用中。 瞭解有關[Azure 存儲定價](https://azure.microsoft.com/pricing/details/storage)的更多。

## <a name="link-storage-to-your-web-app-preview"></a>將儲存體連結到您的 Web 應用程式 (預覽)

 要將 Azure 檔共用裝載到應用服務應用中的目錄，請使用 命令[`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add)。 存儲類型必須是 Azure 檔。

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

對於要連結到 Azure 檔共用的任何其他目錄，應執行此操作。

## <a name="verify"></a>Verify

將 Azure 檔共用連結到 Web 應用後，可以通過運行以下命令來驗證這一點：

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>後續步驟

- [使用 Windows 容器（預覽）將ASP.NET應用遷移到 Azure 應用服務](app-service-web-tutorial-windows-containers-custom-fonts.md)。
