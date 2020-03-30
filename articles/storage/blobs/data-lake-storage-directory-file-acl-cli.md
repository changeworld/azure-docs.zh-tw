---
title: 在 Azure 資料湖存儲 Gen2（預覽）中，對檔& ACL 使用 Azure CLI
description: 使用 Azure CLI 管理具有階層命名空間的存儲帳戶中的目錄、檔和目錄存取控制清單 （ACL）。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486129"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>使用 Azure CLI 管理 Azure 資料湖存儲 Gen2 中的目錄、檔和 ACL（預覽版）

本文介紹如何使用[Azure 命令列介面 （CLI）](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)創建和管理具有階層命名空間的存儲帳戶中的目錄、檔和許可權。 

> [!IMPORTANT]
> 本文`storage-preview`仲介紹的擴展當前處於公共預覽版中。

[示例](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [第 1 代到第 2 代映射](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [提供回饋](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 （HNS） 的存儲帳戶。 按照[這些](data-lake-storage-quickstart-create-account.md)說明創建一個。
> * Azure CLI`2.0.67`版本或更高版本。

## <a name="install-the-storage-cli-extension"></a>安裝存儲 CLI 擴展

1. 打開[Azure 雲外殼](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或者如果您在本地[安裝了](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)Azure CLI，請打開命令主控台應用程式，如 Windows PowerShell。

2. 使用以下命令驗證已安裝的 Azure CLI`2.0.67`版本是或更高版本。

   ```azurecli
    az --version
   ```
   如果版本的 Azure CLI 低於`2.0.67`，則安裝更高版本。 請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

3. 安裝 `storage-preview` 延伸模組。

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>連接到帳戶

1. 如果您在本地使用 Azure CLI，則運行登錄命令。

   ```azurecli
   az login
   ```

   如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。

   否則，請在 打開 瀏覽器[https://aka.ms/devicelogin](https://aka.ms/devicelogin)頁面並輸入終端中顯示的授權代碼。 然後，在瀏覽器中使用您的帳戶憑據登錄。

   若要深入了解不同的驗證方法，請參閱使用 Azure CLI 登入。

2. 如果您的身份與多個訂閱相關聯，則將活動訂閱設置為將承載靜態網站的存儲帳戶的訂閱。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   將`<subscription-id>`預留位置值替換為訂閱的 ID。

## <a name="create-a-file-system"></a>建立檔案系統

檔案系統充當檔的容器。 可以使用 命令創建一個`az storage container create`。 

本示例創建名為 的`my-file-system`檔案系統。

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>建立目錄

使用`az storage blob directory create`命令創建目錄引用。 

本示例將名為 的`my-directory`目錄添加到名為 的`my-file-system`檔案系統中，該檔案系統位於名為`mystorageaccount`的帳戶中。

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>顯示目錄屬性

可以使用 命令將目錄的屬性列印到主控台`az storage blob show`。

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>重新命名目錄或移動目錄

使用`az storage blob directory move`命令重命名或移動目錄。

本示例將目錄從名稱`my-directory`重命名為 名稱。 `my-new-directory`

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>刪除目錄

使用`az storage blob directory delete`命令刪除目錄。

此示例刪除名為 的`my-directory`目錄。 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>檢查是否存在目錄

使用`az storage blob directory exist`命令確定檔案系統中是否存在特定目錄。

此示例顯示`my-directory``my-file-system`檔案系統中是否存在名為的目錄。 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>從目錄中下載

使用`az storage blob directory download`命令從目錄中下載檔案。

本示例從名為`upload.txt``my-directory`的目錄下載名為 的檔。 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

此示例下載整個目錄。

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>列出目錄內容

使用`az storage blob directory list`命令列出目錄的內容。

此示例列出位於名為`my-directory``my-file-system``mystorageaccount`的存儲帳戶的檔案系統中名為 的目錄的內容。 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>將檔上載到目錄

使用`az storage blob directory upload`命令將檔上載到目錄。

本示例將名為`upload.txt`的檔上載到名為 的`my-directory`目錄。 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

此示例上載整個目錄。

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>顯示檔案屬性

可以使用 命令將檔的屬性列印到主控台`az storage blob show`。

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>重新命名檔案或移動檔案

使用`az storage blob move`命令重命名或移動檔。

本示例將檔從名稱`my-file.txt`重命名為 名稱。 `my-file-renamed.txt`

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>刪除檔案

使用`az storage blob delete`命令刪除檔。

此示例刪除名為`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>管理權限

您可以獲取、設置和更新目錄和檔的存取權限。

> [!NOTE]
> 如果使用 Azure 活動目錄 （Azure AD） 來授權命令，請確保已分配了安全主體的[存儲 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 要瞭解有關如何應用 ACL 許可權及其更改效果的更多內容，請參閱[Azure 資料湖存儲 Gen2 中的訪問控制項](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

### <a name="get-directory-and-file-permissions"></a>獲取目錄和檔許可權

使用`az storage blob directory access show`命令獲取**目錄**的 ACL。

此示例獲取目錄的 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

使用`az storage blob access show`命令獲取**檔的**存取權限。 

此示例獲取檔的 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

下圖顯示了獲取目錄 ACL 後的輸出。

![獲取 ACL 輸出](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

在此示例中，擁有使用者具有讀取、寫入和執行許可權。 所屬組僅具有讀取和執行許可權。 有關存取控制清單的詳細資訊，請參閱[Azure 資料湖存儲 Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="set-directory-and-file-permissions"></a>設置目錄和檔許可權

使用`az storage blob directory access set`命令設置**目錄**的 ACL。 

本示例為擁有使用者、擁有組或其他使用者在目錄中設置 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

本示例為擁有的使用者、擁有組或其他使用者在目錄中設置*預設*ACL，然後將 ACL 列印到主控台。

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

使用`az storage blob access set`命令設置**檔的**acl。 

本示例為擁有的使用者、擁有組或其他使用者的檔設置 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
下圖顯示了設置檔的 ACL 後的輸出。

![獲取 ACL 輸出](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

在此示例中，擁有的使用者和擁有組僅具有讀取和寫入權限。 所有其他使用者都具有寫入和執行許可權。 有關存取控制清單的詳細資訊，請參閱[Azure 資料湖存儲 Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="update-directory-and-file-permissions"></a>更新目錄和檔許可權

設置此許可權的另一種方法是使用 或`az storage blob directory access update``az storage blob access update`命令。 

通過將參數設置為 ACL 的短形式來`-permissions`更新目錄或檔的 ACL。

此示例更新**目錄**的 ACL。

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

此示例更新**檔的**ACL。

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

還可以通過將`--owner`或`group`參數設置為使用者的實體 ID 或使用者主體名稱 （UPN） 來更新目錄或檔的擁有使用者和組。 

此示例更改目錄的擁有者。 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

此示例更改檔的擁有者。 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>管理使用者定義的中繼資料

通過將命令與一個或多個名稱值對一起使用，`az storage blob directory metadata update`可以將使用者定義的中繼資料添加到檔或目錄。

本示例為名為`my-directory`目錄的目錄添加使用者定義的中繼資料。

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

此示例顯示名為 的`my-directory`目錄的所有使用者定義的中繼資料。

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>另請參閱

* [樣品](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [第 1 代到第 2 代映射](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [提供意見反應](https://github.com/Azure/azure-cli-extensions/issues)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [原始程式碼](https://github.com/Azure/azure-cli-extensions/tree/master/src)

