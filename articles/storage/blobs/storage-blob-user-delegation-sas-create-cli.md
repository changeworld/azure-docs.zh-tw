---
title: 使用 Azure CLI 為容器或 Blob 創建使用者委派 SAS
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure CLI 使用 Azure 活動目錄憑據創建使用者委派 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: e1a81b25042501a166cee122279d21e3702cd419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371984"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>使用 Azure CLI 為容器或 Blob 創建使用者委派 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文演示如何使用 Azure 活動目錄 （Azure AD） 憑據使用 Azure CLI 為容器或 Blob 創建使用者委派 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>安裝最新版的 Azure CLI

要使用 Azure CLI 使用 Azure AD 憑據保護 SAS，請先確保您已安裝最新版本的 Azure CLI。 有關安裝 Azure CLI 的詳細資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

要使用 Azure CLI 創建使用者委派 SAS，請確保已安裝版本 2.0.78 或更高版本。 要檢查已安裝的版本，請使用`az --version`命令。

## <a name="sign-in-with-azure-ad-credentials"></a>使用 Azure AD 憑據登錄

使用 Azure AD 憑據登錄到 Azure CLI。 如需詳細資訊，請參閱[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli)。

## <a name="assign-permissions-with-rbac"></a>使用 RBAC 分配許可權

要從 Azure PowerShell 創建使用者委派 SAS，必須為用於登錄到 Azure CLI 的 Azure AD 帳戶分配一個角色，其中包括**Microsoft.存儲/存儲帳戶/blob 服務/生成User委派鍵**操作。 此許可權使 Azure AD 帳戶能夠請求*使用者委派金鑰*。 使用者委派金鑰用於對使用者委派 SAS 進行簽名。 提供**Microsoft.存儲/存儲帳戶/blob 服務/生成User委派鍵**操作的角色必須在存儲帳戶、資源組或訂閱級別分配。

如果沒有足夠的許可權將 RBAC 角色指派給 Azure AD 安全主體，則可能需要要求帳戶擁有者或管理員分配必要的許可權。

下面的示例分配**存儲 Blob 資料參與者**角色，其中包括**Microsoft.存儲/存儲帳戶/blob 服務/生成User委派鍵**操作。 角色在存儲帳戶級別進行限定。

請記得以您自己的值取代角括號中的預留位置值：

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

有關包括**Microsoft.存儲/存儲帳戶/blob 服務/生成User委派鍵**操作的內置角色的詳細資訊，請參閱[Azure 資源的內置角色](../../role-based-access-control/built-in-roles.md)。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>使用 Azure AD 憑據來保護 SAS

使用 Azure CLI 創建使用者委派 SAS 時，將隱式創建用於對 SAS 進行簽名的使用者委派金鑰。 您為 SAS 指定的開始時間和到期時間也用作使用者委派金鑰的開始時間和到期時間。

由於使用者委派金鑰的有效最大間隔為自開始日期起 7 天，因此應指定在開始時間 7 天內為 SAS 的到期日期。 SAS 在使用者委派金鑰過期後無效，因此過期時間大於 7 天的 SAS 仍然僅有效 7 天。

創建使用者委派 SAS 時，`--auth-mode login`需要`--as-user parameters`和 為`--auth-mode`參數指定*登錄名*，以便使用 Azure AD 憑據授權對 Azure 存儲發出的請求。 指定參數`--as-user`以指示返回的 SAS 應為使用者委派 SAS。

### <a name="create-a-user-delegation-sas-for-a-container"></a>為容器創建使用者委派 SAS

要使用 Azure CLI 為容器創建使用者委派 SAS，請調用[az 存儲容器生成 sas](/cli/azure/storage/container#az-storage-container-generate-sas)命令。

受支援的使用者委派容器上的 SAS 的許可權包括添加、創建、刪除、清單、讀取和寫入。 可以單獨指定許可權或組合許可權。 有關這些許可權的詳細資訊，請參閱[創建使用者委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。

下面的示例返回容器的使用者委派 SAS 權杖。 請記住將括弧中的預留位置值替換為您自己的值：

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

返回的使用者委派 SAS 權杖將類似于：

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>為 Blob 創建使用者委派 SAS

要使用 Azure CLI 為 Blob 創建使用者委派 SAS，請調用[az 存儲 Blob 生成 sas](/cli/azure/storage/blob#az-storage-blob-generate-sas)命令。

使用者委派 SAS 在 blob 上的許可權包括添加、創建、刪除、讀取和寫入。 可以單獨指定許可權或組合許可權。 有關這些許可權的詳細資訊，請參閱[創建使用者委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。

以下語法返回 Blob 的使用者委派 SAS。 該示例指定參數`--full-uri`，該參數返回附加 SAS 權杖的 blob URI。 請記住將括弧中的預留位置值替換為您自己的值：

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

返回的使用者委派 SAS URI 將類似于：

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> 使用者委派 SAS 不支援使用存儲的訪問策略定義許可權。

## <a name="revoke-a-user-delegation-sas"></a>撤銷使用者委派 SAS

要從 Azure CLI 撤銷使用者委派 SAS，請調用[az 存儲帳戶撤銷委派金鑰](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys)命令。 此命令撤銷與指定存儲帳戶關聯的所有使用者委派金鑰。 與這些金鑰關聯的任何共用訪問簽名將失效。

請記得以您自己的值取代角括號中的預留位置值：

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> 使用者委派金鑰和 RBAC 角色指派都由 Azure 存儲緩存，因此在啟動吊銷過程和現有使用者委派 SAS 無效之間可能存在延遲。

## <a name="next-steps"></a>後續步驟

- [創建使用者委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
- [獲取使用者委派金鑰操作](/rest/api/storageservices/get-user-delegation-key)
