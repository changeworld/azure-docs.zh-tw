---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c26ef5b857d7295b533079a70959f0f1ef1e8206
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424654"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>為您的函式建立支援的 Azure 資源

若要將函式程式碼部署至 Azure，您必須先建立三個資源：

- 資源群組，這是相關資源的邏輯容器。
- 儲存體帳戶，用來維護專案的狀態和其他資訊。
- 函式應用程式，可提供用來執行函式程式碼的環境。 函式應用程式可對應至您的本機函式專案，並可讓您將函式分組為邏輯單位，以便管理、部署和共用資源。

請使用下列命令來建立這些項目。 Azure CLI 和 PowerShell 均受支援。

1. 如果您尚未登入 Azure，請於此時登入：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az_login) 命令會將您登入您的 Azure 帳戶。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 會將您登入您的 Azure 帳戶。

    ---

1. 在 `westeurope` 區域中，建立名為 `AzureFunctionsQuickstart-rg` 的資源群組：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [az group create](/cli/azure/group#az_group_create) 命令會建立資源群組。 您通常會使用 `az account list-locations` 命令傳回的可用區域，在您附近的區域中建立資源群組和資源。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令會建立資源群組。 您通常會使用 [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) cmdlet 傳回的可用區域，在您附近的區域中建立資源群組和資源。

    ---

1. 在您的資源群組和區域中建立一般用途的儲存體帳戶：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令會建立儲存體帳戶。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) Cmdlet 會建立儲存體帳戶。

    ---

    在上述範例中，請將 `<STORAGE_NAME>` 取代為適合您且在 Azure 儲存體中是唯一的名稱。 名稱只能包含 3 到 24 個字元的數字和小寫字母。 `Standard_LRS` 會指定[受 Functions 支援](../articles/azure-functions/storage-considerations.md#storage-account-requirements)的一般用途帳戶。
    


