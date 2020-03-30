---
title: 使用 Azure AD 憑據運行 PowerShell 命令以訪問 Blob 或佇列資料
titleSuffix: Azure Storage
description: PowerShell 支援使用 Azure AD 憑據登錄，以在 Azure 存儲 Blob 和佇列資料上運行命令。 存取權杖是針對工作階段提供，用來授權呼叫作業。 許可權取決於分配給 Azure AD 安全主體的 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75553445"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>使用 Azure AD 憑據運行 PowerShell 命令以訪問 Blob 或佇列資料

Azure 存儲為 PowerShell 提供擴展，使您能夠使用 Azure 活動目錄 （Azure AD） 憑據登錄和運行指令碼命令。 使用 Azure AD 憑據登錄到 PowerShell 時，將返回 OAuth 2.0 訪問權杖。 PowerShell 會自動使用該權杖來授權針對 Blob 或佇列存儲的後續資料操作。 針對支援的作業，您不需要再使用命令傳遞帳戶金鑰或 SAS 權杖。

您可以通過基於角色的存取控制 （RBAC） 將 Blob 和佇列資料的許可權分配給 Azure AD 安全主體。 有關 Azure 存儲中的 RBAC 角色的詳細資訊，請參閱[使用 RBAC 管理對 Azure 存儲資料的存取權限](storage-auth-aad-rbac.md)。

## <a name="supported-operations"></a>支援的作業

對於 Blob 和佇列資料的操作，支援 Azure 存儲擴展。 可以調用哪些操作取決於授予登錄到 PowerShell 的 Azure AD 安全主體的許可權。 Azure 存儲容器或佇列的許可權通過 RBAC 分配。 例如，如果您已分配**Blob 資料讀取器**角色，則可以運行從容器或佇列讀取資料的指令碼命令。 如果已分配 Blob**資料參與者**角色，則可以運行讀取、寫入或刪除容器或佇列或其包含的資料的指令碼命令。

有關容器或佇列上每個 Azure 存儲操作所需的許可權的詳細資訊，請參閱使用[OAuth 權杖調用存儲操作](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>使用 Azure AD 憑據調用 PowerShell 命令

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

使用 Azure PowerShell 使用 Azure AD 憑據登錄到 Azure 存儲並運行後續操作，請創建存儲上下文以引用存儲帳戶，`-UseConnectedAccount`並包含參數。

下面的示例演示如何使用 Azure AD 憑據從 Azure PowerShell 創建新存儲帳戶中的容器。 請記得以您自己的值取代角括號中的預留位置值：

1. 使用[連接-AzAccount](/powershell/module/az.accounts/connect-azaccount)命令登錄到 Azure 帳戶：

    ```powershell
    Connect-AzAccount
    ```

    有關使用 PowerShell 登錄到 Azure 的詳細資訊，請參閱[使用 Azure PowerShell 登錄](/powershell/azure/authenticate-azureps)。

1. 通過調用[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)創建 Azure 資源組。 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. 通過調用[New-AzStorage 帳戶](/powershell/module/az.storage/new-azstorageaccount)創建存儲帳戶。

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. 通過調用[New-AzStorageCoNtext](/powershell/module/az.storage/new-azstoragecontext)獲取指定新存儲帳戶的存儲帳戶上下文。 在存儲帳戶上操作時，可以引用上下文，而不是重複傳入憑據。 包括使用`-UseConnectedAccount`Azure AD 憑據調用任何後續資料操作的參數：

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 在創建容器之前，請自行分配[存儲 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色。 即使您是帳戶擁有者，也需要明確權限才能對存儲帳戶執行資料操作。 有關分配 RBAC 角色的詳細資訊，請參閱[在 Azure 門戶中使用 RBAC 授予對 Azure Blob 和佇列資料的存取權限](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色指派可能需要幾分鐘才能傳播。

1. 通過調用[New-Az 存儲容器創建容器](/powershell/module/az.storage/new-azstoragecontainer)。 由於此調用使用在前面的步驟中創建的上下文，所以容器是使用 Azure AD 憑據創建的。

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>後續步驟

- [使用 PowerShell 分配 RBAC 角色以訪問 Blob 和佇列資料](storage-auth-aad-rbac-powershell.md)
- [使用 Azure 資源的託管標識授權訪問 Blob 和佇列資料](storage-auth-aad-msi.md)
