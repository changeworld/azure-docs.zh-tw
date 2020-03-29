---
title: 如何從 Azure 開發人員測試實驗室刪除和匯出個人資料
description: 了解如何從 Azure DevLast Labs 服務刪除及匯出個人資料，以履行您在一般資料保護規定 (GDPR) 下的責任。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c87e2fb534480bbf9bbe625d67782e5a11eda18c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169691"
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

**當前日曆月的估計成本趨勢：**
![當前日曆月的估計成本趨勢](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**按資源估算的月至今成本：**
![按資源估算的月至今成本](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>我們為何需要這項個人資料？
DevTest Labs 服務會基於運作目的使用個人資料。 這是服務為了提供重要功能所不可或缺的資料。 如果您設定了使用者電子郵件地址的保留原則，實驗室使用者在其電子郵件地址從我們的系統中刪除後，就無法適時收到自動關閉電子郵件通知。 同樣地，如果使用者物件識別碼依據保留原則而刪除，實驗室管理員也無法就其在實驗室中的機器檢視逐月成本趨勢和個別資源的成本。 因此，只要使用者的資源在實驗室中仍有效用，就必須保留這項資料。

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>如何讓系統消除我的個人資料？
如果您是實驗室使用者，當您想要刪除這項個人資料時，可以藉由刪除實驗室中的對應資源來達到此目的。 DevTest Labs 服務會在使用者刪除其個人資料的 30 天後，將已刪除的資料匿名處理。

例如，如果您刪除了 VM，或移除了電子郵件地址，DevTest Labs 服務將會在資源刪除的 30 天後將這項資料匿名處理。 刪除後保留 30 天的原則，是為了確保我們能為實驗室管理員提供精確的逐月成本預測。

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>如何要求匯出我的個人資料？
您可以使用 Azure 門戶或 PowerShell 匯出個人和實驗室使用方式資料。 資料匯出為兩個不同的 CSV 檔：

- **disks.csv** - 包含有關不同 VM 正在使用的磁片的資訊
- **虛擬機器.csv** - 包含有關實驗室中 VM 的資訊。

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

上述示例中的關鍵元件包括：

- 調用 AzureRm 資源操作命令。
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- 兩個指令引數
    - **blob 存儲絕對Sasuri** - 具有共用訪問簽名 （SAS） 權杖的存儲帳戶 URI。 在 PowerShell 腳本中，可以傳入此值，而不是存儲金鑰。
    - **使用開始日期**- 提取資料的開始日期，結束日期為執行操作的當前日期。 細微性在日級，因此即使您添加時間資訊，也會忽略該資訊。

### <a name="exported-data---a-closer-look"></a>匯出的資料 - 仔細觀察
現在，讓我們仔細看看匯出的資料。 如前所述，一旦資料成功匯出，將有兩個 CSV 檔。 

**虛擬機器.csv**包含以下資料列：

| 資料行名稱 | 描述 |
| ----------- | ----------- | 
| SubscriptionId | 實驗室存在的訂閱識別碼。 |
| 拉布伊德 | 實驗室的唯一 GUID 識別碼。 |
| 實驗室名稱 | 實驗室名稱。 |
| 實驗室資源Id | 完全合格的實驗室資源識別碼。 |
| resourceGroupName | 包含 VM 的資源組的名稱 | 
| ResourceId | VM 完全限定的資源識別碼。 |
| 資源庫ID | VM 的 GUID |
| 名稱 | 虛擬機器名稱。 |
| CreatedTime | 創建 VM 的日期和時間。 |
| DeletedDate | 刪除 VM 的日期和時間。 如果為空，則尚未發生刪除。 |
| 資源擁有者 | VM 的擁有者。 如果該值為空，則該值要麼是可聲明 VM，要麼是由服務主體創建的。 |
| 定價層 | VM 的定價層 |
| 資源狀態 | VM 的可用性狀態。 如果 VM 已刪除，則處於活動狀態（如果仍然存在或處於非活動狀態）。 |
| 計算資源 Id | 完全合格的虛擬機器計算資源識別碼。 |
| 可索賠 | 如果 VM 是可聲明的 VM，則設置為 true | 
| 環境Id | 在其中創建虛擬機器的環境資源識別碼。 當 VM 不是作為環境資源的一部分創建時，它是空的。 |
| ExpirationDate | VM 的到期日期。 如果尚未設置到期日期，則將其設置為空。
| 庫圖像參考版本 |  VM 基本映射的版本。 |
| 畫廊圖片參考提供 | 提供 VM 基本映射。 |
| 畫廊圖片參考發行者 | VM 基本映射的發行者。 |
| 畫廊圖片參考庫 | VM 基本映射的 Sku |
| 庫圖像參考類型 | VM 基本映射的作業系統類型 |
| 自訂圖像 Id | VM 基本自訂映射的完全限定 ID。 |

**磁片.csv**中包含的資料列如下所示：

| 資料行名稱 | 描述 | 
| ----------- | ----------- | 
| SubscriptionId | 包含實驗室的訂閱 ID |
| 拉布伊德 | 實驗室 GUID |
| 實驗室名稱 | 實驗室名稱 | 
| 實驗室資源Id | 實驗室完全合格的資源識別碼 | 
| resourceGroupName | 包含實驗室的資源組的名稱 | 
| ResourceId | VM 完全限定的資源識別碼。 |
| 資源庫ID | VM 的 GUID |
 |名稱 | 連接磁片的名稱 |
| CreatedTime |創建資料磁片的日期和時間。 |
| DeletedDate | 刪除資料磁片的日期和時間。 |
| 資源狀態 | 資源的狀態。 如果資源存在，則處於活動狀態。 刪除時處於非活動狀態。 |
| 磁片Blob名稱 | 資料磁片的 Blob 名稱。 |
| 磁片大小GB | 資料磁片的大小。 |
| DiskType | 資料磁片的類型。 0 表示標準，1 表示高級。 |
| 租賃ByVmId | 已附加到資料磁片的 VM 的資源識別碼。 |


> [!NOTE]
> 如果您正在處理多個實驗室，並且想要獲取總體資訊，則兩個關鍵列是**LabUID**和**ResourceUID，** 它們是跨訂閱的唯一 ID。

可以使用 SQL Server、Power BI 等工具操作和視覺化匯出的資料。當您要向可能使用與 Azure 訂閱相同的 Azure 訂閱的管理團隊報告實驗室的使用方式時，此功能特別有用。

## <a name="next-steps"></a>後續步驟
查看下列文章： 

- [設定實驗室的原則](devtest-lab-get-started-with-lab-policies.md)
- [常見問題集](devtest-lab-faq.md)
