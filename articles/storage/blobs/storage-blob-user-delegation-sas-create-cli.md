---
title: 使用 Azure CLI 建立容器或 blob 的使用者委派 SAS
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure CLI 來建立具有 Azure Active Directory 認證的使用者委派 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 536cd01fbcf2c5d18a8c12030b709427d9bb91b1
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703601"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>使用 Azure CLI 建立容器或 blob 的使用者委派 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文說明如何使用 Azure Active Directory (Azure AD) 認證，為具有 Azure CLI 的容器或 blob 建立使用者委派 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>安裝最新版的 Azure CLI

若要使用 Azure CLI 以 Azure AD 認證來保護 SAS，請先確定您已安裝最新版本的 Azure CLI。 如需安裝 Azure CLI 的詳細資訊，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

若要使用 Azure CLI 建立使用者委派 SAS，請確定您已安裝2.0.78 版或更新版本。 若要檢查已安裝的版本，請使用 `az --version` 命令。

## <a name="sign-in-with-azure-ad-credentials"></a>使用 Azure AD 認證登入

使用您的 Azure AD 認證登入 Azure CLI。 如需詳細資訊，請參閱[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli)。

## <a name="assign-permissions-with-azure-rbac"></a>使用 Azure RBAC 指派許可權

若要從 Azure PowerShell 建立使用者委派 SAS，您必須將用來登入 Azure CLI 的 Azure AD 帳戶指派給包含 **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** 動作的角色。 此許可權可讓該 Azure AD 帳戶要求 *使用者委派金鑰*。 使用者委派金鑰是用來簽署使用者委派 SAS。 提供 storageAccounts **/blobServices/generateUserDelegationKey** 動作的角色必須在儲存體帳戶、資源群組或訂用帳戶層級指派。

如果您沒有足夠的許可權可將 Azure 角色指派給 Azure AD 安全性主體，您可能需要要求帳戶擁有者或系統管理員指派必要的許可權。

下列範例會指派 **儲存體 Blob 資料參與者** 角色，其中包括 **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** 動作。 角色的範圍是儲存體帳戶的層級。

請記得以您自己的值取代角括號中的預留位置值：

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

如需內建角色（包括 **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** 動作）的詳細資訊，請參閱 [Azure 內建角色](../../role-based-access-control/built-in-roles.md)。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>使用 Azure AD 認證來保護 SAS

當您使用 Azure CLI 建立使用者委派 SAS 時，會以隱含方式為您建立用來簽署 SAS 的使用者委派金鑰。 您為 SAS 指定的開始時間和到期時間也會用來做為使用者委派金鑰的開始時間和到期時間。

由於使用者委派金鑰有效的最大間隔是從開始日期起算的7天，您應該指定在開始時間的7天內的 SAS 到期時間。 當使用者委派金鑰到期之後，SAS 無效，因此到期時間超過7天的 SAS 將仍有效7天。

建立使用者委派 SAS 時， `--auth-mode login` `--as-user parameters` 需要和。 指定參數的 *登* 入， `--auth-mode` 以便向 Azure 儲存體提出的要求會獲得 Azure AD 認證的授權。 指定 `--as-user` 參數以指出傳回的 sas 應該是使用者委派 sas。

### <a name="create-a-user-delegation-sas-for-a-container"></a>建立容器的使用者委派 SAS

若要為具有 Azure CLI 的容器建立使用者委派 SAS，請呼叫 [az storage container 產生 SAS](/cli/azure/storage/container#az-storage-container-generate-sas) 命令。

容器上使用者委派 SAS 的支援許可權包括新增、建立、刪除、列出、讀取及寫入。 您可以單獨或合併來指定許可權。 如需這些許可權的詳細資訊，請參閱 [建立使用者委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。

下列範例會傳回容器的使用者委派 SAS 權杖。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

傳回的使用者委派 SAS 權杖將如下所示：

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>建立 blob 的使用者委派 SAS

若要使用 Azure CLI 建立 blob 的使用者委派 SAS，請呼叫 [az storage blob 產生 sas](/cli/azure/storage/blob#az-storage-blob-generate-sas) 命令。

Blob 上的使用者委派 SAS 支援的許可權包括新增、建立、刪除、讀取和寫入。 您可以單獨或合併來指定許可權。 如需這些許可權的詳細資訊，請參閱 [建立使用者委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。

下列語法會傳回 blob 的使用者委派 SAS。 此範例會指定 `--full-uri` 參數，此參數會傳回已附加 SAS 權杖的 BLOB URI。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user \
    --full-uri
```

傳回的使用者委派 SAS URI 將類似于：

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> 使用者委派 SAS 不支援使用預存存取原則來定義許可權。

## <a name="revoke-a-user-delegation-sas"></a>撤銷使用者委派 SAS

若要撤銷 Azure CLI 的使用者委派 SAS，請呼叫 [az storage account revoke 委派金鑰](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) 命令。 此命令會撤銷與指定的儲存體帳戶相關聯的所有使用者委派金鑰。 任何與這些金鑰相關聯的共用存取簽章都會失效。

請記得以您自己的值取代角括號中的預留位置值：

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Azure 儲存體會快取使用者委派金鑰和 Azure 角色指派，因此當您起始撤銷的進程以及現有的使用者委派 SAS 失效時，可能會有延遲。

## <a name="next-steps"></a>後續步驟

- [建立使用者委派 SAS (REST API) ](/rest/api/storageservices/create-user-delegation-sas)
- [取得使用者委派金鑰操作](/rest/api/storageservices/get-user-delegation-key)
