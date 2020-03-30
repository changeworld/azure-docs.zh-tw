---
title: 使用 Azure 應用配置配置託管標識
description: 瞭解託管標識在 Azure 應用配置中的工作方式以及如何配置託管標識
author: jpconnock
ms.topic: article
ms.date: 02/25/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: fe66466395a100221e6a3cdebdef870bdf195afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623035"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>如何使用託管標識進行 Azure 應用配置

本主題演示如何為 Azure 應用配置創建託管標識。 Azure 活動目錄 （AAD） 中的託管標識允許 Azure 應用配置輕鬆訪問其他受 AAD 保護的資源，如 Azure 金鑰保存庫。 標識由 Azure 平臺管理。 它不要求您預配或輪換任何機密。 如需有關 ADD 中受控身分識別的詳細資訊，請參閱 [Azure 資源的受控身分識別](../active-directory/managed-identities-azure-resources/overview.md)。

您的應用程式可以授與兩種類型的身分識別：

- **系統分配的標識**綁定到配置存儲。 如果已刪除配置存儲，則將其刪除。 配置存儲只能有一個系統分配的標識。
- **使用者分配的標識**是可分配給配置存儲的獨立 Azure 資源。 配置存儲可以有多個使用者分配的身份。

## <a name="adding-a-system-assigned-identity"></a>新增系統指派的身分識別

使用系統分配的標識創建應用配置存儲需要在存儲中設置其他屬性。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

要使用 Azure CLI 設置託管標識，請使用針對現有配置存儲的[az 應用程式標識分配]命令。 有三個選項可供您執行本節中的範例︰

- 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 通過位於下面每個代碼塊右上角的"試用"按鈕使用嵌入的 Azure 雲外殼。
- 如果您喜歡使用本地[CLI 主控台，請安裝最新版本的 Azure CLI（2.1](https://docs.microsoft.com/cli/azure/install-azure-cli)或更高版本）。

以下步驟將引導您完成創建應用配置存儲並使用 CLI 為其分配標識：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login] 登入 Azure。 使用與 Azure 訂閱關聯的帳戶：

    ```azurecli-interactive
    az login
    ```

1. 使用 CLI 創建應用配置存儲。 有關如何將 CLI 與 Azure 應用配置一起使用的更多示例，請參閱[應用配置 CLI 示例](scripts/cli-create-service.md)：

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. 運行[az 應用程式佈建標識分配]命令，以為此配置存儲創建系統分配的標識：

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>添加使用者分配的標識

使用使用者分配標識創建應用配置存儲需要創建標識，然後將其資源識別碼分配給存儲。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

要使用 Azure CLI 設置託管標識，請使用針對現有配置存儲的[az 應用程式標識分配]命令。 有三個選項可供您執行本節中的範例︰

- 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 通過位於下面每個代碼塊右上角的"試用"按鈕使用嵌入的 Azure 雲外殼。
- 如果您偏好使用本機 CLI 主控台，請[安裝最新版的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 或更新版本)。

以下步驟將引導您完成創建使用者分配的標識和應用配置存儲，然後使用 CLI 將標識分配給存儲：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login] 登入 Azure。 使用與 Azure 訂閱關聯的帳戶：

    ```azurecli-interactive
    az login
    ```

1. 使用 CLI 創建應用配置存儲。 有關如何將 CLI 與 Azure 應用配置一起使用的更多示例，請參閱[應用配置 CLI 示例](scripts/cli-create-service.md)：

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. 使用 CLI 創建使用者`myUserAssignedIdentity`分配的標識。

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    在此命令的輸出中，請注意`id`屬性的值。

1. 運行[az 應用程式佈建標識分配]命令，將新的使用者分配標識分配給此配置存儲。 使用您在上一步`id`中注意到的屬性的值。

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>移除身分識別

通過使用 Azure CLI 中的[az 應用程式標識刪除](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove)命令禁用該功能，可以刪除系統分配的標識。 使用者指派的身分識別可以個別移除。 以這種方式將系統指派的身分識別移除，也會從 AAD 刪除它。 當您刪除應用程式資源時，系統指派的身分識別會自動從 AAD 移除。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure 應用配置創建ASP.NET核心應用](quickstart-aspnet-core-app.md)

[az 應用程式佈建標識分配]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az 登錄]: /cli/azure/reference-index#az-login
