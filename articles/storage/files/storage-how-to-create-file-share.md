---
title: 建立 Azure 檔案共用
titleSuffix: Azure Files
description: 如何使用 Azure 門戶、PowerShell 或 Azure CLI 創建 Azure 檔共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596866"
---
# <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用
要創建 Azure 檔共用，您需要回答有關如何使用它三個問題：

- **Azure 檔共用的性能要求是什麼？**  
    Azure 檔提供標準檔共用，這些共用託管在基於硬碟（基於 HDD 的）硬體上，高級檔共用託管在基於固態磁片（基於 SSD）的硬體上。

- **您需要大小檔共用？**  
    標準檔共用可以跨越多達 100 TiB，但預設情況下未啟用此功能;但是，預設情況下，此功能不會啟用。如果需要大於 5 TiB 的檔共用，則需要為存儲帳戶啟用大型檔共用功能。 高級檔共用可以跨越多達 100 TiB 沒有任何特殊設置，但是高級檔共用是預配的，而不是像標準檔共用一樣付費。 這意味著預配檔共用比所需共用大得多，這將增加存儲總成本。

- **Azure 檔共用的冗余要求是什麼？**  
    標準檔共用提供本地冗余 （LRS）、區域冗余 （ZRS）、地理冗余 （GRS） 或地理區域冗余 （GZRS） 存儲，但大型檔共用功能僅支援本地冗余和區域冗余檔共用。 高級檔共用不支援任何形式的異地冗余。

    在大多數提供存儲帳戶的區域提供本地冗余，在較小的區域子集中提供區域冗余，可以使用高級檔共用。 要瞭解您所在地區當前是否提供高級檔共用，請參閱 Azure 按[區域可用的產品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)頁面。 有關支援 ZRS 的區域的資訊，請參閱[Azure 存儲冗余](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

有關這三個選項的詳細資訊，請參閱規劃[Azure 檔部署](storage-files-planning.md)。

## <a name="prerequisites"></a>Prerequisites
- 本文假定您已經創建了 Azure 訂閱。 如果您還沒有訂用帳戶，則先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。
- 如果要使用 Azure PowerShell，[請安裝最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 如果要使用 Azure CLI，[請安裝最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-storage-account"></a>建立儲存體帳戶
Azure 檔共用部署到*存儲帳戶*中，這些帳戶是表示共用存儲池的頂級物件。 此存儲池可用於部署多個檔共用。 

Azure 支援客戶可能具有的不同存儲方案的多種類型的存儲帳戶，但 Azure 檔的存儲帳戶有兩種主要類型。 您需要創建哪種存儲帳戶類型取決於是要創建標準檔共用還是高級檔共用： 

- **通用版本 2 （GPv2） 存儲帳戶**：GPv2 存儲帳戶允許您在基於標準/基於硬碟（基於 HDD）的硬體上部署 Azure 檔共用。 除了存儲 Azure 檔共用外，GPv2 存儲帳戶還可以存儲其他存儲資源，如 Blob 容器、佇列或表。 

- **檔存儲存儲帳戶**：檔存儲存儲帳戶允許您在基於高級/固態磁片（基於 SSD）的硬體上部署 Azure 檔共用。 檔存儲帳戶只能用於存儲 Azure 檔共用;因此，檔存儲帳戶只能用於存儲 Azure 檔共用。無法在 FileStorage 帳戶中部署其他存儲資源（blob 容器、佇列、表等）。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
要通過 Azure 門戶創建存儲帳戶，請選擇 **" 從儀表板創建資源**"。 在生成的 Azure 應用商店搜索視窗中，搜索**存儲帳戶**並選擇生成的搜尋結果。 這將導致存儲帳戶的概覽頁;選擇 **"創建**"以繼續存儲帳戶創建嚮導。

![存儲帳戶快速建立選項的螢幕截圖](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>基礎知識部分
創建存儲帳戶要完成的第一部分標記為 **"基礎知識**"。 它包含創建存儲帳戶所需的所有欄位。 要創建 GPv2 存儲帳戶，請確保**性能**選項按鈕設置為*標準*，並且 **"帳戶"類**下拉清單選擇存儲*V2（通用 v2）。*

!["性能單選"按鈕的螢幕截圖，其中選擇了"標準"螢幕，選擇了"已選擇存儲V2的帳戶類型"](media/storage-how-to-create-file-share/create-storage-account-1.png)

要創建檔存儲存儲帳戶，請確保**性能**選項按鈕設置為*高級*，並且 **"帳戶"類**下拉清單選擇到*檔存儲*。

![選擇"高級"和"選擇檔存儲的帳戶類型"性能選項按鈕的螢幕截圖](media/storage-how-to-create-file-share/create-storage-account-2.png)

其他基礎知識欄位獨立于存儲帳戶的選擇：
- **訂閱**：要部署到的存儲帳戶的訂閱。 
- **資源組**：要部署到的存儲帳戶的資源組。 您可以創建新資源組或使用現有資源組。 資源群組是用來群組 Azure 服務的邏輯容器。 當您建立儲存體帳戶時，可以選擇建立新的資源群組，或使用現有的資源群組。
- **存儲帳戶名稱**：要創建的存儲帳戶資源的名稱。 此名稱必須是全域唯一的，但除此之外，任何您想要的名稱都可以。 當您通過 SMB 裝載 Azure 檔共用時，存儲帳戶名稱將用作伺服器名稱。
- **位置**：要部署到的存儲帳戶的區域。 這可以是與資源組或任何其他可用區域關聯的區域。
- **複製**：雖然這被標記為複製，但此欄位實際上意味著**冗余**;這是所需的冗余級別：本地冗余 （LRS）、區域冗余 （ZRS）、地理冗余 （GRS） 和地理區域冗余。 此下拉清單還包含讀取存取地理冗余 （RA-GRS） 和讀取存取地理區域冗余 （RA-GZRS），它們不適用於 Azure 檔共用;在具有這些選擇的存儲帳戶中創建的任何檔共用實際上將分別為異地冗余或地理區域冗余。 根據您所在的區域或選定的存儲帳戶類型，可能不允許使用某些冗余選項。
- **訪問層**：此欄位不適用於 Azure 檔，因此您可以選擇其中一個選項按鈕。

#### <a name="the-networking-blade"></a>網路邊欄選項卡
網路部分允許您配置網路選項。 這些設置是創建存儲帳戶的可選設置，如果需要，可以稍後進行配置。 有關這些選項的詳細資訊，請參閱[Azure 檔網路注意事項](storage-files-networking-overview.md)。

#### <a name="the-advanced-blade"></a>高級刀片
高級部分包含 Azure 檔共用的幾個重要設置：

- **需要安全轉移**：此欄位指示存儲帳戶是否需要加密才能與存儲帳戶通信。 我們建議保持啟用狀態，但是，如果您需要 SMB 2.1 支援，則必須禁用此功能。 我們建議您禁用加密，以便將存儲帳戶訪問限制到具有服務終結點和/或專用終結點的虛擬網路。
- **大型檔共用**：此欄位啟用最多 100 TiB 的檔共用的存儲帳戶。 啟用此功能將限制存儲帳戶僅本地冗余和區域冗余存儲選項。 為大型檔共用啟用 GPv2 存儲帳戶後，無法禁用大型檔共用功能。 檔存儲存儲帳戶（高級檔共用的存儲帳戶）沒有此選項，因為所有高級檔共用可以擴展到 100 TiB。 

![應用於 Azure 檔的重要高級設置的螢幕截圖](media/storage-how-to-create-file-share/create-storage-account-3.png)

高級選項卡中提供的其他設置（Blob 虛刪除、Azure 資料湖存儲第 2 代的階層命名空間和 Blob 存儲的 NFSv3）不適用於 Azure 檔。

#### <a name="tags"></a>Tags
標記是名稱/值對，使您能夠通過將同一標記應用於多個資源和資源組來對資源進行分類並查看合併計費。 這些是可選的，可以在創建存儲帳戶後應用。

#### <a name="review--create"></a>檢閱 + 建立
創建存儲帳戶的最後一步是選擇 **"審閱 + 創建**"選項卡上的 **"創建**"按鈕。如果未填充存儲帳戶的所有必需欄位，則此按鈕將不可用。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
要使用 PowerShell 創建存儲帳戶，我們將使用`New-AzStorageAccount`Cmdlet。 這個 Cmdlet 有很多選項;僅顯示所需的選項。 要瞭解有關高級選項的詳細資訊，[`New-AzStorageAccount`請參閱 Cmdlet 文檔](/powershell/module/az.storage/new-azstorageaccount)。

為了簡化存儲帳戶和後續檔的創建，我們將在變數中存儲多個參數。 您可以將變數內容替換為所需的任何值，但請注意，存儲帳戶名稱必須全域唯一。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

要創建能夠存儲標準 Azure 檔共用的存儲帳戶，我們將使用以下命令。 該`-SkuName`參數與所需的冗余類型相關;如果需要異地冗余或地理區域冗余存儲帳戶，還必須刪除該`-EnableLargeFileShare`參數。

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

要創建能夠存儲高級 Azure 檔共用的存儲帳戶，我們將使用以下命令。 請注意，`-SkuName`參數已更改，以包括本地冗`Premium`余 （）`LRS`的兩個冗余級別和所需的冗余級別。 參數`-Kind`不是`FileStorage``StorageV2`因為必須在檔存儲存儲帳戶而不是 GPv2 存儲帳戶中創建高級檔共用。

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
要使用 Azure CLI 創建存儲帳戶，我們將使用 az 存儲帳戶創建命令。 此命令有許多選項;但是，此命令具有許多選項。僅顯示所需的選項。 要瞭解有關高級選項的詳細資訊，請參閱[`az storage account create`命令文檔](/cli/azure/storage/account)。

為了簡化存儲帳戶和後續檔的創建，我們將在變數中存儲多個參數。 您可以將變數內容替換為所需的任何值，但請注意，存儲帳戶名稱必須全域唯一。

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

要創建能夠存儲標準 Azure 檔共用的存儲帳戶，我們將使用以下命令。 該`--sku`參數與所需的冗余類型相關;如果需要異地冗余或地理區域冗余存儲帳戶，還必須刪除該`--enable-large-file-share`參數。

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

要創建能夠存儲高級 Azure 檔共用的存儲帳戶，我們將使用以下命令。 請注意，`--sku`參數已更改，以包括本地冗`Premium`余 （）`LRS`的兩個冗余級別和所需的冗余級別。 參數`--kind`不是`FileStorage``StorageV2`因為必須在檔存儲存儲帳戶而不是 GPv2 存儲帳戶中創建高級檔共用。

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>建立檔案共用
創建存儲帳戶後，剩下的就是創建檔共用。 無論您使用的是高級檔共用還是標準檔共用，此過程都大致相同。 主要區別是**配額**及其表示的內容。

對於標準檔共用，它是 Azure 檔共用的上限，最終使用者無法超越該邊界。 標準檔共用配額的主要目的是預算："我不希望此檔共用超過此點"。 如果未指定配額，則標準檔共用可以跨越多達 100 TiB（如果未為存儲帳戶設置大型檔共用屬性，則為 5 TiB）。

對於高級檔共用，配額重載為平均**預配大小**。 預配大小是您要計費的金額，而不考慮實際使用方式。 預配高級檔共用時，需要考慮兩個因素：1） 從空間利用率的角度來看，共用的未來增長和 2） 工作負載所需的 IOPS。 每個預配的 GiB 都允許您進行額外的預留和突發 IOPS。 有關如何規劃高級檔共用的詳細資訊，請參閱[預配高級檔共用](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
如果剛剛創建了存儲帳戶，則可以通過選擇 **"轉到資源"** 從部署螢幕導航到它。 如果以前已創建存儲帳戶，則可以通過包含該帳戶的資源組導航到該帳戶。 進入存儲帳戶後，選擇標記為 **"檔共用"的**磁貼（您還可以通過存儲帳戶的目錄導航到**檔共用**）。

![檔共用磁貼的螢幕截圖](media/storage-how-to-create-file-share/create-file-share-1.png)

在檔共用清單中，您應該看到以前在此存儲帳戶中創建的任何檔共用;因此，應看到以前在此存儲帳戶中創建的任何檔共用。尚未創建檔共用的空表。 選擇 **= 檔共用**以創建新的檔共用。

新的檔共用邊欄選項卡應顯示在螢幕上。 完成新檔共用邊欄選項卡中的欄位以創建檔共用：

- **名稱**：要創建的檔共用的名稱。
- **配額**：標準檔共用的檔共用配額;高級檔共用的檔共用的預配大小。

選擇 **"創建**"以完成創建新共用。 請注意，如果存儲帳戶位於虛擬網路中，則除非用戶端也在虛擬網路中，否則將無法成功創建 Azure 檔共用。 您還可以使用 Azure PowerShell `New-AzRmStorageShare` Cmdlet 解決此時間點限制。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
您可以使用[`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare)Cmdlet 創建 Azure 檔共用。 以下 PowerShell 命令假定您已設置變數`$resourceGroupName`，並且`$storageAccountName`如上述使用 Azure PowerShell 部分創建存儲帳戶中定義的變數。 

> [!Important]  
> 對於高級檔共用，參數`-QuotaGiB`引用檔共用的預配大小。 檔共用的預配大小是您要計費的金額，無論使用方式如何。 標準檔共用根據使用方式而不是預配大小計費。

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> 您的檔案共用名稱必須是全部小寫。 有關命名檔共用和檔的完整詳細資訊，請參閱 [命名和引用共用、目錄、檔和中繼資料](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
必須先獲取存儲帳戶金鑰以授權使用 Azure CLI 創建檔共用。 這可以通過命令[`az storage account keys list`](/cli/azure/storage/account/keys)完成：

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

獲得存儲帳戶金鑰後，可以使用[`az storage share create`](/cli/azure/storage/share)命令創建 Azure 檔共用。 

> [!Important]  
> 對於高級檔共用，參數`--quota`引用檔共用的預配大小。 檔共用的預配大小是您要計費的金額，無論使用方式如何。 標準檔共用根據使用方式而不是預配大小計費。

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

如果存儲帳戶包含在虛擬網路中，並且您從中調用此命令的電腦不是虛擬網路的一部分，則此命令將失敗。 您可以通過使用上述 Azure PowerShell `New-AzRmStorageShare` Cmdlet 或從屬於虛擬網路的電腦（包括通過 VPN 連接）執行 Azure CLI 來解決此時間點限制。

---

> [!Note]  
> 您的檔案共用名稱必須是全部小寫。 有關命名檔共用和檔的完整詳細資訊，請參閱 [命名和引用共用、目錄、檔和中繼資料](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

## <a name="next-steps"></a>後續步驟
- [計畫部署 Azure 檔](storage-files-planning.md)或[計畫部署 Azure 檔同步](storage-sync-files-planning.md)。 
- [網路概述](storage-files-networking-overview.md)。
- 在[Windows、macOS](storage-how-to-use-files-windows.md)和[macOS](storage-how-to-use-files-mac.md)[Linux](storage-how-to-use-files-linux.md)上連接並裝載檔共用。