---
title: 如何從 Azure DevTest Labs 刪除及匯出個人資料
description: 了解如何從 Azure DevLast Labs 服務刪除及匯出個人資料，以履行您在一般資料保護規定 (GDPR) 下的責任。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2c44b2f3aa6f2dfad18ed53804842a5dad8bd94a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483511"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>從 Azure DevTest Labs 匯出或刪除個人資料
本文提供從 Azure DevTest Labs 服務刪除及匯出個人資料的步驟。 

## <a name="what-personal-data-does-devtest-labs-collect"></a>DevTest Labs 會收集哪些個人資料？
DevTest Labs 會對使用者收集兩種主要的個人資料。 這兩種資料是使用者電子郵件地址和使用者物件識別碼。 這些都是服務為實驗室管理員和實驗室使用者提供服務功能所不可或缺的資訊。

### <a name="user-email-address"></a>使用者電子郵件地址
DevTest Labs 會依據使用者電子郵件地址，將自動關閉電子郵件通知傳送給實驗室使用者。 此電子郵件會通知使用者其機器即將關閉。 使用者可依需求自行選擇延後或略過關機作業。 您可以設定實驗室層級或 VM 層級的電子郵件地址。

**設定實驗室的電子郵件：**

![設定實驗室層級的電子郵件](./media/personal-data-delete-export/lab-user-email.png)

**設定 VM 的電子郵件：**

![設定 VM 層級的電子郵件](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>使用者物件識別碼
DevTest Labs 會透過使用者物件識別碼向實驗室管理員顯示逐月的成本趨勢，以及依資源列出的成本資訊。 它可讓管理員追蹤成本及管理其實驗室的閾值。 

**目前行事曆月份的預估成本趨勢：** 
 ![目前行事曆月份的預估成本趨勢](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**依資源的預估月迄今成本：** 
 ![依資源的預估月迄今成本](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>我們為何需要這項個人資料？
DevTest Labs 服務會基於運作目的使用個人資料。 這是服務為了提供重要功能所不可或缺的資料。 如果您設定了使用者電子郵件地址的保留原則，實驗室使用者在其電子郵件地址從我們的系統中刪除後，就無法適時收到自動關閉電子郵件通知。 同樣地，如果使用者物件識別碼依據保留原則而刪除，實驗室管理員也無法就其在實驗室中的機器檢視逐月成本趨勢和個別資源的成本。 因此，只要使用者的資源在實驗室中仍有效用，就必須保留這項資料。

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>如何讓系統消除我的個人資料？
如果您是實驗室使用者，當您想要刪除這項個人資料時，可以藉由刪除實驗室中的對應資源來達到此目的。 DevTest Labs 服務會在使用者刪除其個人資料的 30 天後，將已刪除的資料匿名處理。

例如，如果您刪除了 VM，或移除了電子郵件地址，DevTest Labs 服務將會在資源刪除的 30 天後將這項資料匿名處理。 刪除後保留 30 天的原則，是為了確保我們能為實驗室管理員提供精確的逐月成本預測。

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>如何要求匯出我的個人資料？
您可以使用 Azure 入口網站或 PowerShell 來匯出個人和實驗室使用量資料。 資料會匯出為兩個不同的 CSV 檔案：

- **disks.csv** -包含不同 vm 所使用之磁片的相關資訊
- **virtualmachines.csv** -包含實驗室中 vm 的相關資訊。

### <a name="azure-portal"></a>Azure 入口網站
身為實驗室使用者，您可以要求匯出 DevTest Labs 服務所儲存的個人資料。 若要要求匯出，請瀏覽至實驗室 [概觀]**** 頁面上的 [個人資料]**** 選項。 選取 [要求匯出]**** 按鈕，即可開始在您實驗室管理員的儲存體帳戶中建立可下載的 Excel 檔案。 接著，您可以連絡實驗室管理員以檢視這項資料。

1. 選取左側功能表上的 [個人資料]****。 

    ![個人資料頁面](./media/personal-data-delete-export/personal-data-page.png)
2. 選取包含實驗室的**資源群組**。

    ![選取資源群組](./media/personal-data-delete-export/select-resource-group.png)
3. 選取資源群組中的**儲存體帳戶**。
4. 在 [儲存體帳戶]**** 頁面上，選取 [Blob]****。

    ![選取 Blob 圖格](./media/personal-data-delete-export/select-blobs-tile.png)
5. 在容器清單中選取名為 **labresourceusage** 的容器。

    ![選取 Blob 容器](./media/personal-data-delete-export/select-blob-container.png)
6. 選取以您的實驗室命名的**資料夾**。 在此資料夾中，您會看到您實驗室中的**磁碟**和**虛擬機器**的 **csv** 檔案。 您可以下載這些 csv 檔案、篩選出要求存取的實驗室使用者所適用的內容，並與他們共用。

    ![下載 CSV 檔案](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

上述範例中的主要元件如下：

- AzureRmResourceAction 命令。
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- 兩個動作參數
    - **blobStorageAbsoluteSasUri** -具有共用存取簽章（SAS）權杖的儲存體帳戶 URI。 在 PowerShell 腳本中，可以傳入此值，而不是儲存體金鑰。
    - **usageStartDate** -提取資料的開始日期，而結束日期是執行動作的目前日期。 資料細微性是在 day 層級，因此即使您加入時間資訊，它也會被忽略。

### <a name="exported-data---a-closer-look"></a>匯出的資料-深入探討
現在讓我們仔細看一下匯出的資料。 如先前所述，一旦成功匯出資料之後，將會有兩個 CSV 檔案。 

**virtualmachines.csv**包含下列資料行：

| 資料行名稱 | 描述 |
| ----------- | ----------- | 
| SubscriptionId | 實驗室所在的訂用帳戶識別碼。 |
| LabUId | 實驗室的唯一 GUID 識別碼。 |
| LabName | 實驗室名稱。 |
| LabResourceId | 完整的實驗室資源識別碼。 |
| resourceGroupName | 包含 VM 的資源組名 | 
| ResourceId | VM 的完整資源識別碼。 |
| ResourceUId | VM 的 GUID |
| Name | 虛擬機器名稱。 |
| CreatedTime | 建立 VM 的日期時間。 |
| DeletedDate | 刪除 VM 的日期時間。 如果是空的，則刪除作業尚未發生。 |
| ResourceOwner | VM 的擁有者。 如果值是空的，則為可宣告 VM，或由服務主體所建立。 |
| PricingTier | VM 的定價層 |
| ResourceStatus | VM 的可用性狀態。 如果 VM 已被刪除，則為作用中（如果仍然存在或非作用中）。 |
| ComputeResourceId | 完整的虛擬機器計算資源識別碼。 |
| 可宣告 | 如果 VM 是可宣告 VM，則設定為 true | 
| EnvironmentId | 在其中建立虛擬機器的環境資源識別碼。 當 VM 不是在環境資源中建立時，它會是空的。 |
| ExpirationDate | VM 的到期日。 如果尚未設定到期日，它會設為空白。
| GalleryImageReferenceVersion |  VM 基底映射的版本。 |
| GalleryImageReferenceOffer | VM 基底映射的供應專案。 |
| GalleryImageReferencePublisher | VM 基底映射的發行者。 |
| GalleryImageReferenceSku | VM 基底映射的 Sku |
| GalleryImageReferenceOsType | VM 基底映射的 OS 類型 |
| CustomImageId | VM 基底自訂映射的完整識別碼。 |

**disks.csv**中所包含的資料行如下所示：

| 資料行名稱 | 描述 | 
| ----------- | ----------- | 
| SubscriptionId | 包含實驗室之訂用帳戶的識別碼 |
| LabUId | 實驗室的 GUID |
| LabName | 實驗室的名稱 | 
| LabResourceId | 實驗室的完整資源識別碼 | 
| resourceGroupName | 包含實驗室的資源組名 | 
| ResourceId | VM 的完整資源識別碼。 |
| ResourceUId | VM 的 GUID |
 |Name | 連接的磁片名稱 |
| CreatedTime |資料磁片的建立日期和時間。 |
| DeletedDate | 資料磁片被刪除的日期和時間。 |
| ResourceStatus | 資源的狀態。 [作用中] （如果資源存在的話）。 非使用中，刪除時。 |
| DiskBlobName | 資料磁片的 Blob 名稱。 |
| DiskSizeGB | 資料磁片的大小。 |
| DiskType | 資料磁片的類型。 0代表 Standard，1代表 Premium。 |
| LeasedByVmId | 已連接資料磁片之 VM 的資源識別碼。 |


> [!NOTE]
> 如果您要處理多個實驗室並想要取得整體資訊，這兩個索引鍵資料行是**LabUID**和**ResourceUId**，這是跨訂用帳戶的唯一識別碼。

匯出的資料可以使用工具（例如 SQL Server、Power BI 等）進行操作和視覺化。當您想要將實驗室的使用方式回報給您的管理小組，而該團隊可能不會使用與您相同的 Azure 訂用帳戶時，這項功能特別有用。

## <a name="next-steps"></a>後續步驟
查看下列文章： 

- [設定實驗室的原則](devtest-lab-set-lab-policy.md)
- [常見問題集](devtest-lab-faq.md)
