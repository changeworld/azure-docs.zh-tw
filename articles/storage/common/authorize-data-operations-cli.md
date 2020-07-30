---
title: 使用 Azure CLI 授權 blob 或佇列資料的存取權
titleSuffix: Azure Storage
description: 指定如何使用 Azure CLI 對 blob 或佇列資料授權資料作業。 您可以使用 Azure AD 認證、帳戶存取金鑰或共用存取簽章（SAS）權杖來授權資料作業。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 1e0b3890c50a476e4c3b3247db435c6032bf9bbc
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417296"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>使用 Azure CLI 授權 blob 或佇列資料的存取權

Azure 儲存體提供 Azure CLI 的延伸模組，可讓您指定您想要如何授權 blob 或佇列資料的作業。 您可以利用下列方式來授權資料作業：

- 具有 Azure Active Directory （Azure AD）安全性主體。 Microsoft 建議使用 Azure AD 認證，以獲得更佳的安全性和易用性。
- 使用帳戶存取金鑰或共用存取簽章（SAS）權杖。

## <a name="specify-how-data-operations-are-authorized"></a>指定資料作業的授權方式

用來讀取和寫入 blob 和佇列資料的 Azure CLI 命令包含選擇性 `--auth-mode` 參數。 指定此參數以指示要如何授權資料作業：

- 將 `--auth-mode` 參數設定為 `login` ，以使用 Azure AD 安全性主體進行登入（建議）。
- 將 `--auth-mode` 參數設定為舊版 `key` 值，以嘗試取出帳戶存取金鑰以用於授權。 如果您省略 `--auth-mode` 參數，則 Azure CLI 也會嘗試取得存取金鑰。

若要使用 `--auth-mode` 參數，請確定您已安裝 Azure CLI 版2.0.46 版或更新版本。 執行 `az --version` 來檢查您安裝的版本。

> [!IMPORTANT]
> 如果您省略 `--auth-mode` 參數，或將它設為 `key` ，則 Azure CLI 會嘗試使用帳戶存取金鑰進行授權。 在此情況下，Microsoft 建議您在命令或**AZURE_STORAGE_KEY**環境變數中提供存取金鑰。 如需有關環境變數的詳細資訊，請參閱標題為[授權參數設定環境變數](#set-environment-variables-for-authorization-parameters)一節。
>
> 如果您未提供存取金鑰，則 Azure CLI 會嘗試呼叫 Azure 儲存體資源提供者，以針對每個作業取得它。 執行許多需要呼叫資源提供者的資料作業，可能會導致節流。 如需資源提供者限制的詳細資訊，請參閱[Azure 儲存體資源提供者的擴充性和效能目標](scalability-targets-resource-provider.md)。

## <a name="authorize-with-azure-ad-credentials"></a>使用 Azure AD 認證進行授權

當您使用 Azure AD 認證登入 Azure CLI 時，會傳回 OAuth 2.0 存取權杖。 Azure CLI 會自動使用該權杖來對 Blob 或佇列儲存體授權後續的資料作業。 針對支援的作業，您不需要再使用命令傳遞帳戶金鑰或 SAS 權杖。

您可以透過角色型存取控制（RBAC），將 blob 和佇列資料的許可權指派給 Azure AD 的安全性主體。 如需 Azure 儲存體中 RBAC 角色的詳細資訊，請參閱[使用 rbac 來管理 Azure 儲存體資料的存取權限](storage-auth-aad-rbac.md)。

### <a name="permissions-for-calling-data-operations"></a>呼叫資料作業的許可權

支援對 blob 和佇列資料的作業進行 Azure 儲存體延伸模組。 您可能呼叫的作業取決於授與您用來登入 Azure CLI 之 Azure AD 安全性主體的許可權。 Azure 儲存體容器或佇列的許可權會透過 RBAC 來指派。 例如，如果您被指派「 **Blob 資料讀取**者」角色，則您可以執行從容器或佇列讀取資料的指令碼命令。 如果您已獲指派**Blob 資料參與者**角色，則您可以執行讀取、寫入或刪除容器或佇列或其包含之資料的指令碼命令。

如需有關容器或佇列上每個 Azure 儲存體作業所需許可權的詳細資訊，請參閱[使用 OAuth 權杖呼叫儲存體作業](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>範例：授權作業來建立具有 Azure AD 認證的容器

下列範例顯示如何使用您的 Azure AD 認證，從 Azure CLI 建立容器。 若要建立容器，您必須登入 Azure CLI，而且您需要一個資源群組和一個儲存體帳戶。 若要瞭解如何建立這些資源，請參閱[快速入門：使用 Azure CLI 建立、下載及列出 blob](../blobs/storage-quickstart-blobs-cli.md)。

1. 建立容器之前，請將 [儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 角色指派給自己。 即使您是帳戶擁有者，您還是需要明確的許可權，才能對儲存體帳戶執行資料作業。 如需指派 RBAC 角色的詳細資訊，請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure blob 和佇列資料的存取權](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > Azure 角色指派可能需要幾分鐘的時間才能傳播。

1. 呼叫[az storage container create](/cli/azure/storage/container#az-storage-container-create)命令，並將 `--auth-mode` 參數設定為， `login` 以使用您的 Azure AD 認證來建立容器。 請記得以您自己的值取代角括號中的預留位置值：

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>使用帳戶存取金鑰進行授權

如果您擁有帳戶金鑰，您可以呼叫任何 Azure 儲存體資料作業。 一般而言，使用帳戶金鑰比較不安全。 如果帳戶金鑰遭到入侵，您帳戶中的所有資料可能會遭到入侵。

下列範例顯示如何使用帳戶存取金鑰來建立容器。 指定帳戶金鑰，並提供 `--auth-mode` 具有下列值的參數 `key` ：

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>使用 SAS 權杖進行授權

如果您擁有 SAS 權杖，您可以呼叫 SAS 所允許的資料作業。 下列範例顯示如何使用 SAS 權杖來建立容器：

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>設定授權參數的環境變數

您可以在環境變數中指定授權參數，以避免在每次呼叫 Azure 儲存體資料作業時包含它們。 下表描述可用的環境變數。

| 環境變數                  | 描述                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    儲存體帳戶名稱。 此變數應該與儲存體帳戶金鑰或 SAS 權杖搭配使用。 如果兩者都不存在，Azure CLI 會嘗試使用已驗證的 Azure AD 帳戶來抓取儲存體帳戶存取金鑰。 如果一次執行大量命令，可能會達到 Azure 儲存體資源提供者節流限制。 如需資源提供者限制的詳細資訊，請參閱[Azure 儲存體資源提供者的擴充性和效能目標](scalability-targets-resource-provider.md)。             |
|    AZURE_STORAGE_KEY                  |    儲存體帳戶金鑰。 此變數必須與儲存體帳戶名稱一起使用。                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    包含儲存體帳戶金鑰或 SAS 權杖的連接字串。 此變數必須與儲存體帳戶名稱一起使用。                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    共用存取簽章（SAS）權杖。 此變數必須與儲存體帳戶名稱一起使用。                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    用來執行命令的授權模式。 允許的值為 `login` （建議）或 `key` 。 如果您指定 `login` ，則 Azure CLI 會使用您的 Azure AD 認證來授權資料作業。 如果您指定舊版 `key` 模式，Azure CLI 會嘗試查詢帳戶存取金鑰，並使用金鑰來授權命令。    |

## <a name="next-steps"></a>後續步驟

- [使用 Azure CLI 來指派用於存取 blob 和佇列資料的 RBAC 角色](storage-auth-aad-rbac-cli.md)
- [使用 Azure 資源的受控識別來授權 blob 和佇列資料的存取](storage-auth-aad-msi.md)
