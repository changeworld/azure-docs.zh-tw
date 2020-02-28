---
title: 使用 Azure AD 認證來執行 Azure CLI 命令，以存取 blob 或佇列資料
titleSuffix: Azure Storage
description: Azure CLI 支援使用 Azure AD 認證進行登入，以在 Azure 儲存體 blob 和佇列資料上執行命令。 存取權杖是針對工作階段提供，用來授權呼叫作業。 許可權取決於指派給 Azure AD 安全性主體的 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 4133c081823f1cc319480c1bc847b672df0374d4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660609"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>使用 Azure AD 認證來執行 Azure CLI 命令，以存取 blob 或佇列資料

Azure 儲存體提供 Azure CLI 的延伸模組，可讓您使用 Azure Active Directory （Azure AD）認證來登入及執行指令碼命令。 當您使用 Azure AD 認證登入 Azure CLI 時，會傳回 OAuth 2.0 存取權杖。 Azure CLI 會自動使用該權杖來對 Blob 或佇列儲存體授權後續的資料作業。 針對支援的作業，您不需要再使用命令傳遞帳戶金鑰或 SAS 權杖。

您可以透過角色型存取控制（RBAC），將 blob 和佇列資料的許可權指派給 Azure AD 的安全性主體。 如需 Azure 儲存體中 RBAC 角色的詳細資訊，請參閱[使用 rbac 來管理 Azure 儲存體資料的存取權限](storage-auth-aad-rbac.md)。

## <a name="supported-operations"></a>支援的作業

支援對 blob 和佇列資料的作業進行 Azure 儲存體延伸模組。 您可能呼叫的作業取決於授與您用來登入 Azure CLI 之 Azure AD 安全性主體的許可權。 Azure 儲存體容器或佇列的許可權會透過 RBAC 來指派。 例如，如果您被指派「 **Blob 資料讀取**者」角色，則您可以執行從容器或佇列讀取資料的指令碼命令。 如果您已獲指派**Blob 資料參與者**角色，則您可以執行讀取、寫入或刪除容器或佇列或其包含之資料的指令碼命令。

如需有關容器或佇列上每個 Azure 儲存體作業所需許可權的詳細資訊，請參閱[使用 OAuth 權杖呼叫儲存體作業](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>使用 Azure AD 認證呼叫 Azure CLI 命令

Azure CLI 支援 blob 和佇列資料作業的 `--auth-mode` 參數：

- 將 `--auth-mode` 參數設定為 `login`，以使用 Azure AD 安全性主體進行登入。
- 將 `--auth-mode` 參數設定為舊版 `key` 值，以便在未針對帳戶提供驗證參數時，嘗試查詢帳戶金鑰。

下列範例示範如何使用您的 Azure AD 認證，從 Azure CLI 在新的儲存體帳戶中建立容器。 請記得以您自己的值取代角括號中的預留位置值：

1. 請確定您已安裝 Azure CLI 版2.0.46 版或更新版本。 執行 `az --version` 來檢查您安裝的版本。

1. 在瀏覽器視窗中執行 `az login` 並驗證：

    ```azurecli
    az login
    ```

1. 指定您想要的訂用帳戶。 使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 建立資源群組。 使用[az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)在該資源群組內建立儲存體帳戶：

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_ZRS \
        --encryption-services blob
    ```

1. 建立容器之前，請先將[儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色指派給自己。 即使您是帳戶擁有者，您還是需要明確的許可權，才能對儲存體帳戶執行資料作業。 如需指派 RBAC 角色的詳細資訊，請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure blob 和佇列資料的存取權](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色指派可能需要幾分鐘的時間才能傳播。

1. 呼叫[az storage container create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)命令，並將 `--auth-mode` 參數設定為 `login`，以使用您的 Azure AD 認證來建立容器：

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

與 `--auth-mode` 參數相關聯的環境變數是 `AZURE_STORAGE_AUTH_MODE`。 您可以在環境變數中指定適當的值，以避免在每次呼叫 Azure 儲存體資料作業時包含它。

## <a name="next-steps"></a>後續步驟

- [使用 Azure CLI 來指派用於存取 blob 和佇列資料的 RBAC 角色](storage-auth-aad-rbac-cli.md)
- [使用 Azure 資源的受控識別來授權 blob 和佇列資料的存取](storage-auth-aad-msi.md)
