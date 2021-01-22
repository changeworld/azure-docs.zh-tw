---
title: 開始使用 PowerShell
description: 您可以用來管理 Batch 資源的 Azure PowerShell Cmdlet 快速簡介。
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 2b51a2a7852df82625fb342bbbbc4a3a1cbf72a3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685505"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>使用 PowerShell Cmdlet 管理 Batch 資源

使用 Azure Batch PowerShell Cmdlet，您可以執行許多常見的 Batch 工作並編寫其腳本。 本文會快速介紹可用來管理 Batch 帳戶和使用批次資源 (例如集區、作業和和工作) 的 Cmdlet。

如需批次 Cmdlet 和詳細 Cmdlet 語法的完整清單，請參閱 [Azure 批次 Cmdlet 參考資料](/powershell/module/az.batch)。

建議您經常更新 Azure PowerShell 模組，以充分運用服務更新和增強功能。

## <a name="prerequisites"></a>Prerequisites

- [安裝及設定 Azure PowerShell 模組](/powershell/azure/)。 若要安裝特定的 Azure Batch 模組 (例如發行前版本模組)，請參閱 [PowerShell 資源庫](https://www.powershellgallery.com/packages/Az.Batch/)。

- 執行 **Connect-AzAccount** Cmdlet 以連線到訂用帳戶 (Azure Batch Cmdlet 隨附在 Azure Resource Manager 模組中)：

  ```powershell
  Connect-AzAccount
  ```

- **註冊 Batch 提供者命名空間**。 每個訂用帳戶只需要執行這項作業 **一次**。
  
  ```powershell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>管理 Batch 帳戶和金鑰

### <a name="create-a-batch-account"></a>建立批次帳戶：

**New-AzBatchAccount** 會在指定的資源群組中建立 Batch 帳戶。 如果您還沒有資源群組，請執行 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet 建立一個資源群組。 在 **Location** 參數中指定其中一個 Azure 區域，例如 "Central US"。 例如：

```powershell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

然後，在資源群組中建立 Batch 帳戶。 為 <*account_name*> 中的帳戶指定名稱，以及指定資源群組的位置和名稱。 建立 Batch 帳戶可能需要一些時間來完成。 例如：

```powershell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> Batch 帳戶名稱對資源群組的 Azure 區域必須是唯一的、包含 3 到 24 個字元，並只使用小寫字母和數字。

### <a name="get-account-access-keys"></a>取得帳戶存取金鑰

**Get-AzBatchAccountKeys** 會顯示與 Azure Batch 帳戶相關聯的存取金鑰。 例如，執行下列命令以取得您所建立帳戶的主要和次要金鑰。

 ```powershell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>產生新的存取金鑰

**New-AzBatchAccountKey** 會為 Azure Batch 帳戶產生新的主要或次要帳戶金鑰。 例如，若要為您的 Batch 帳戶產生新的主要金鑰，請輸入：

```powershell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> 若要產生新的次要金鑰，請將 **KeyType** 參數指定為「次要」。 您必須個別重新產生主要和次要金鑰。

### <a name="delete-a-batch-account"></a>刪除 Batch 帳戶

**Remove-AzBatchAccount** 會刪除 Batch 帳戶。 例如：

```powershell
Remove-AzBatchAccount -AccountName <account_name>
```

出現提示時，請確認您想要移除的帳戶。 帳戶移除可能需要一些時間來完成。

## <a name="create-a-batchaccountcontext-object"></a>建立 BatchAccountContext 物件

您可以使用共用金鑰驗證或 Azure Active Directory 驗證來驗證並管理 Batch 資源。 若要使用 Batch PowerShell Cmdlet 進行驗證，請先建立 BatchAccountContext 物件以儲存您的帳戶認證或身分識別。 您必須將 BatchAccountContext 物件傳遞給使用 **BatchContext** 參數的 Cmdlet。

### <a name="shared-key-authentication"></a>共用金鑰驗證

```powershell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> 依預設，帳戶的主要金鑰用於驗證，但是您可以透過變更 BatchAccountContext 物件的 **KeyInUse** 屬性，明確地選取要使用的金鑰：`$context.KeyInUse = "Secondary"`。

### <a name="azure-active-directory-authentication"></a>Azure Active Directory 驗證

```powershell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>建立和修改批次資源

請使用 **New-AzBatchPool**、**New-AzBatchJob** 和 **New-AzBatchTask** 之類的 Cmdlet，在 Batch 帳戶下建立資源。 要更新現有資源的屬性，有對應的 **Get-** 和 **Set-** Cmdlet ，要移除 Batch 帳戶下的資源，則有 **Remove-** Cmdlet。

使用許多這類 Cmdlet 時，除了傳遞 BatchContext 物件，您還需要建立或傳遞包含詳細資源設定的物件，如下列範例所示。 如需其他範例，請參閱每個 Cmdlet 的詳細說明。

### <a name="create-a-batch-pool"></a>建立 Batch 集區

當 [您建立或](nodes-and-pools.md#configurations)更新 Batch 集區時，您會指定一個設定。 集區通常應該使用虛擬機器設定進行設定，這可讓您指定 [Azure 虛擬機器 Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)中所列的其中一個支援的 Linux 或 Windows VM 映射，或提供您已備妥的自訂映射。 雲端服務設定集區僅提供 Windows 計算節點，不支援所有批次功能。

當您執行 **>new-azbatchpool** 時，請在 PSVirtualMachineConfiguration 或 PSCloudServiceConfiguration 物件中傳遞作業系統設定。 例如，下列程式碼片段會使用虛擬機器組態中的 Standard_A1 計算節點建立 Batch 集區，並以 Ubuntu Server 18.04-LTS 製作映像。 在此，**VirtualMachineConfiguration** 參數會將 $configuration 變數指定為 PSVirtualMachineConfiguration 物件。 **BatchContext** 參數會將先前定義的變數 $context 指定為 BatchAccountContext 物件。

```powershell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

新集區中計算節點的目標數目會由自動調整公式計算。 在此案例中，此公式僅僅是 **$TargetDedicated=4**，表示集區中的計算節點數最多為 4 個。

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>查詢集區、作業、工作及其他詳細資料

使用 **Get-AzBatchPool**、**Get-AzBatchJob** 和 **Get-AzBatchTask** 之類的 Cmdelt，查詢在 Batch 帳戶下建立的實體。

### <a name="query-for-data"></a>查詢資料

例如，使用 **Get-AzBatchPools** 尋找您的集區。 依預設，這將查詢您帳戶下的所有集區，並假設您已經將 BatchAccountContext 物件儲存在 *$context* 中：

```powershell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>使用 OData 篩選

您可以提供 **篩選** 參數給 OData 篩選，只尋找您感興趣的物件。 例如，您可以尋找識別碼以 “myPool” 開頭的所有集區：

```powershell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

雖然這個方法比在本機管線中使用 “Where-Object” 較不具有彈性， 不過查詢將直接傳送進 Batch 服務，讓所有篩選在伺服器端運作，進而省下網際網路頻寬。

### <a name="use-the-id-parameter"></a>使用識別碼參數

使用 **Id** 參數可做為 OData 篩選器的替代方式。 若要查詢識別碼為 "myPool" 的特定集區：

```powershell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

**Id** 參數僅支援完整識別碼的搜尋，不可使用萬用字元或 OData 樣式的篩選器。

### <a name="use-the-maxcount-parameter"></a>使用 MaxCount 參數

依預設，每個 Cmdlet 最多傳回 1000 個物件。 如果到達此限制，請調整您的篩選器以傳回較少的物件，或使用 **MaxCount** 參數明確地設定最大值。 例如：

```powershell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

若要移除上限，將 **MaxCount** 設定為 0 或更少。

### <a name="use-the-powershell-pipeline"></a>使用 PowerShell 管線

Batch Cmdlet 可使用 PowerShell 管線在 Cmdlet 之間傳送資料。 這和指定參數有相同的效果，但是讓處理多個實體更容易。

例如，尋找和顯示您帳戶下的所有作業：

```powershell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

重新啟動集區中的每個計算節點︰

```powershell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>應用程式封裝管理

[應用程式封裝](batch-application-packages.md) 可讓您以簡化的方式，將應用程式部署到集區中的計算節點。 利用 Batch PowerShell Cmdlet，您可以上傳和管理 Batch 帳戶中的應用程式套件，並將套件版本部署至計算節點。

> [!IMPORTANT]
> 您必須先將 Azure 儲存體帳戶連結到您的 Batch 帳戶，才能使用應用程式套件。

**建立** 應用程式：

```powershell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**新增** 應用程式封裝︰

```powershell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

設定應用程式的 **預設版本**︰

```powershell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**列出** 應用程式的套件

```powershell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

**刪除** 應用程式套件

```powershell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

**刪除** 應用程式

```powershell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> 您必須先刪除所有應用程式的應用程式套件版本，才能刪除應用程式。 如果您嘗試刪除目前具有應用程式套件的應用程式，您會收到「衝突」錯誤。

### <a name="deploy-an-application-package"></a>部署應用程式封裝

您可以在建立集區時，指定一或多個應用程式套件以供部署。 當您在建立集區時指定封裝時，它會在節點加入集區時部署到每個節點。 重新啟動或重新安裝映像節點時，也會部署套件。

在建立集區時指定 `-ApplicationPackageReference` 選項，以在節點加入集區時將應用程式套件部署到集區的節點。 首先，建立 **PSApplicationPackageReference** 物件，然後以您要部署至集區中各個計算節點的應用程式識別碼和套件版本進行設定︰

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

現在建立集區，並指定套件參考物件做為 `ApplicationPackageReferences` 選項的引數︰

```powershell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -VirtualMachineConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

您可以在[使用 Batch 應用程式套件將應用程式部署至計算節點](batch-application-packages.md)中找到應用程式套件的詳細資訊。

### <a name="update-a-pools-application-packages"></a>更新集區的應用程式封裝

若要更新指派給現有集區的應用程式，請先使用所需的屬性 (應用程式識別碼和套件版本) 建立 PSApplicationPackageReference 物件︰

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

接下來，從 Batch 取得集區、清除任何現有的套件、新增套件參考，然後使用新的集區設定來更新 Batch 服務：

```powershell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

您現在已更新 Batch 服務中集區的屬性。 若要將新的應用程式套件實際部署至集區中的計算節點，您必須重新啟動這些節點或重新安裝其映像。 您可以使用此命令重新啟動集區中的每個節點︰

```powershell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> 您可以將多個應用程式套件部署至集區中的計算節點。 如果您想要新增應用程式封裝，而不是取代目前部署的封裝，請省略 `$pool.ApplicationPackageReferences.Clear()` 上述那一行。

## <a name="next-steps"></a>下一步

- 如需詳細的 Cmdlet 語法和範例，請參閱 [Azure Batch Cmdlet 參考](/powershell/module/az.batch) 。
- 瞭解如何 [使用 Batch 應用程式套件將應用程式部署至計算節點](batch-application-packages.md)。
