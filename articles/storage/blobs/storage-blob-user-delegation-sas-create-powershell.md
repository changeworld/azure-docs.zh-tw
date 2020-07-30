---
title: 使用 PowerShell 建立容器或 blob 的使用者委派 SAS
titleSuffix: Azure Storage
description: 瞭解如何使用 PowerShell 建立具有 Azure Active Directory 認證的使用者委派 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 99d8ab3b7edc2d32e580ec8de9132fef3ea3503a
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424336"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>使用 PowerShell 建立容器或 blob 的使用者委派 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文說明如何使用 Azure Active Directory （Azure AD）認證，以 Azure PowerShell 建立容器或 blob 的使用者委派 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

若要使用 PowerShell 建立使用者委派 SAS，請安裝1.10.0 或更新版本的 Az. Storage 模組。 請遵循下列步驟來安裝模組的最新版本：

1. 解除安裝任何先前安裝的 Azure PowerShell：

    - 使用 [設定]**** 底下的 [應用程式與功能]****，從 Windows 移除任何先前安裝的 Azure PowerShell。
    - 從移除所有**Azure**模組 `%Program Files%\WindowsPowerShell\Modules` 。

1. 確定您已安裝最新版的 PowerShellGet。 開啟 Windows PowerShell 視窗，然後執行下列命令來安裝最新版本：

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. 在安裝完 PowerShellGet 之後，關閉並重新開啟 PowerShell 視窗。

1. 安裝最新版的 Azure PowerShell：

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 請確定您已安裝 Azure PowerShell 版3.2.0 或更新版本。 執行下列命令，以安裝最新版的 Azure 儲存體 PowerShell 模組：

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. 關閉並重新開啟 PowerShell 視窗。

若要檢查已安裝的 Az. Storage 模組版本，請執行下列命令：

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

如需如何安裝 Azure PowerShell 的詳細資訊，請參閱[使用 PowerShellGet 安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>使用 Azure AD 登入 Azure PowerShell

呼叫[disconnect-azaccount](/powershell/module/az.accounts/connect-azaccount)命令，以使用您的 Azure AD 帳戶登入：

```powershell
Connect-AzAccount
```

如需使用 PowerShell 登入的詳細資訊，請參閱[使用 Azure PowerShell 登入](/powershell/azure/authenticate-azureps)。

## <a name="assign-permissions-with-rbac"></a>使用 RBAC 指派許可權

若要從 Azure PowerShell 建立使用者委派 SAS，您必須將用來登入 PowerShell 的 Azure AD 帳戶指派為包含**generateUserDelegationKey**動作的角色。 此許可權可讓 Azure AD 帳戶要求*使用者委派金鑰*。 使用者委派金鑰是用來簽署使用者委派 SAS。 提供**Microsoft 儲存體/storageAccounts/blobServices/generateUserDelegationKey**動作的角色，必須在儲存體帳戶、資源群組或訂用帳戶的層級指派。 如需建立使用者委派 SAS 之 RBAC 許可權的詳細資訊，請參閱[建立使用者委派 sas](/rest/api/storageservices/create-user-delegation-sas)中的**使用 rbac 指派許可權**一節。

如果您沒有足夠的許可權可將 RBAC 角色指派給 Azure AD 的安全性主體，您可能需要要求帳戶擁有者或系統管理員指派必要的許可權。

下列範例會指派**儲存體 Blob 資料參與者**角色，其中包括**Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey**動作。 角色的範圍設定為儲存體帳戶的層級。

請記得以您自己的值取代角括號中的預留位置值：

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

如需包含**storageAccounts/blobServices/generateUserDelegationKey**動作之內建角色的詳細資訊，請參閱[Azure 內建角色](../../role-based-access-control/built-in-roles.md)。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>使用 Azure AD 認證來保護 SAS

當您使用 Azure PowerShell 建立使用者委派 SAS 時，會隱含地為您建立用來簽署 SAS 的使用者委派金鑰。 您為 SAS 指定的開始時間和到期時間也會用來做為使用者委派金鑰的開始時間和到期時間。 

因為使用者委派金鑰有效的最大間隔是從開始日期起算的7天，所以您應該指定在開始時間的7天內的 SAS 到期時間。 在使用者委派金鑰過期之後，SAS 無效，因此到期時間超過7天的 SAS 仍然有效7天。

若要使用 Azure PowerShell 建立容器或 blob 的使用者委派 SAS，請先建立新的 Azure 儲存體內容物件，並指定 `-UseConnectedAccount` 參數。 `-UseConnectedAccount`參數指定命令會在您用來登入的 Azure AD 帳戶底下建立內容物件。

請記得以您自己的值取代角括號中的預留位置值：

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>建立容器的使用者委派 SAS

若要傳回容器的使用者委派 SAS 權杖，請呼叫[AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken)命令，並傳入您先前建立的 Azure 儲存體內容物件。

下列範例會傳回容器的使用者委派 SAS 權杖。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

傳回的使用者委派 SAS 權杖將類似于：

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>建立 blob 的使用者委派 SAS

若要傳回 blob 的使用者委派 SAS 權杖，請呼叫[AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken)命令，並傳入您先前建立的 Azure 儲存體內容物件。

下列語法會傳回 blob 的使用者委派 SAS。 此範例會指定 `-FullUri` 參數，傳回已附加 SAS 權杖的 BLOB URI。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

傳回的使用者委派 SAS URI 將類似于：

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> 使用者委派 SAS 不支援使用預存存取原則來定義許可權。

## <a name="revoke-a-user-delegation-sas"></a>撤銷使用者委派 SAS

若要撤銷 Azure PowerShell 的使用者委派 SAS，請呼叫**AzStorageAccountUserDelegationKeys**命令。 此命令會撤銷與指定的儲存體帳戶相關聯的所有使用者委派金鑰。 任何與這些金鑰相關聯的共用存取簽章都會失效。

請記得以您自己的值取代角括號中的預留位置值：

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Azure 儲存體會快取使用者委派金鑰和 Azure 角色指派，因此當您起始撤銷的進程，以及現有的使用者委派 SAS 失效時，可能會有延遲。

## <a name="next-steps"></a>後續步驟

- [建立使用者委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
- [取得使用者委派金鑰作業](/rest/api/storageservices/get-user-delegation-key)
