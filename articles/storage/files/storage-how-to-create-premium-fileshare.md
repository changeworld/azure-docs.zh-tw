---
title: 建立 premium Azure 檔案共用
description: 在本文中，您將瞭解如何使用 Azure 入口網站、PowerShell 或 Azure CLI 來建立 premium Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: adeb1635489441b30c15fee69922e3abef0a53f9
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903811"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>如何建立 premium Azure 檔案共用
高階檔案共用是在固態磁片 (SSD) 儲存體媒體上提供，適用于需要大量 IO 的工作負載，包括裝載資料庫和高效能計算 (HPC) 。 高階檔案共用裝載在特殊用途的儲存體帳戶類型中，稱為 FileStorage 帳戶。 Premium 檔案共用是針對高效能和企業級應用程式所設計，可提供一致的低延遲、高 IOPS 和高輸送量的共用。

本文說明如何使用[Azure 入口網站](https://portal.azure.com/)、Azure PowerShell 和 Azure CLI 來建立這個新帳戶類型。

## <a name="prerequisites"></a>必要條件

若要存取 Azure 資源（包括 premium Azure 檔案共用），您將需要 Azure 訂用帳戶。 如果您還沒有訂用帳戶，則先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>使用 Azure 入口網站建立 premium 檔案共用

### <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com/)。

### <a name="create-a-filestorage-storage-account"></a>建立 filestorage 儲存體帳戶

現在您已經準備好建立儲存體帳戶。

每個儲存體帳戶都必須屬於 Azure 資源群組。 資源群組是用來群組 Azure 服務的邏輯容器。 當您建立儲存體帳戶時，可以選擇建立新的資源群組，或使用現有的資源群組。 本文說明如何建立新的資源群組。

1. 在 [Azure 入口網站中，選取左側功能表上的 [**儲存體帳戶**]。

    ![Azure 入口網站主頁面選取儲存體帳戶](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 在出現的 [儲存體帳戶]**** 視窗上，選擇 [新增]****。
1. 選取要在其中建立儲存體帳戶的訂用帳戶。
1. 在 [資源群組]**** 欄位下方，選取 [新建]****。 輸入新資源群組的名稱，如下圖所示。

1. 接下來，輸入儲存體帳戶的名稱。 您所選擇的名稱在整個 Azure 中必須是唯一的。 名稱的長度必須介於 3 到 24 個字元之間，且只能包含數字和小寫字母。
1. 選取儲存體帳戶的位置，或使用預設位置。
1. 針對 [**效能**]，請選取 [ **Premium**]。

    您必須在 [**帳戶種類**] 下拉式清單中選取 [ **Premium** for **FileStorage** ] 做為可用的選項。

1. 選取 [**帳戶種類**]，然後選擇 [ **FileStorage**]。
1. 將 **[複寫] 設為**其預設值 [**本機-多餘儲存體] (LRS) **。

    ![如何建立 premium 檔案共用的儲存體帳戶](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. 選取 [檢閱 + 建立]  ，以檢閱您的儲存體帳戶設定並建立帳戶。
1. 選取 [建立]。

建立儲存體帳戶資源之後，請流覽至它。

### <a name="create-a-premium-file-share"></a>建立進階檔案共用

1. 在儲存體帳戶的左側功能表中，依序移至 [檔案**服務**] 區段，**然後選取 [** 檔案]。
1. 選取 [檔案**共用**] 以建立 premium 檔案共用。
1. 輸入檔案共用的名稱和所需的配額，然後選取 [**建立**]。

> [!NOTE]
> 布建的共用大小是由共用配額所指定，檔案共用會依布建大小計費，如需詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/storage/files/)。

   ![建立進階檔案共用](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>清除資源

如果您想要清除在本文中建立的資源，只要刪除資源群組即可。 刪除資源群組也會刪除相關聯的儲存體帳戶，以及與資源群組相關聯的任何其他資源。

## <a name="create-a-premium-file-share-using-powershell"></a>使用 PowerShell 建立 premium 檔案共用

### <a name="create-an-account-using-powershell"></a>使用 PowerShell 建立帳戶

安裝最新版的 [PowerShellGet](/powershell/scripting/gallery/installing-psget) 模組。

然後，升級您的 PowerShell 模組、登入您的 Azure 訂用帳戶、建立資源群組，然後建立儲存體帳戶。

### <a name="upgrade-your-powershell-module"></a>升級您的 PowerShell 模組

若要使用 PowerShell 與 premium 檔案共用互動，您必須安裝 Az. Storage 模組版本1.4.0 或最新的 Az. Storage 模組。

從以提高的權限開啟 PowerShell 工作階段作為開始。

安裝 Az. Storage 模組：

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶

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

若要從 PowerShell 建立 FileStorage 儲存體帳戶，請使用[new-azstorageaccount](/powershell/module/az.storage/New-azStorageAccount)命令：

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>建立進階檔案共用

現在您已有 FileStorage 帳戶，您可以建立 premium 檔案共用。 使用[AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) Cmdlet 來建立一個。

> [!NOTE]
> 布建的共用大小是由共用配額所指定，檔案共用會依布建大小計費，如需詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/storage/files/)。

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>清除資源

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令： 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>使用 Azure CLI 建立 premium 檔案共用

若要啟動 Azure Cloud Shell，請登入 [Azure 入口網站](https://portal.azure.com)。

如果您想要登入本機安裝的 CLI，請先確定您有最新版本，然後執行 login 命令：

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

若要從 Azure CLI 建立 FileStorage 儲存體帳戶，請使用[az storage account create](/cli/azure/storage/account)命令。

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>取得儲存體帳戶金鑰

儲存體帳戶金鑰可控制儲存體帳戶中資源的存取權，在本文中，我們會使用金鑰來建立高階檔案共用。 當您建立儲存體帳戶時，系統會自動建立金鑰。 您可以使用 [az storage account keys list](/cli/azure/storage/account/keys) 命令取得儲存體帳戶的儲存體帳戶金鑰：

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>建立進階檔案共用

現在您已有 filestorage 帳戶，您可以建立 premium 檔案共用。 使用[az storage share create](/cli/azure/storage/share)命令來建立一個。

> [!NOTE]
> 布建的共用大小是由共用配額所指定，檔案共用會依布建大小計費，如需詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/storage/files/)。

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>清除資源

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用 [az group delete](/cli/azure/group) 命令。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本文中，您已建立 premium 檔案共用。 若要瞭解此帳戶所提供的效能，請繼續進行《規劃指南》中的「效能層」一節。

> [!div class="nextstepaction"]
> [檔案共用層](storage-files-planning.md#storage-tiers)
