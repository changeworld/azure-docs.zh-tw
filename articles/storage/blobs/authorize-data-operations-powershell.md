---
title: 使用 Azure AD 認證來執行 PowerShell 命令以存取 blob 資料
titleSuffix: Azure Storage
description: PowerShell 支援使用 Azure AD 認證登入，以在 Azure 儲存體的 blob 資料上執行命令。 存取權杖是針對工作階段提供，用來授權呼叫作業。 許可權取決於指派給 Azure AD 安全性主體的 Azure 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.openlocfilehash: de42854f512a69b7b70a15a10fe8499ca1d0bf8b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017724"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-data"></a>使用 Azure AD 認證來執行 PowerShell 命令以存取 blob 資料

Azure 儲存體提供 PowerShell 的延伸模組，可讓您以 Azure Active Directory (Azure AD) 認證來登入和執行指令碼命令。 當您使用 Azure AD 認證登入 PowerShell 時，會傳回 OAuth 2.0 存取權杖。 PowerShell 會自動使用該權杖來對 Blob 儲存體授權後續的資料作業。 針對支援的作業，您不需要再使用命令傳遞帳戶金鑰或 SAS 權杖。

您可以透過 Azure RBAC)  (的 Azure 角色型存取控制，將 blob 資料的許可權指派給 Azure AD 的安全性主體。 如需有關 Azure 儲存體中 Azure 角色的詳細資訊，請參閱 [使用 AZURE RBAC 管理 Azure 儲存體資料的存取權限](../common/storage-auth-aad-rbac-portal.md)。

## <a name="supported-operations"></a>支援的作業

Blob 資料的作業支援 Azure 儲存體擴充功能。 您可以呼叫哪些作業取決於授與您用來登入 PowerShell 的 Azure AD 安全性主體的許可權。 Azure 儲存體容器的許可權是透過 Azure RBAC 指派的。 例如，如果您已獲指派 **Blob 資料讀者** 角色，則您可以執行從容器讀取資料的指令碼命令。 如果您已獲指派 **Blob 資料參與者** 角色，則您可以執行指令碼命令，以讀取、寫入或刪除容器或其包含的資料。

如需有關容器上每個 Azure 儲存體作業所需許可權的詳細資訊，請參閱 [使用 OAuth 權杖呼叫儲存體作業](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>使用 Azure AD 認證呼叫 PowerShell 命令

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 登入，並針對 Azure 儲存體使用 Azure AD 認證執行後續作業，請建立儲存體內容來參考儲存體帳戶，並包含 `-UseConnectedAccount` 參數。

下列範例示範如何使用 Azure AD 認證，從 Azure PowerShell 在新的儲存體帳戶中建立容器。 請記得以您自己的值取代角括號中的預留位置值：

1. 使用 [disconnect-azaccount](/powershell/module/az.accounts/connect-azaccount) 命令登入您的 Azure 帳戶：

    ```powershell
    Connect-AzAccount
    ```

    如需使用 PowerShell 登入 Azure 的詳細資訊，請參閱 [使用 Azure PowerShell 登入](/powershell/azure/authenticate-azureps)。

1. 藉由呼叫 [New->new-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)來建立 Azure 資源群組。 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. 藉由呼叫 [New-new-azstorageaccount](/powershell/module/az.storage/new-azstorageaccount)來建立儲存體帳戶。

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. 藉由呼叫 [AzStorageCoNtext](/powershell/module/az.storage/new-azstoragecontext)來取得指定新儲存體帳戶的儲存體帳戶內容。 在儲存體帳戶上進行操作時，您可以參考內容，而不是重複傳遞認證。 包含 `-UseConnectedAccount` 參數，以使用您的 Azure AD 認證來呼叫任何後續的資料作業：

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 建立容器之前，請將 [儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 角色指派給自己。 即使您是帳戶擁有者，您還是需要明確的許可權，才能對儲存體帳戶執行資料作業。 如需指派 Azure 角色的詳細資訊，請參閱 [使用 Azure 入口網站指派 azure 角色以存取 blob 和佇列資料](../common/storage-auth-aad-rbac-portal.md)。

    > [!IMPORTANT]
    > Azure 角色指派可能需要數分鐘的時間傳播。

1. 藉由呼叫 [New->new-azstoragecontainer](/powershell/module/az.storage/new-azstoragecontainer)來建立容器。 因為此呼叫會使用在先前步驟中建立的內容，所以會使用您的 Azure AD 認證來建立容器。

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>後續步驟

- [使用 PowerShell 指派 Azure 角色以存取 blob 和佇列資料](../common/storage-auth-aad-rbac-powershell.md)
- [使用適用于 Azure 資源的受控識別來授權 blob 和佇列資料的存取](../common/storage-auth-aad-msi.md)