---
title: 教學課程`:`使用受控識別透過 SAS 認證存取 Azure 儲存體 - Azure AD
description: 本教學課程說明如何使用 Windows VM 系統指派的受控識別，以利用 SAS 認證 (而非儲存體帳戶來存取金鑰) 來存取 Azure 儲存體。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba8c88f040bbd527b0d9f219a81fa090f53c84ed
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590540"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>教學課程：使用 Windows VM 系統指派的受控識別，透過 SAS 認證來存取 Azure 儲存體

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教學課程說明如何將系統指派的身分識別用於 Windows 虛擬機器 (VM)，以取得儲存體共用存取簽章 (SAS) 認證。 具體而言，即[服務 SAS 認證](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures)。 

服務 SAS 可以針對時間限制和特定服務 (在此例為 Blob 服務) 授與有限的存取權限，以供使用儲存體帳戶內的物件，而不需公開帳戶存取金鑰。 在執行儲存體作業時 (例如使用儲存體 SDK)，您可以如往常般使用 SAS 認證。 此教學課程中，我們會示範使用 Azure 儲存體 PowerShell 上傳和下載 Blob。 您將了解如何：

> [!div class="checklist"]
> * 建立儲存體帳戶
> * 在資源管理員中將 VM 存取權限授與儲存體帳戶 SAS 
> * 使用 VM 身分識別取得存取權杖，並將其用於從資源管理員取出 SAS 

## <a name="prerequisites"></a>必要條件

- 了解受控識別。 如果您不熟悉適用於 Azure 資源的受控識別功能，請參閱此[概觀](overview.md)。 
- Azure 帳戶，[註冊免費帳戶](https://azure.microsoft.com/free/)。
- 若要執行所需的資源建立和角色管理步驟，需要在適當的範圍 (您的訂用帳戶或資源群組) 上具備「擁有者」權限。 如果您需要角色指派的協助，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取權](../../role-based-access-control/role-assignments-portal.md)。
- 您也需要已啟用系統指派受控識別的 Windows 虛擬機器。
  - 如果您需要為本教學課程建立虛擬機器，您可以遵循標題為[建立已啟用系統指派身分識別的虛擬機器](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)的文章

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>建立儲存體帳戶 

如果您還沒有帳戶，您現在將建立一個儲存體帳戶。 您也可以略過此步驟，並將存取現有儲存體帳戶 SAS 認證的權利，授予 VM 系統指派的受控識別。 

1. 按一下 Azure 入口網站左上角的 [+/建立新服務] 按鈕。
2. 按一下 [儲存體]，然後按一下 [儲存體帳戶]，就會顯示新的 [建立儲存體帳戶] 面板。
3. 輸入儲存體帳戶的名稱，您稍後將會使用它。  
4. [部署模型] 和 [帳戶類型] 應該分別設定為「資源管理員」和「一般用途」。 
5. 確定 [訂用帳戶]  和 [資源群組]  符合您在上一個步驟中建立 VM 時指定的值。
6. 按一下頁面底部的 [新增]  。

    ![建立新的儲存體帳戶](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>在儲存體帳戶中建立 Blob 容器

稍後我們將上傳和下載檔案到新的儲存體帳戶。 由於檔案需要 Blob 儲存體，我們需要建立 Blob 容器，用來儲存檔案。

1. 巡覽回到您新建立的儲存體帳戶。
2. 按一下左側面板 [Blob 服務] 下的 [容器] 連結。
3. 按一下頁面上方的 [+ 容器]，[新的容器] 面板隨即會滑出。
4. 指定容器的名稱，選取存取層級，然後按一下 [確定]  。 稍後在教學課程中將會用到您指定的名稱。 

    ![建立儲存體容器](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>將存取儲存體帳戶 SAS 的權利，授予 VM 系統指派的受控識別 

Azure 儲存體原生並不支援 Azure AD 驗證。  不過，您可以使用受控識別從 Resource Manager 擷取儲存體 SAS，然後使用該 SAS 存取儲存體。  在此步驟中，您會將存取儲存體帳戶 SAS 的權利，授予 VM 系統指派的受控識別。   

1. 巡覽回到您新建立的儲存體帳戶。   
2. 按一下左側面板中的 [存取控制 (IAM)]  連結。  
3. 按一下頁面頂端的 [+ 新增角色指派]，以新增虛擬機器的新角色指派。
4. 在頁面右側中，將 [角色] 設定為 [儲存體帳戶參與者]。  
5. 在下一個下拉式清單中，將 [存取權指派對象為] 設定為資源 [虛擬機器]。  
6. 接下來，請確保 [訂用帳戶] 下拉式清單中已列出適當的訂用帳戶，然後將 [資源群組] 設定為 [所有資源群組]。  
7. 最後，在 [選取] 的下拉式清單中，選擇您的 Windows 虛擬機器，然後按一下 [儲存]。 

    ![替代映像文字](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 的身分識別取得存取權杖，並使用它來呼叫 Azure Resource Manager 

其餘課程會從 VM 繼續進行。

在這部分的課程中，需要用到 Azure Resource Manager PowerShell Cmdlet。  如果您沒有安裝它，請[下載最新版本](/powershell/azure/)之後再繼續。

1. 在 Azure 入口網站中，瀏覽至 [虛擬機器]，移至您的 Windows 虛擬機器，然後在 [概觀] 頁面中，按一下頂端的 [連線]。
2. 輸入您建立 Windows VM 時新增的 **使用者名稱** 和 **密碼**。 
3. 現在您已經建立虛擬機器的 **遠端桌面連線**。
4. 在遠端工作階段中開啟 PowerShell 並使用 Invoke-WebRequest，向 Azure 資源端點的本機受控識別提出要求，以取得 Azure Resource Manager 的權杖。

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > 「資源」參數的值必須完全符合 Azure AD 的預期。 當使用 Azure Resource Manager 資源 ID 時，必須在 URI 中包含結尾的斜線。
    
    接下來，擷取「Content」元素，該元素會儲存為 $response 物件中的 JavaScript 物件標記法 (JSON) 格式字串。 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    再來，從回應中擷取存取權杖。
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>從 Azure Resource Manager 取得 SAS 認證以進行儲存體呼叫 

現在請使用上一節中取出的存取權杖，使用 PowerShell 對資源管理員進行呼叫，以建立儲存體 SAS 認證。 擁有 SAS 認證後，便可以呼叫儲存體作業。

針對此要求，我們將使用下列 HTTP 要求參數來建立 SAS 認證：

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

要求 SAS 認證的 POST 主體中包含了這些參數。 如需有關建立 SAS 認證參數的詳細資訊，請參閱[清單服務 SAS REST 參考](/rest/api/storagerp/storageaccounts/listservicesas)。

首先，將參數轉換為 JSON，然後呼叫儲存體 `listServiceSas` 端點以建立 SAS 認證：

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> URL 區分大小寫，因此請確定您使用的是稍早在命名資源群組時，您所使用的相同大小寫，而且 "resourceGroups" 中的 "G" 為大寫。 

現在我們可以從回應中擷取 SAS 認證：

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

如果檢查 SAS 認證，則會看到類似下列的內容：

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

接著，我們會建立一個名為 "test.txt" 的檔案。 然後使用 SAS 認證，以 `New-AzStorageContent` Cmdlet 進行驗證，並將檔案上傳至 Blob 容器，然後下載該檔案。

```bash
echo "This is a test text file." > test.txt
```

請務必先使用 `Install-Module Azure.Storage` 安裝 Azure 儲存體 Cmdlet。 然後使用 `Set-AzStorageBlobContent` PowerShell Cmdlet 上傳您剛才建立的 Blob：

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

回應：

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

您也可以使用 `Get-AzStorageBlobContent` PowerShell Cmdlet 來下載您剛剛上傳的 Blob：

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

回應：

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Windows VM 系統指派的受控識別，以利用 SAS 認證來存取 Azure 儲存體。  若要深入了解 Azure 儲存體 SAS，請參閱：

> [!div class="nextstepaction"]
>[使用共用存取簽章 (SAS)](../../storage/common/storage-sas-overview.md)