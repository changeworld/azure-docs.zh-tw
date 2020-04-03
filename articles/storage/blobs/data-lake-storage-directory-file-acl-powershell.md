---
title: Azure 資料儲存第 2 代 PowerShell 檔案& ACL(預覽)
description: 使用 PowerShell cmdlet 管理已啟用分層命名空間 (HNS) 的儲存帳戶中的目錄、檔案和目錄存取控制列表 (ACL)。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 9be3b2c9b2624d4cd758081703373a433861e4a7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585320"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>使用 PowerShell 管理 Azure 資料系統儲存 Gen2 中的目錄、檔案和 ACL(預覽版)

本文介紹如何使用 PowerShell 在已啟用分層命名空間 (HNS) 的儲存帳戶中創建和管理目錄、檔和許可權。 

> [!IMPORTANT]
> 本文中介紹的 PowerShell 模組當前處於公共預覽版中。

[第一代到第 2 代映射](#gen1-gen2-map) | [提供回饋](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用分層命名空間 (HNS) 的儲存帳戶。 按照[這些](data-lake-storage-quickstart-create-account.md)說明創建一個。
> * .NET 框架已安裝 4.7.2 或更高。 請參考[下載 .NET 框架](https://dotnet.microsoft.com/download/dotnet-framework)。
> * PowerShell`5.1`版本或更高版本。

## <a name="install-powershell-modules"></a>安裝 PowerShell 模組

1. 使用以下命令驗證已安裝的 PowerShell`5.1`版本是或更高版本。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   要升級版本的 PowerShell,請參閱[升級現有 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. 安裝最新的**PowershellGet**模組。 然後,關閉並重新打開 PowerShell 主控台。

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force 
   ```

3. 安裝**Az.存儲**預覽模組。

   ```powershell
   Install-Module az.storage -RequiredVersion 1.13.3-preview -Repository PSGallery -AllowClobber -AllowPrerelease -Force 
   ```

   有關如何安裝 PowerShell 模組的詳細資訊,請參閱安裝[Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>連線到帳戶

打開 Windows PowerShell 命令視窗`Connect-AzAccount`,然後使用 命令登錄到 Azure 訂閱,然後按照螢幕上的說明操作。

```powershell
Connect-AzAccount
```

如果標識與多個訂閱關聯,則將活動訂閱設置為要創建和管理目錄中的存儲帳戶的訂閱。 在此範例中,將`<subscription-id>`占位符值替換為訂閱的 ID。

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

接下來,選擇您希望命令如何獲取存儲帳戶的授權。 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>選項 1: 使用 Azure 的目錄 (AD) 取得授權

使用此方法,系統可確保使用者帳戶具有適當的基於角色的訪問控制 (RBAC) 分配和 ACL 許可權。 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>選項 2:使用儲存帳戶金鑰取得授權

使用此方法,系統不檢查 RBAC 或 ACL 許可權。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>建立檔案系統

檔案系統充當檔的容器。 您可以使用`New-AzDatalakeGen2FileSystem`cmdlet 建立一個。 

本示例創建名為的`my-file-system`檔案系統。

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>建立目錄

使用`New-AzDataLakeGen2Item`cmdlet 建立目錄引用。 

本示例向檔案系統添加名為`my-directory`的目錄。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

此示例添加相同的目錄,但也設置許可權、umask、屬性值和元數據值。 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>顯示目錄屬性

本示例使用`Get-AzDataLakeGen2Item`cmdlet 獲取目錄,然後將屬性值列印到主控台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Metadata
$dir.Properties
```

## <a name="rename-or-move-a-directory"></a>重新命名目錄或移動目錄

使用`Move-AzDataLakeGen2Item`cmdlet 重新命名或移動目錄。

本示例將目錄從名稱`my-directory`重新命名為名稱`my-new-directory`。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> 如果要在沒有`-Force`提示的情況下覆蓋,請使用 參數。

本示例將名為`my-directory`的目錄移動到名為`my-directory-2``my-subdirectory`的子目錄。 此示例還會將 umask 應用於子目錄。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>刪除目錄

使用`Remove-AzDataLakeGen2Item`cmdlet 刪除目錄。

此範例刪除名為的`my-directory`目錄。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

您可以使用`-Force`參數 刪除檔而不提示。

## <a name="download-from-a-directory"></a>從目錄下載

使用`Get-AzDataLakeGen2ItemContent`cmdlet 從目錄中下載檔案。

本示例從名為`upload.txt``my-directory`的目錄下載名為的檔。 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>列出目錄內容

使用`Get-AzDataLakeGen2ChildItem`cmdlet 列出目錄的內容。 可以使用可選參數`-OutputUserPrincipalName`獲取使用者的名稱(而不是對象ID)。

此示例列出名為`my-directory`的目錄的內容。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

此範例不返回`ACL`、`Permissions``Group``Owner`屬性的值。 要取得這些值,請使用`-FetchProperty`參數。 

下面的範例列出了同一目錄的內容,但它也使用`-FetchProperty`參數`ACL`返回`Permissions``Group``Owner`、 和 屬性的值。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

要列出檔案系統的內容,`-Path`從命令中省略參數。

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳到目錄

使用`New-AzDataLakeGen2Item`cmdlet 將檔案上傳到目錄。

本示例將名為`upload.txt`的檔上傳到名為的`my-directory`目錄。 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

本示例上載同一檔,但隨後設置目標文件的許可權、umask、屬性值和元數據值。 此示例還會將這些值列印到主控台。

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>顯示檔案屬性

本示例使用`Get-AzDataLakeGen2Item`cmdlet 獲取檔,然後將屬性值列印到主控台。

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Metadata
$file.Properties
```

## <a name="delete-a-file"></a>刪除檔案

使用`Remove-AzDataLakeGen2Item`cmdlet 刪除檔案。

這個範例移除名為的檔案`upload.txt`。 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

您可以使用`-Force`參數 刪除檔而不提示。

## <a name="manage-access-permissions"></a>管理存取權限

您可以獲取、設置和更新檔案系統、目錄和檔案的訪問許可權。

> [!NOTE]
> 如果使用 Azure 的活動目錄 (Azure AD) 來授權命令,請確保已配置了安全主體的[儲存 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 要瞭解有關如何應用 ACL 權限及其變更效果的更多內容,請參閱[Azure 資料湖儲存 Gen2 中的存取控制件](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

### <a name="get-permissions"></a>取得權限

使用`Get-AzDataLakeGen2Item`cmdlet 取得目錄或檔的 ACL。


此範例取得**檔案系統**的 ACL,然後將 ACL 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

此示例獲取**目錄**的 ACL,然後將 ACL 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

此示例獲取**檔的**ACL,然後將 ACL 列印到主控台。

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下圖顯示了獲取目錄 ACL 后的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

在此示例中,擁有使用者具有讀取、寫入和執行許可權。 所屬組僅具有讀取和執行許可權。 有關存取控制清單的詳細資訊,請參考[Azure 資料的資料管理 Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="set-or-update-permissions"></a>設定或更新權限

使用`set-AzDataLakeGen2ItemAclObject`cmdlet 為擁有的使用者、擁有組或其他使用者創建 ACL。 然後,`Update-AzDataLakeGen2Item`使用 cmdlet 提交 ACL。

本示例為擁有的使用者、擁有組或其他使用者在**檔案系統**上設置 ACL,然後將 ACL 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$filesystem.ACL
```

本示例為擁有使用者、擁有組或其他使用者在**目錄中**設置 ACL,然後將 ACL 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
本示例為擁有的使用者、擁有組或其他使用者**的檔**設置 ACL,然後將 ACL 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下圖顯示了設定檔的 ACL 后的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

在此示例中,擁有的使用者和擁有組僅具有讀取和寫入許可權。 所有其他使用者都具有寫入和執行許可權。 有關存取控制清單的詳細資訊,請參考[Azure 資料的資料管理 Gen2 中的存取控制](data-lake-storage-access-control.md)。


### <a name="set-permissions-on-all-items-in-a-file-system"></a>設定檔案系統中所有項目的權限

您可以將`Get-AzDataLakeGen2Item``-Recurse`與`Update-AzDataLakeGen2Item`參數與 cmdlet 一起使用,以遞迴來設定檔案系統中所有目錄和檔案的 ACL。 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse -FetchProperty | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>第一代到第 2 代映射

下表顯示了用於資料儲存庫的 cmdlet 第 1 代如何映射到數據湖儲存 Gen2 的 cmdlet。

|資料儲存湖儲存第 1 代 cmdlet| 資料儲存湖儲存第二代 cmdlet| 注意 |
|--------|---------|-----|
|取得阿茲達特湖儲存子專案|獲取-阿茲達萊克根2子專案|預設情況下,獲取-AzDataLakeGen2ChildItem cmdlet 僅列出第一級子項。 -Recurse 參數遞歸地列出子項。 |
|取得-阿茲達數據湖儲存專案<br>獲取-阿茲達湖存儲專案Aclentry<br>取得阿茲達資料湖儲存專案擁有者<br>取得阿茲達資料湖儲存項目權限|獲取-阿茲達萊克根2專案|Get-AzDataLakeGen2Item cmdlet 的輸出項具有以下屬性:Acl、擁有者、組、許可權。|
|取得阿茲達資料湖儲存項目內容|獲取-阿茲達萊克根2文件內容|獲取-AzDataLakeGen2文件內容 cmdlet 將檔案內容下載到本地檔案。|
|移動-阿茲達湖存儲專案|移動-阿茲達萊克根2專案||
|新-阿茲達湖存儲專案|新-阿茲達萊克根2專案|此 cmdlet 從本地檔案上傳新文件內容。|
|刪除-阿茲達資料湖儲存專案|刪除-阿茲達萊克根2專案||
|設定-阿茲達湖存儲專案擁有者<br>設定-阿茲達湖存儲項目許可權<br>設定-阿茲達湖存儲專案Acl|更新-阿茲達萊克根2專案|更新-AzDataLakeGen2Item cmdlet 僅更新單個專案,而不是遞迴更新。 如果要以遞迴方式更新,請使用 Get-AzDataLakeStoreChildItem cmdlet 列出專案,然後管道到更新-AzDataLakeGen2Item cmdlet。|
|測試-阿茲達湖存儲專案|獲取-阿茲達萊克根2專案|如果專案不存在,Get-AzDataLakeGen2Item cmdlet 將報告錯誤。|



## <a name="see-also"></a>另請參閱

* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [將 Azure PowerShell 與 Azure 儲存一起使用](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
* [儲存電源外殼 cmdlet](/powershell/module/az.storage)。

