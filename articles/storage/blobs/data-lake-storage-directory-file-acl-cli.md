---
title: 在 Azure Data Lake Storage Gen2 中使用檔案 & Acl 的 Azure CLI （預覽）
description: 使用 Azure CLI 來管理具有階層命名空間的儲存體帳戶中的目錄和檔案和目錄存取控制清單（ACL）。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77486129"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>使用 Azure CLI 管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl （預覽）

本文說明如何使用[Azure 命令列介面（CLI）](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)來建立及管理具有階層命名空間的儲存體帳戶中的目錄、檔案和許可權。 

> [!IMPORTANT]
> 本文`storage-preview`中所述的擴充功能目前為公開預覽狀態。

[Sample](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support)  |   |  [Gen2 對應的範例 Gen1](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)[提供意見](https://github.com/Azure/azure-cli-extensions/issues)反應
## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間（HNS）的儲存體帳戶。 請遵循[這些](data-lake-storage-quickstart-create-account.md)指示來建立一個。
> * Azure CLI 版本`2.0.67`或更高版本。

## <a name="install-the-storage-cli-extension"></a>安裝 storage CLI 擴充功能

1. 開啟[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或如果您已在本機[安裝](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)Azure CLI，請開啟命令主控台應用程式（例如 Windows PowerShell）。

2. 使用下列命令，確認已安裝的 Azure CLI 版本`2.0.67`為或更高。

   ```azurecli
    az --version
   ```
   如果您的 Azure CLI 版本低於`2.0.67`，請安裝較新的版本。 請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

3. 安裝 `storage-preview` 延伸模組。

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>連接到帳戶

1. 如果您在本機使用 Azure CLI，請執行登入命令。

   ```azurecli
   az login
   ```

   如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。

   否則，請在[https://aka.ms/devicelogin](https://aka.ms/devicelogin)中開啟瀏覽器頁面，並輸入顯示在終端機中的授權碼。 然後，在瀏覽器中使用您的帳號憑證登入。

   若要深入了解不同的驗證方法，請參閱使用 Azure CLI 登入。

2. 如果您的身分識別與多個訂用帳戶相關聯，請將您的使用中訂用帳戶設定為將裝載靜態網站之儲存體帳戶的訂用帳戶。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   將`<subscription-id>`預留位置值取代為您的訂用帳戶識別碼。

## <a name="create-a-file-system"></a>建立檔案系統

檔案系統作為檔案的容器。 您可以使用`az storage container create`命令來建立一個。 

這個範例會建立名為`my-file-system`的檔案系統。

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>建立目錄

使用`az storage blob directory create`命令建立目錄參考。 

這個範例會將名`my-directory`為`my-file-system`的目錄新增至名為的檔案系統，該檔案`mystorageaccount`位於名為的帳戶中。

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>顯示目錄屬性

您可以使用`az storage blob show`命令，將目錄的屬性列印到主控台。

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>重新命名目錄或移動目錄

使用`az storage blob directory move`命令重新命名或移動目錄。

這個範例會將名稱`my-directory`的目錄重新命名為名稱`my-new-directory`。

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>刪除目錄

使用`az storage blob directory delete`命令刪除目錄。

這個範例會刪除名為`my-directory`的目錄。 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>檢查目錄是否存在

使用`az storage blob directory exist`命令判斷檔案系統中是否有特定的目錄。

這個範例會顯示名為`my-directory`的目錄是否存在`my-file-system`于檔案系統中。 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>從目錄下載

使用`az storage blob directory download`命令從目錄下載檔案。

這個範例會從名`upload.txt` `my-directory`為的目錄下載名為的檔案。 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

這個範例會下載整個目錄。

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>列出目錄內容

使用`az storage blob directory list`命令來列出目錄的內容。

這個範例會列出名`my-directory`為之目錄的內容，該目錄位於`my-file-system`名為`mystorageaccount`之儲存體帳戶的檔案系統中。 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳到目錄

使用`az storage blob directory upload`命令將檔案上傳至目錄。

這個範例會將名`upload.txt`為的檔案上傳`my-directory`至名為的目錄。 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

這個範例會上傳整個目錄。

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>顯示檔案屬性

您可以使用`az storage blob show`命令，將檔案的屬性列印到主控台。

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>重新命名檔案或移動檔案

使用`az storage blob move`命令重新命名或移動檔案。

這個範例會將檔案從名稱`my-file.txt`重新命名為名稱`my-file-renamed.txt`。

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>刪除檔案

使用`az storage blob delete`命令來刪除檔案。

這個範例會刪除名為的檔案`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>管理權限

您可以取得、設定及更新目錄和檔案的存取權限。

> [!NOTE]
> 如果您使用 Azure Active Directory （Azure AD）來授權命令，請確定已將[儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)指派給您的安全性主體。 若要深入瞭解如何套用 ACL 許可權，以及變更它們的影響，請參閱[Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

### <a name="get-directory-and-file-permissions"></a>取得目錄和檔案許可權

使用`az storage blob directory access show`命令取得**目錄**的 ACL。

這個範例會取得目錄的 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

使用`az storage blob access show`命令取得**檔案的訪問**許可權。 

這個範例會取得檔案的 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

下圖顯示取得目錄的 ACL 之後的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

在此範例中，擁有使用者具有 [讀取]、[寫入] 和 [執行] 許可權。 擁有群組只有「讀取」和「執行」許可權。 如需存取控制清單的詳細資訊，請參閱[Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="set-directory-and-file-permissions"></a>設定目錄和檔案許可權

使用`az storage blob directory access set`命令來設定**目錄**的 ACL。 

這個範例會在擁有使用者、擁有群組或其他使用者的目錄上設定 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

這個範例會在擁有使用者、擁有群組或其他使用者的目錄上設定*預設*ACL，然後將 acl 列印到主控台。

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

使用`az storage blob access set`命令來設定**檔案的 acl。** 

這個範例會針對擁有使用者、擁有群組或其他使用者，在檔案上設定 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
下圖顯示設定檔案的 ACL 後的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

在此範例中，擁有使用者和擁有群組只有「讀取」和「寫入」許可權。 所有其他使用者都具有 [寫入] 和 [執行] 許可權。 如需存取控制清單的詳細資訊，請參閱[Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="update-directory-and-file-permissions"></a>更新目錄和檔案許可權

設定此許可權的`az storage blob directory access update`另一種方式是使用或`az storage blob access update`命令。 

藉由將`-permissions`參數設定為 acl 的簡短形式，更新目錄或檔案的 acl。

這個範例會更新**目錄**的 ACL。

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

這個範例會更新**檔案的 ACL。**

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

您也可以將`--owner`或`group`參數設定為使用者的實體識別碼或使用者主體名稱（UPN），以更新目錄或檔案的擁有使用者和群組。 

這個範例會變更目錄的擁有者。 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

這個範例會變更檔案的擁有者。 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>管理使用者定義的中繼資料

您可以使用`az storage blob directory metadata update`命令搭配一或多個名稱/值組，將使用者定義的中繼資料新增至檔案或目錄。

這個範例會針對名為`my-directory` directory 的目錄新增使用者定義的中繼資料。

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

這個範例會顯示名為`my-directory`之目錄的所有使用者定義中繼資料。

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>請參閱

* [範例](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen1 至 Gen2 對應](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [提供意見反應](https://github.com/Azure/azure-cli-extensions/issues)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [原始程式碼](https://github.com/Azure/azure-cli-extensions/tree/master/src)

