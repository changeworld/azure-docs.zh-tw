---
title: 使用 Azure AD 認證執行 PowerShell 命令，以存取 blob 或佇列資料
titleSuffix: Azure Storage
description: PowerShell 支援使用 Azure AD 認證進行登入，以在 Azure 儲存體 blob 和佇列資料上執行命令。 存取權杖是針對工作階段提供，用來授權呼叫作業。 許可權取決於指派給 Azure AD 安全性主體的 Azure 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a99b2841441e43ac02688494b1324fa5d630dcd5
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534918"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>使用 Azure AD 認證執行 PowerShell 命令，以存取 blob 或佇列資料

Azure 儲存體提供 PowerShell 的延伸模組，可讓您使用 Azure Active Directory （Azure AD）認證來登入及執行指令碼命令。 當您使用 Azure AD 認證登入 PowerShell 時，會傳回 OAuth 2.0 存取權杖。 PowerShell 會自動使用該權杖來授權對 Blob 或佇列儲存體的後續資料作業。 針對支援的作業，您不需要再使用命令傳遞帳戶金鑰或 SAS 權杖。

您可以透過角色型存取控制（RBAC），將 blob 和佇列資料的許可權指派給 Azure AD 的安全性主體。 如需 Azure 儲存體中 Azure 角色的詳細資訊，請參閱[使用 RBAC 來管理 Azure 儲存體資料的存取權限](storage-auth-aad-rbac.md)。

## <a name="supported-operations"></a>支援的作業

支援對 blob 和佇列資料的作業進行 Azure 儲存體延伸模組。 您可能呼叫的作業取決於授與您用來登入 PowerShell 之 Azure AD 安全性主體的許可權。 Azure 儲存體容器或佇列的許可權會透過 RBAC 來指派。 例如，如果您已獲指派「 **Blob 資料讀取**者」角色，則您可以執行從容器或佇列讀取資料的指令碼命令。 如果您已獲指派**Blob 資料參與者**角色，則您可以執行讀取、寫入或刪除容器或佇列或其包含之資料的指令碼命令。

如需有關容器或佇列上每個 Azure 儲存體作業所需許可權的詳細資訊，請參閱[使用 OAuth 權杖呼叫儲存體作業](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>使用 Azure AD 認證呼叫 PowerShell 命令

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 來登入，並對使用 Azure AD 認證的 Azure 儲存體執行後續作業，請建立儲存體內容來參考儲存體帳戶，並包含 `-UseConnectedAccount` 參數。

下列範例示範如何使用您的 Azure AD 認證，從 Azure PowerShell 在新的儲存體帳戶中建立容器。 請記得以您自己的值取代角括號中的預留位置值：

1. 使用[disconnect-azaccount](/powershell/module/az.accounts/connect-azaccount)命令登入您的 Azure 帳戶：

    ```powershell
    Connect-AzAccount
    ```

    如需使用 PowerShell 登入 Azure 的詳細資訊，請參閱[使用 Azure PowerShell 登入](/powershell/azure/authenticate-azureps)。

1. 藉由呼叫[remove-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)來建立 Azure 資源群組。 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. 藉由呼叫[new-azstorageaccount](/powershell/module/az.storage/new-azstorageaccount)來建立儲存體帳戶。

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. 藉由呼叫[AzStorageCoNtext](/powershell/module/az.storage/new-azstoragecontext)，取得指定新儲存體帳戶的儲存體帳戶內容。 在儲存體帳戶上作用時，您可以參考內容，而不是重複傳入認證。 包含 `-UseConnectedAccount` 參數，以使用您的 Azure AD 認證來呼叫任何後續的資料作業：

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 建立容器之前，請將 [儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 角色指派給自己。 即使您是帳戶擁有者，您還是需要明確的許可權，才能對儲存體帳戶執行資料作業。 如需指派 Azure 角色的詳細資訊，請參閱[在 Azure 入口網站中使用 RBAC 授與 azure blob 和佇列資料的存取權](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > Azure 角色指派可能需要幾分鐘的時間才能傳播。

1. 藉由呼叫[new-azstoragecontainer](/powershell/module/az.storage/new-azstoragecontainer)來建立容器。 因為此呼叫會使用在先前步驟中建立的內容，所以會使用您的 Azure AD 認證來建立容器。

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>後續步驟

- [使用 PowerShell 來指派 Azure 角色以存取 blob 和佇列資料](storage-auth-aad-rbac-powershell.md)
- [使用 Azure 資源的受控識別來授權 blob 和佇列資料的存取](storage-auth-aad-msi.md)
