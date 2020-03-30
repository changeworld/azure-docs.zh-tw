---
title: 創建高級 Azure 檔共用
description: 在本文中，您將瞭解如何創建高級 Azure 檔共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7680a28b165dc252159cf95311439508d3c867e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529102"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>如何創建高級 Azure 檔共用
高級檔共用在固態磁片 （SSD） 存儲介質上提供，適用于 IO 密集型工作負載，包括託管資料庫和高性能計算 （HPC）。 高級檔共用託管在特殊用途存儲帳戶類型中，稱為檔存儲帳戶。 高級檔共用專為高性能和企業規模應用程式而設計，提供一致的低延遲、高 IOPS 和高輸送量共用。

本文介紹如何使用[Azure 門戶](https://portal.azure.com/)、Azure PowerShell 和 Azure CLI 創建新帳戶類型。

## <a name="prerequisites"></a>Prerequisites

要訪問 Azure 資源（包括高級 Azure 檔共用），需要 Azure 訂閱。 如果您還沒有訂用帳戶，則先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>使用 Azure 門戶創建高級檔共用

### <a name="sign-in-to-azure"></a>登入 Azure

登錄到 Azure[門戶](https://portal.azure.com/)。

### <a name="create-a-filestorage-storage-account"></a>創建檔存儲存儲帳戶

現在，您已準備好創建存儲帳戶。

每個儲存體帳戶都必須屬於 Azure 資源群組。 資源群組是用來群組 Azure 服務的邏輯容器。 當您建立儲存體帳戶時，可以選擇建立新的資源群組，或使用現有的資源群組。 本文演示如何創建新的資源組。

1. 在 Azure 門戶中，選擇左側功能表上的**存儲帳戶**。

    ![Azure 門戶主頁選擇存儲帳戶](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 在出現的 [儲存體帳戶]**** 視窗上，選擇 [新增]****。
1. 選取要在其中建立儲存體帳戶的訂用帳戶。
1. 在 [資源群組]**** 欄位下方，選取 [新建]****。 輸入新資源群組的名稱，如下圖所示。

1. 接下來，輸入儲存體帳戶的名稱。 您所選擇的名稱在整個 Azure 中必須是唯一的。 名稱的長度必須介於 3 到 24 個字元之間，且只能包含數字和小寫字母。
1. 選取儲存體帳戶的位置，或使用預設位置。
1. 對於**性能**選擇 **"高級**"。
1. 選擇**帳戶類型**並選擇**檔存儲**。
1. 將**複製**設置為其預設值**本地冗余存儲 （LRS）。**

    ![如何為高級檔共用創建存儲帳戶](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. 選取 [檢閱 + 建立]****，以檢閱您的儲存體帳戶設定並建立帳戶。
1. 選取 [建立]****。

創建存儲帳戶資源後，導航到該資源。

### <a name="create-a-premium-file-share"></a>建立進階檔案共用

1. 在存儲帳戶的左側功能表中，滾動到 **"檔服務**"部分，然後選擇"**檔**"。
1. 選擇**檔共用**以創建高級檔共用。
1. 輸入檔共用的名稱和所需配額，然後選擇 **"創建**"。

> [!NOTE]
> 預配的共用大小由股票配額指定，檔共用按預配大小計費，請參閱[定價頁](https://azure.microsoft.com/pricing/details/storage/files/)瞭解更多詳細資訊。

   ![建立進階檔案共用](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>清除資源

如果要清理本文中創建的資源，只需刪除資源組即可。 刪除資源組還會刪除關聯的存儲帳戶以及與資源組關聯的任何其他資源。

## <a name="create-a-premium-file-share-using-powershell"></a>使用 PowerShell 創建高級檔共用

### <a name="create-an-account-using-powershell"></a>使用 PowerShell 建立帳戶

安裝最新版的 [PowerShellGet](/powershell/scripting/gallery/installing-psget) 模組。

然後，升級 PowerShell 模組，登錄到 Azure 訂閱，創建資源組，然後創建存儲帳戶。

### <a name="upgrade-your-powershell-module"></a>升級您的 PowerShell 模組

要與來自 PowerShell 的高級檔共用進行交互，您需要安裝 Az.Storage 模組版本 1.4.0 或最新的 Az.Storage 模組。

從以提高的權限開啟 PowerShell 工作階段作為開始。

安裝 Az.存儲模組：

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

### <a name="create-a-filestorage-storage-account"></a>創建檔存儲存儲帳戶

要從 PowerShell 創建檔存儲帳戶，請使用["新 AzStorageAccount"](/powershell/module/az.storage/New-azStorageAccount)命令：

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>建立進階檔案共用

現在，您擁有了檔存儲帳戶，您可以創建高級檔共用。 使用["新共用共用](/powershell/module/az.storage/New-AzStorageShare)"Cmdlet 創建一個。

> [!NOTE]
> 預配的共用大小由股票配額指定，檔共用按預配大小計費，請參閱[定價頁](https://azure.microsoft.com/pricing/details/storage/files/)瞭解更多詳細資訊。

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

## <a name="create-a-premium-file-share-using-azure-cli"></a>使用 Azure CLI 創建高級檔共用

若要啟動 Azure Cloud Shell，請登入 [Azure 入口網站](https://portal.azure.com)。

如果要登錄到 CLI 的本地安裝，請確保具有最新版本，然後運行登錄命令：

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

### <a name="create-a-filestorage-storage-account"></a>創建檔存儲存儲帳戶

要從 Azure CLI 創建檔存儲帳戶，請使用[az 存儲帳戶創建](/cli/azure/storage/account)命令。

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>取得儲存體帳戶金鑰

存儲帳戶金鑰控制對存儲帳戶中資源的訪問，在本文中，我們使用金鑰來創建高級檔共用。 當您建立儲存體帳戶時，系統會自動建立金鑰。 您可以使用 [az storage account keys list](/cli/azure/storage/account/keys) 命令取得儲存體帳戶的儲存體帳戶金鑰：

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>建立進階檔案共用

現在，您擁有了檔存儲帳戶，您可以創建高級檔共用。 使用[az 存儲共用創建](/cli/azure/storage/share)命令創建一個。

> [!NOTE]
> 預配的共用大小由股票配額指定，檔共用按預配大小計費，請參閱[定價頁](https://azure.microsoft.com/pricing/details/storage/files/)瞭解更多詳細資訊。

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

在本文中，您創建了高級檔共用。 要瞭解此帳戶提供的性能，請繼續訪問規劃指南的性能層部分。

> [!div class="nextstepaction"]
> [檔共用層](storage-files-planning.md#storage-tiers)