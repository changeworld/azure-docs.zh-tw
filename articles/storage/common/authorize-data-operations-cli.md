---
title: 使用 Azure CLI 授權訪問 Blob 或佇列資料
titleSuffix: Azure Storage
description: 指定如何使用 Azure CLI 授權針對 Blob 或佇列資料執行資料操作。 您可以使用 Azure AD 憑據、帳戶訪問金鑰或共用訪問簽名 （SAS） 權杖授權資料操作。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207689"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>使用 Azure CLI 授權訪問 Blob 或佇列資料

Azure 存儲為 Azure CLI 提供擴展，使您能夠指定如何授權對 Blob 或佇列資料的操作。 您可以通過以下方式授權資料操作：

- 具有 Azure 活動目錄 （Azure AD） 安全主體。 Microsoft 建議使用 Azure AD 憑據，以提供卓越的安全性和易用性。
- 使用帳戶訪問金鑰或共用訪問簽名 （SAS） 權杖。

## <a name="specify-how-data-operations-are-authorized"></a>指定資料操作的授權方式

用於讀取和寫入 Blob 和佇列資料的 Azure CLI`--auth-mode`命令包括可選參數。 指定此參數以指示如何授權資料操作：

- 將`--auth-mode`參數`login`設置為使用 Azure AD 安全主體登錄（建議）。
- 將`--auth-mode`參數設置為舊`key`值，以嘗試檢索用於授權的帳戶訪問金鑰。 如果省略了參數，`--auth-mode`則 Azure CLI 還會嘗試檢索訪問金鑰。

要使用`--auth-mode`參數，請確保已安裝 Azure CLI 版本 2.0.46 或更高版本。 執行 `az --version` 來檢查您安裝的版本。

> [!IMPORTANT]
> 如果省略參數`--auth-mode`或將其設置為`key`，則 Azure CLI 會嘗試使用帳戶訪問金鑰進行授權。 在這種情況下，Microsoft 建議您在命令或**AZURE_STORAGE_KEY**環境變數中提供訪問金鑰。 有關環境變數的詳細資訊，請參閱標題為["設置授權參數的環境變數"的部分](#set-environment-variables-for-authorization-parameters)。
>
> 如果不提供訪問金鑰，Azure CLI 將嘗試調用 Azure 存儲資來源提供者來檢索每個操作。 執行許多需要調用資來源提供者的資料操作可能會導致限制。 有關資來源提供者限制的詳細資訊，請參閱[Azure 存儲資來源提供者 的可伸縮性和性能目標](scalability-targets-resource-provider.md)。

## <a name="authorize-with-azure-ad-credentials"></a>使用 Azure AD 憑據進行授權

使用 Azure AD 憑據登錄到 Azure CLI 時，將返回 OAuth 2.0 訪問權杖。 Azure CLI 會自動使用該權杖來授權針對 Blob 或佇列存儲的後續資料操作。 針對支援的作業，您不需要再使用命令傳遞帳戶金鑰或 SAS 權杖。

您可以通過基於角色的存取控制 （RBAC） 將 Blob 和佇列資料的許可權分配給 Azure AD 安全主體。 有關 Azure 存儲中的 RBAC 角色的詳細資訊，請參閱[使用 RBAC 管理對 Azure 存儲資料的存取權限](storage-auth-aad-rbac.md)。

### <a name="permissions-for-calling-data-operations"></a>調用資料操作的許可權

對於 Blob 和佇列資料的操作，支援 Azure 存儲擴展。 可以調用哪些操作取決於授予登錄到 Azure CLI 的 Azure AD 安全主體的許可權。 Azure 存儲容器或佇列的許可權通過 RBAC 分配。 例如，如果您被分配了**Blob 資料讀取器**角色，則可以運行從容器或佇列讀取資料的指令碼命令。 如果為 Blob**資料參與者角色**分配了腳本，則可以運行讀取、寫入或刪除容器或佇列或其包含的資料的指令碼命令。

有關容器或佇列上每個 Azure 存儲操作所需的許可權的詳細資訊，請參閱使用[OAuth 權杖調用存儲操作](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>示例：授權操作以創建具有 Azure AD 憑據的容器

下面的示例演示如何使用 Azure AD 憑據從 Azure CLI 創建容器。 要創建容器，您需要登錄到 Azure CLI，並且需要資源組和存儲帳戶。 要瞭解如何創建這些資源，請參閱[快速入門：使用 Azure CLI 創建、下載和列出 blob。](../blobs/storage-quickstart-blobs-cli.md)

1. 在創建容器之前，請自行分配[存儲 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色。 即使您是帳戶擁有者，也需要明確權限才能對存儲帳戶執行資料操作。 有關分配 RBAC 角色的詳細資訊，請參閱[在 Azure 門戶中使用 RBAC 授予對 Azure Blob 和佇列資料的存取權限](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色指派可能需要幾分鐘才能傳播。

1. 使用`--auth-mode`參數集`login`使用 Azure AD 憑據創建容器的 az[存儲容器創建](/cli/azure/storage/container#az-storage-container-create)命令。 請記得以您自己的值取代角括號中的預留位置值：

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>使用帳戶訪問金鑰進行授權

如果擁有帳戶金鑰，可以調用任何 Azure 存儲資料操作。 通常，使用帳戶金鑰不太安全。 如果帳戶金鑰被洩露，您的帳戶中的所有資料都可能受到威脅。

下面的示例演示如何使用帳戶訪問金鑰創建容器。 指定帳戶金鑰，`--auth-mode`並提供參數`key`的值：

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>使用 SAS 權杖進行授權

如果您擁有 SAS 權杖，則可以調用 SAS 允許的資料操作。 下面的示例演示如何使用 SAS 權杖創建容器：

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>為授權參數設置環境變數

可以在環境變數中指定授權參數，以避免在每次調用 Azure 存儲資料操作時都包括它們。 下表描述了可用的環境變數。

| 環境變數                  | 描述                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    儲存體帳戶名稱。 此變數應與存儲帳戶金鑰或 SAS 權杖結合使用。 如果兩者都不存在，Azure CLI 會嘗試使用經過身份驗證的 Azure AD 帳戶檢索存儲帳戶訪問金鑰。 如果一次執行大量命令，則可能會達到 Azure 存儲資來源提供者限制。 有關資來源提供者限制的詳細資訊，請參閱[Azure 存儲資來源提供者 的可伸縮性和性能目標](scalability-targets-resource-provider.md)。             |
|    AZURE_STORAGE_KEY                  |    儲存體帳戶金鑰。 此變數必須與存儲帳戶名稱結合使用。                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    包含存儲帳戶金鑰或 SAS 權杖的連接字串。 此變數必須與存儲帳戶名稱結合使用。                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    共用訪問簽名 （SAS） 權杖。 此變數必須與存儲帳戶名稱結合使用。                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    運行命令的授權模式。 允許的值為`login`（推薦）或`key`。 如果指定`login`，Azure CLI 將使用 Azure AD 憑據來授權資料操作。 如果指定舊模式`key`，Azure CLI 將嘗試查詢帳戶訪問金鑰，並授權使用該鍵的命令。    |

## <a name="next-steps"></a>後續步驟

- [使用 Azure CLI 分配 RBAC 角色以訪問 Blob 和佇列資料](storage-auth-aad-rbac-cli.md)
- [使用 Azure 資源的託管標識授權訪問 Blob 和佇列資料](storage-auth-aad-msi.md)
