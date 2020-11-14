---
title: 建立 premium Azure 檔案共用
description: 瞭解如何使用 Azure 入口網站、Azure PowerShell 模組或 Azure CLI 來建立 Azure premium 檔案共用。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1ec8e4a945f8b8277d05c11bf3673d2e4ab15f9a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626787"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>如何建立 Azure premium 檔案共用

Premium 檔案共用是在固態硬碟 (SSD) 儲存體媒體上提供，適用于需要大量 IO 的工作負載，包括裝載資料庫和高效能運算 (HPC) 。 Premium 檔案共用是以特殊用途的儲存體帳戶類型（稱為 FileStorage 帳戶）來託管。 高階檔案共用是針對高效能和企業級的應用程式所設計，可提供一致的低延遲、高 IOPS 和高輸送量的共用。

本文說明如何使用 [Azure 入口網站](https://portal.azure.com/)、Azure PowerShell 模組和 Azure CLI 來建立這個新的帳戶類型。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- 如果您想要使用 Azure CLI，請[安裝最新版本](/cli/azure/install-azure-cli?view=azure-cli-latest)。
- 如果您想要使用 Azure PowerShell 模組，請 [安裝最新版本](/powershell/azure/install-az-ps?view=azps-4.6.0)。

## <a name="create-a-filestorage-storage-account"></a>建立 FileStorage 儲存體帳戶

每個儲存體帳戶都必須屬於 Azure 資源群組。 資源群組是用來群組 Azure 服務的邏輯容器。 當您建立儲存體帳戶時，可以選擇建立新的資源群組，或使用現有的資源群組。 Premium 檔案共用需要 FileStorage 帳戶。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

### <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com/)。

現在您已經準備好建立儲存體帳戶。

1. 在 [Azure 入口網站中，選取左側功能表上的 [ **儲存體帳戶** ]。

    ![Azure 入口網站主頁面選取儲存體帳戶](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 在出現的 [儲存體帳戶]  視窗上，選擇 [新增]  。
1. 選取要在其中建立儲存體帳戶的訂用帳戶。
1. 在 [資源群組] 欄位下方，選取 [新建]。 輸入新資源群組的名稱，如下圖所示。

1. 接下來，輸入儲存體帳戶的名稱。 您所選擇的名稱在整個 Azure 中必須是唯一的。 名稱的長度必須介於 3 到 24 個字元之間，且只能包含數字和小寫字母。
1. 選取儲存體帳戶的位置，或使用預設位置。
1. 針對 **效能** ，請選取 [ **Premium** ]。

    您必須在 [ **帳戶類型** ] 下拉式清單中選取 [ **Premium** for **FileStorage** ] 做為可用的選項。

1. 選取 [ **帳戶類型** ]，然後選擇 [ **FileStorage** ]。
1. 將 **複寫設定為** 預設值 [ **本機-多餘的儲存體] (LRS)** 。

    ![如何建立 premium 檔案共用的儲存體帳戶](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. 選取 [檢閱 + 建立]  ，以檢閱您的儲存體帳戶設定並建立帳戶。
1. 選取 [建立]。

建立儲存體帳戶資源之後，請流覽至該資源。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="sign-in-to-azure"></a>登入 Azure

請使用 `Connect-AzAccount` 命令並遵循畫面上的指示進行驗證。

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>建立資源群組

若要使用 PowerShell 建立新的資源群組，請使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令：

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>建立 FileStorage 儲存體帳戶

若要從 PowerShell 建立 FileStorage 儲存體帳戶，請使用 [new-azstorageaccount](/powershell/module/az.storage/New-azStorageAccount) 命令：

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要啟動 Azure Cloud Shell，請登入 [Azure 入口網站](https://portal.azure.com)。

如果您想要登入 CLI 的本機安裝，請確定您擁有最新版本，然後登入：

```azurecli
az login
```

### <a name="create-a-resource-group"></a>建立資源群組

若要使用 Azure CLI 建立新的資源群組，請使用 [az group create](/cli/azure/group) 命令。

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>建立 FileStorage 儲存體帳戶

若要從 Azure CLI 建立 FileStorage 儲存體帳戶，請使用 [az storage account create](/cli/azure/storage/account) 命令。

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>取得儲存體帳戶金鑰

儲存體帳戶金鑰可控制儲存體帳戶中資源的存取權，在本文中，我們會使用金鑰來建立 premium 檔案共用。 當您建立儲存體帳戶時，系統會自動建立金鑰。 您可以使用 [az storage account keys list](/cli/azure/storage/account/keys) 命令取得儲存體帳戶的儲存體帳戶金鑰：

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```
---

## <a name="create-a-premium-file-share"></a>建立進階檔案共用

現在您已建立 FileStorage 帳戶，您可以在該儲存體帳戶內建立 premium 檔案共用。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在儲存體帳戶的左側功能表中，移至 [檔案 **服務** ] 區段， **然後選取 [** 檔案]。
1. 選取 [檔案 **共用** ] 以建立 premium 檔案共用。
1. 輸入檔案共用的名稱和所需的配額，然後選取 [ **建立** ]。

> [!NOTE]
> 布建的共用大小是由共用配額指定，檔案共用會依布建的大小計費。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage/files/)。

   ![建立進階檔案共用](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 模組建立 premium 檔案共用，請使用 [AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) Cmdlet。

> [!NOTE]
> 布建的共用大小是由共用配額指定，檔案共用會依布建的大小計費。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage/files/)。

```powershell
New-AzStorageShare `
   -Name myshare `
   -EnabledProtocol SMB `
   -Context $storageAcct.Context
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 建立 premium 檔案共用，請使用 [az storage share create](/cli/azure/storage/share) 命令。

> [!NOTE]
> 布建的共用大小是由共用配額指定，檔案共用會依布建的大小計費。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage/files/)。

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol SMB \
    --name "myshare" 
```
---

## <a name="clean-up-resources"></a>清除資源

# <a name="portal"></a>[入口網站](#tab/azure-portal)

如果您想要清除在本文中建立的資源，請刪除資源群組。 刪除資源群組也會刪除相關聯的儲存體帳戶，以及與資源群組相關聯的任何其他資源。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

如果您想要清除在本文中建立的資源，請刪除資源群組。 刪除資源群組也會刪除相關聯的儲存體帳戶，以及與資源群組相關聯的任何其他資源。

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令： 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果您想要清除在本文中建立的資源，請刪除資源群組。 刪除資源群組也會刪除相關聯的儲存體帳戶，以及與資源群組相關聯的任何其他資源。

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用 [az group delete](/cli/azure/group) 命令。

```azurecli-interactive
az group delete --name myResourceGroup
```
---

## <a name="next-steps"></a>後續步驟

在本文中，您已建立 premium 檔案共用。 若要瞭解此帳戶提供的效能，請繼續進行規劃指南的 [效能層級] 區段。

> [!div class="nextstepaction"]
> [檔案共用層](storage-files-planning.md#storage-tiers)