---
title: 使用 PowerShell 為容器或 Blob 創建使用者委派 SAS
titleSuffix: Azure Storage
description: 瞭解如何使用 PowerShell 使用 Azure 活動目錄憑據創建使用者委派 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5250a27e6c5fcf012207f1edb95ad46c0aabfe63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536168"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>使用 PowerShell 為容器或 Blob 創建使用者委派 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文演示如何使用 Azure 活動目錄 （Azure AD） 憑據為具有 Azure PowerShell 的容器或 Blob 創建使用者委派 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

要使用 PowerShell 創建使用者委派 SAS，請安裝版本 1.10.0 或更高版本的 Az.Storage 模組。 按照以下步驟安裝最新版本的模組：

1. 解除安裝任何先前安裝的 Azure PowerShell：

    - 使用 [設定]**** 底下的 [應用程式與功能]****，從 Windows 移除任何先前安裝的 Azure PowerShell。
    - 從`%Program Files%\WindowsPowerShell\Modules`中刪除所有**Azure**模組。

1. 確定您已安裝最新版的 PowerShellGet。 開啟 Windows PowerShell 視窗，然後執行下列命令來安裝最新版本：

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. 在安裝完 PowerShellGet 之後，關閉並重新開啟 PowerShell 視窗。

1. 安裝最新版的 Azure PowerShell：

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 請確保已安裝 Azure PowerShell 版本 3.2.0 或更高版本。 運行以下命令以安裝最新版本的 Azure 存儲 PowerShell 模組：

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. 關閉並重新開啟 PowerShell 視窗。

要檢查安裝了哪個版本的 Az.Storage 模組，請運行以下命令：

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

有關安裝 Azure PowerShell 的詳細資訊，請參閱[使用 PowerShell 安裝 Azure PowerShell。](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>使用 Azure AD 登錄到 Azure 電源外殼

調用[連接-AzAccount](/powershell/module/az.accounts/connect-azaccount)命令以使用 Azure AD 帳戶登錄：

```powershell
Connect-AzAccount
```

有關使用 PowerShell 登錄的詳細資訊，請參閱[使用 Azure PowerShell 登錄](/powershell/azure/authenticate-azureps)。

## <a name="assign-permissions-with-rbac"></a>使用 RBAC 分配許可權

要從 Azure PowerShell 創建使用者委派 SAS，必須為用於登錄到 PowerShell 的 Azure AD 帳戶分配一個角色，其中包括**Microsoft.存儲/存儲帳戶/blob 服務/生成User委派鍵**操作。 此許可權使 Azure AD 帳戶能夠請求*使用者委派金鑰*。 使用者委派金鑰用於對使用者委派 SAS 進行簽名。 提供**Microsoft.存儲/存儲帳戶/blob 服務/生成User委派鍵**操作的角色必須在存儲帳戶、資源組或訂閱級別分配。 有關創建使用者委派 SAS 的 RBAC 許可權的詳細資訊，請參閱[創建使用者委派 SAS](/rest/api/storageservices/create-user-delegation-sas)中的 RBAC 部分中的 **"分配具有 RBAC 許可權"** 部分。

如果沒有足夠的許可權將 RBAC 角色指派給 Azure AD 安全主體，則可能需要要求帳戶擁有者或管理員分配必要的許可權。

下面的示例分配**存儲 Blob 資料參與者**角色，其中包括**Microsoft.存儲/存儲帳戶/blob 服務/生成User委派鍵**操作。 角色在存儲帳戶級別進行限定。

請記得以您自己的值取代角括號中的預留位置值：

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

有關包括**Microsoft.存儲/存儲帳戶/blob 服務/生成User委派鍵**操作的內置角色的詳細資訊，請參閱[Azure 資源的內置角色](../../role-based-access-control/built-in-roles.md)。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>使用 Azure AD 憑據來保護 SAS

使用 Azure PowerShell 創建使用者委派 SAS 時，將隱式創建用於對 SAS 進行簽名的使用者委派金鑰。 您為 SAS 指定的開始時間和到期時間也用作使用者委派金鑰的開始時間和到期時間。 

由於使用者委派金鑰的有效最大間隔為自開始日期起 7 天，因此應指定在開始時間 7 天內為 SAS 的到期日期。 SAS 在使用者委派金鑰過期後無效，因此過期時間大於 7 天的 SAS 仍然僅有效 7 天。

要使用 Azure PowerShell 為容器或 Blob 創建使用者委派 SAS，請先創建新的 Azure`-UseConnectedAccount`存儲上下文物件，指定參數。 該`-UseConnectedAccount`參數指定該命令在您登錄的 Azure AD 帳戶下創建上下文物件。

請記得以您自己的值取代角括號中的預留位置值：

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>為容器創建使用者委派 SAS

要返回容器的使用者委派 SAS 權杖，請調用[New-AzStorage 容器SASToken](/powershell/module/az.storage/new-azstoragecontainersastoken)命令，在以前創建的 Azure 存儲上下文物件中傳遞。

下面的示例返回容器的使用者委派 SAS 權杖。 請記住將括弧中的預留位置值替換為您自己的值：

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

返回的使用者委派 SAS 權杖將類似于：

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>為 Blob 創建使用者委派 SAS

要返回 Blob 的使用者委派 SAS 權杖，請調用[New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken)命令，在以前創建的 Azure 存儲上下文物件中傳遞。

以下語法返回 Blob 的使用者委派 SAS。 該示例指定參數`-FullUri`，該參數返回附加 SAS 權杖的 blob URI。 請記住將括弧中的預留位置值替換為您自己的值：

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

返回的使用者委派 SAS URI 將類似于：

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> 使用者委派 SAS 不支援使用存儲的訪問策略定義許可權。

## <a name="revoke-a-user-delegation-sas"></a>撤銷使用者委派 SAS

要從 Azure PowerShell 撤銷使用者委派 SAS，請調用 **"撤銷-AzstorageAccountUser委派鍵"** 命令。 此命令撤銷與指定存儲帳戶關聯的所有使用者委派金鑰。 與這些金鑰關聯的任何共用訪問簽名將失效。

請記得以您自己的值取代角括號中的預留位置值：

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> 使用者委派金鑰和 RBAC 角色指派都由 Azure 存儲緩存，因此在啟動吊銷過程和現有使用者委派 SAS 無效之間可能存在延遲。

## <a name="next-steps"></a>後續步驟

- [創建使用者委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
- [獲取使用者委派金鑰操作](/rest/api/storageservices/get-user-delegation-key)
