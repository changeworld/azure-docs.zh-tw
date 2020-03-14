---
title: 建立儲存體帳戶
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來建立儲存體帳戶。 Azure 儲存體帳戶會在 Microsoft Azure 中提供唯一命名空間來儲存及存取您的資料。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/07/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c2d1e8b4975be0657983192df00cc434da00a6f7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255401"
---
# <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

Azure 儲存體帳戶包含您所有的 Azure 儲存體資料物件：Blob、檔案、佇列、資料表和磁碟。 儲存體帳戶會為您的 Azure 儲存體資料提供唯一的命名空間，可透過 HTTP 或 HTTPS 從世界各地存取。 您的 Azure 儲存體帳戶中的資料既持久又高可用性、安全且可大幅擴充。

在此操作說明文章中，您將瞭解如何使用[Azure 入口網站](https://portal.azure.com/)、 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)、 [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)或[Azure Resource Manager 範本](../../azure-resource-manager/management/overview.md)來建立儲存體帳戶。  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

無。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 建立 Azure 儲存體帳戶，請確定您已安裝 Azure PowerShell 模組 Az 0.7 版或更新版本。 如需詳細資訊，請參閱[Azure PowerShell Az 模組簡介](/powershell/azure/new-azureps-module-az)。

若要尋找您目前的版本，請執行下列命令：

```powershell
Get-InstalledModule -Name "Az"
```

若要安裝或升級 Azure PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以登入 Azure，並且以下列兩種方式之一執行 Azure CLI 命令：

- 您可以從 Azure 入口網站的 Azure Cloud Shell 中執行 CLI 命令。
- 您可以安裝 CLI，並在本機執行 CLI 命令。

### <a name="use-azure-cloud-shell"></a>使用 Azure Cloud Shell

Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 Azure CLI 已預先安裝並設定為與您的帳戶搭配使用。 在 Azure 入口網站右上方區段的功能表上，按一下 [ **Cloud Shell** ] 按鈕：

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

按鈕會啟動互動式 shell，讓您用來執行本操作說明文章中所述的步驟：

[![顯示 Cloud Shell 視窗的螢幕擷取畫面](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>在本機安裝 CLI

您也可以在本機安裝及使用 Azure CLI。 本操作說明文章需要您執行 Azure CLI 版本2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

# <a name="template"></a>[範本](#tab/template)

無。

---

## <a name="sign-in-to-azure"></a>登入 Azure

# <a name="portal"></a>[入口網站](#tab/azure-portal)

登入 [Azure 入口網站](https://portal.azure.com)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶，並遵循畫面上的指示進行驗證。

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要啟動 Azure Cloud Shell，請登入[Azure 入口網站](https://portal.azure.com)。

若要登入您的本機安裝 CLI，請執行[az login](/cli/azure/reference-index#az-login)命令：

```azurecli-interactive
az login
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

## <a name="create-a-storage-account"></a>建立儲存體帳戶

現在您已準備好建立儲存體帳戶。

每個儲存體帳戶都必須屬於 Azure 資源群組。 資源群組是用來群組 Azure 服務的邏輯容器。 當您建立儲存體帳戶時，可以選擇建立新的資源群組，或使用現有的資源群組。 本文說明如何建立新的資源群組。

**一般用途 v2** 儲存體帳戶提供所有 Azure 儲存體服務的存取權：Blob、檔案、佇列、資料表和磁碟。 這裡所述的步驟會建立一般用途 v2 儲存體帳戶，但建立任何類型儲存體帳戶的步驟都很類似。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

首先，透過 PowerShell，使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令建立新的資源群組：

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

如果您不確定要針對 `-Location` 參數指定哪一個區域，您可以使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 命令擷取訂用帳戶所支援的區域清單：

```powershell
Get-AzLocation | select Location
```

接下來，使用[new-azstorageaccount](/powershell/module/az.storage/New-azStorageAccount)命令，建立具有讀取權限異地多餘儲存體（RA-GRS）的一般用途 v2 儲存體帳戶。 請記住，您的儲存體帳戶名稱在 Azure 中必須是唯一的，因此請將括弧中的預留位置值取代為您自己的唯一值：

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> 如果您打算使用[Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)，請在此參數清單中包含 `-EnableHierarchicalNamespace $True`。

若要使用不同的複寫選項來建立一般用途 v2 儲存體帳戶，請將下表中所需的值取代為**SkuName**參數。

|複寫選項  |SkuName 參數  |
|---------|---------|
|本機備援儲存體 (LRS)     |Standard_LRS         |
|區域備援儲存體 (ZRS)     |Standard_ZRS         |
|異地備援儲存體 (GRS)     |Standard_GRS         |
|讀取權限異地備援儲存體 (GRS)     |Standard_RAGRS         |
|異地區域-多餘儲存體（切換）（預覽）    |Standard_GZRS         |
|讀取權限異地區域-多餘儲存體（RA-切換）（預覽）    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，透過 Azure CLI，使用 [az group create](/cli/azure/group#az_group_create) 命令來建立新的資源群組。

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

如果您不確定要針對 `--location` 參數指定哪一個區域，您可以使用 [az account list-locations](/cli/azure/account#az_account_list) 命令擷取訂用帳戶所支援的區域清單。

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

接下來，使用[az storage account create](/cli/azure/storage/account#az_storage_account_create)命令，建立具有讀取權限異地多餘儲存體的一般用途 v2 儲存體帳戶。 請記住，您的儲存體帳戶名稱在 Azure 中必須是唯一的，因此請將括弧中的預留位置值取代為您自己的唯一值：

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> 如果您打算使用[Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)，請在此參數清單中包含 `--enable-hierarchical-namespace true`。 

若要使用不同的複寫選項來建立一般用途 v2 儲存體帳戶，請以下表中所需的值取代**sku**參數。

|複寫選項  |sku 參數  |
|---------|---------|
|本機備援儲存體 (LRS)     |Standard_LRS         |
|區域備援儲存體 (ZRS)     |Standard_ZRS         |
|異地備援儲存體 (GRS)     |Standard_GRS         |
|讀取權限異地備援儲存體 (GRS)     |Standard_RAGRS         |
|異地區域-多餘儲存體（切換）（預覽）    |Standard_GZRS         |
|讀取權限異地區域-多餘儲存體（RA-切換）（預覽）    |Standard_RAGZRS         |

# <a name="template"></a>[範本](#tab/template)

您可以使用 Azure PowerShell 或 Azure CLI 來部署 Resource Manager 範本以建立儲存體帳戶。 本操作說明文章中使用的範本是來自[Azure Resource Manager 快速入門範本](https://azure.microsoft.com/resources/templates/101-storage-account-create/)。 若要執行指令碼，請選取 [試試看] 開啟 Azure Cloud shell。 若要貼上指令碼，請以滑鼠右鍵按一下 Shell，然後選取 [貼上]。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> 此範本僅作為範例。 有許多儲存體帳戶設定未設定為此範本的一部分。 例如，如果您想要使用[Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)，您可以藉由將 `StorageAccountPropertiesCreateParameters` 物件的 `isHnsEnabledad` 屬性設定為 `true`來修改此範本。 

若要瞭解如何修改此範本或建立新範本，請參閱：

- [Azure Resource Manager 文件](/azure/azure-resource-manager/)。
- [儲存體帳戶範本參考](/azure/templates/microsoft.storage/allversions)。
- [其他儲存體帳戶範本範例](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)。

---

如需可用複寫選項的詳細資訊，請參閱[儲存體複寫選項](storage-redundancy.md)。

## <a name="delete-a-storage-account"></a>刪除儲存體帳戶

刪除儲存體帳戶會刪除整個帳戶，包括帳戶中的所有資料，而且無法復原。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 流覽至[Azure 入口網站](https://portal.azure.com)中的儲存體帳戶。
1. 按一下 **[刪除]** 。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要刪除儲存體帳戶，請使用[new-azstorageaccount](/powershell/module/az.storage/remove-azstorageaccount)命令：

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要刪除儲存體帳戶，請使用[az storage account delete](/cli/azure/storage/account#az-storage-account-delete)命令：

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[範本](#tab/template)

若要刪除儲存體帳戶，請使用 Azure PowerShell 或 Azure CLI。

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

或者，您可以刪除資源群組，這會刪除該資源群組中的儲存體帳戶和任何其他資源。 如需有關刪除資源群組的詳細資訊，請參閱[刪除資源群組和資源](../../azure-resource-manager/management/delete-resource-group.md)。

> [!WARNING]
> 您無法還原已刪除的儲存體帳戶，也無法擷取刪除之前所包含的任何內容。 請務必先備份您想要儲存的任何資料，再刪除帳戶。 這也適用於帳戶中的任何資源 - 一旦刪除 Blob、資料表、佇列或檔案，就是永久刪除。
>
> 如果您嘗試刪除與 Azure 虛擬機器相關聯的儲存體帳戶，您可能會收到儲存體帳戶仍在使用中的相關錯誤。 如需針對此錯誤進行疑難排解的協助，請參閱針對[刪除儲存體帳戶時的錯誤進行疑難排解](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md)。

## <a name="next-steps"></a>後續步驟

在此操作說明文章中，您已建立一般用途 v2 標準儲存體帳戶。 若要瞭解如何在您的儲存體帳戶之間上傳和下載 blob，請繼續進行其中一個 Blob 儲存體快速入門。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

> [!div class="nextstepaction"]
> [使用 Azure 入口網站處理 Blob](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [使用 PowerShell 處理 Blob](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [使用 Azure CLI 處理 Blob](../blobs/storage-quickstart-blobs-cli.md)

# <a name="template"></a>[範本](#tab/template)

> [!div class="nextstepaction"]
> [使用 Azure 入口網站處理 Blob](../blobs/storage-quickstart-blobs-portal.md)

---
