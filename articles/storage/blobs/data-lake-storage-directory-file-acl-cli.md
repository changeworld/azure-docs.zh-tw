---
title: 在 Azure Data Lake Storage Gen2 中使用檔案 & Acl 的 Azure CLI
description: 使用 Azure CLI 來管理具有階層命名空間的儲存體帳戶中的目錄和檔案和目錄存取控制清單（ACL）。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 79a87f02c8730e0b2110e7475de721f11beda568
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120601"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Azure CLI 來管理中的目錄、檔案和 Acl Azure Data Lake Storage Gen2

本文說明如何使用[Azure 命令列介面（CLI）](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)來建立及管理具有階層命名空間的儲存體帳戶中的目錄、檔案和許可權。 

[Gen1 至 Gen2 對應](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)  | [提供意見](https://github.com/Azure/azure-cli-extensions/issues)反應

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間（HNS）的儲存體帳戶。 請遵循[這些](data-lake-storage-quickstart-create-account.md)指示來建立一個。
> * Azure CLI 版本 `2.5.1` 或更高版本。

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>請確定您已安裝正確的 Azure CLI 版本

1. 開啟[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或如果您已在本機[安裝](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)Azure CLI，請開啟命令主控台應用程式（例如 Windows PowerShell）。

2. 使用下列命令，確認已安裝的 Azure CLI 版本為 `2.5.1` 或更高。

   ```azurecli
    az --version
   ```
   如果您的 Azure CLI 版本低於 `2.5.1` ，請安裝較新的版本。 請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="connect-to-the-account"></a>連接到帳戶

1. 如果您在本機使用 Azure CLI，請執行登入命令。

   ```azurecli
   az login
   ```

   如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。

   否則，請在中開啟瀏覽器頁面 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ，並輸入顯示在終端機中的授權碼。 然後，在瀏覽器中使用您的帳號憑證登入。

   若要深入瞭解不同的驗證方法，請參閱[使用 Azure CLI 授權存取 blob 或佇列資料](../common/authorize-data-operations-cli.md)。

2. 如果您的身分識別與多個訂用帳戶相關聯，請將您的使用中訂用帳戶設定為將裝載靜態網站之儲存體帳戶的訂用帳戶。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   將 `<subscription-id>` 預留位置值取代為您的訂用帳戶識別碼。

> [!NOTE]
> 本文中所述的範例會顯示 Azure Active Directory （AD）授權。 若要深入瞭解授權方法，請參閱[使用 Azure CLI 授權存取 blob 或佇列資料](../common/authorize-data-operations-cli.md)。

## <a name="create-a-file-system"></a>建立檔案系統

檔案系統作為檔案的容器。 您可以使用命令來建立一個 `az storage fs create` 。 

這個範例會建立名為的檔案系統 `my-file-system` 。

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-system-properties"></a>顯示檔案系統屬性

您可以使用命令，將檔案系統的屬性列印到主控台 `az storage fs show` 。

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-file-system-contents"></a>列出檔案系統內容

使用命令來列出目錄的內容 `az storage fs file list` 。

這個範例會列出名為的檔案系統內容 `my-file-system` 。

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file-system"></a>刪除檔案系統

使用命令來刪除檔案系統 `az storage fs delete` 。

這個範例會刪除名為的檔案系統 `my-file-system` 。 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>建立目錄

使用命令建立目錄參考 `az storage fs directory create` 。 

這個範例會將名為的目錄新增至名為 `my-directory` 的檔案系統 `my-file-system` ，該檔案位於名為的帳戶中 `mystorageaccount` 。

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>顯示目錄屬性

您可以使用命令，將目錄的屬性列印到主控台 `az storage fs directory show` 。

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>重新命名目錄或移動目錄

使用命令重新命名或移動目錄 `az storage fs directory move` 。

這個範例會將名稱的目錄重新命名 `my-directory` 為 `my-new-directory` 相同檔案系統中的名稱。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

這個範例會將目錄移至名為的檔案系統 `my-second-file-system` 。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>刪除目錄

使用命令刪除目錄 `az storage fs directory delete` 。

這個範例會刪除名為的目錄 `my-directory` 。 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>檢查目錄是否存在

使用命令判斷檔案系統中是否有特定的目錄 `az storage fs directory exists` 。

這個範例會顯示名為的目錄是否 `my-directory` 存在於 `my-file-system` 檔案系統中。 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>從目錄下載

使用命令從目錄下載檔案 `az storage fs file download` 。

這個範例會從名為的目錄下載名為的檔案 `upload.txt` `my-directory` 。 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>列出目錄內容

使用命令來列出目錄的內容 `az storage fs file list` 。

這個範例會列出名為之目錄的內容 `my-directory` ，該目錄位於 `my-file-system` 名為之儲存體帳戶的檔案系統中 `mystorageaccount` 。 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳到目錄

使用命令將檔案上傳至目錄 `az storage fs directory upload` 。

這個範例會將名為的檔案上傳 `upload.txt` 至名為的目錄 `my-directory` 。 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>顯示檔案屬性

您可以使用命令，將檔案的屬性列印到主控台 `az storage fs file show` 。

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>重新命名檔案或移動檔案

使用命令重新命名或移動檔案 `az storage fs file move` 。

這個範例會將檔案從名稱重新命名 `my-file.txt` 為名稱 `my-file-renamed.txt` 。

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>刪除檔案

使用命令來刪除檔案 `az storage fs file delete` 。

這個範例會刪除名為的檔案`my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>管理權限

您可以取得、設定及更新目錄和檔案的存取權限。

> [!NOTE]
> 如果您使用 Azure Active Directory （Azure AD）來授權命令，請確定已將[儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)指派給您的安全性主體。 若要深入瞭解如何套用 ACL 許可權，以及變更它們的影響，請參閱[Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

### <a name="get-an-acl"></a>取得 ACL

使用命令取得**目錄**的 ACL `az storage fs access show` 。

這個範例會取得目錄的 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

使用命令取得**檔案的訪問**許可權 `az storage fs access show` 。 

這個範例會取得檔案的 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

下圖顯示取得目錄的 ACL 之後的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

在此範例中，擁有使用者具有 [讀取]、[寫入] 和 [執行] 許可權。 擁有群組只有「讀取」和「執行」許可權。 如需存取控制清單的詳細資訊，請參閱[Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="set-an-acl"></a>設定 ACL

使用 `az storage fs access set` 命令來設定**目錄**的 ACL。 

這個範例會在擁有使用者、擁有群組或其他使用者的目錄上設定 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

這個範例會在擁有使用者、擁有群組或其他使用者的目錄上設定*預設*ACL，然後將 acl 列印到主控台。

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

使用 `az storage fs access set` 命令來設定檔案**file**的 acl。 

這個範例會針對擁有使用者、擁有群組或其他使用者，在檔案上設定 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

下圖顯示設定檔案的 ACL 後的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

在此範例中，擁有使用者和擁有群組只有「讀取」和「寫入」許可權。 所有其他使用者都具有 [寫入] 和 [執行] 許可權。 如需存取控制清單的詳細資訊，請參閱[Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="update-an-acl"></a>更新 ACL

設定此許可權的另一種方式是使用 `az storage fs access set` 命令。 

藉由將 `-permissions` 參數設定為 acl 的簡短形式，更新目錄或檔案的 acl。

這個範例會更新**目錄**的 ACL。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

這個範例會更新**檔案的 ACL。**

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

您也可以將 `--owner` 或 `group` 參數設定為使用者的實體識別碼或使用者主體名稱（UPN），以更新目錄或檔案的擁有使用者和群組。 

這個範例會變更目錄的擁有者。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

這個範例會變更檔案的擁有者。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>請參閱

* [Gen1 至 Gen2 對應](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [提供意見反應](https://github.com/Azure/azure-cli-extensions/issues)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


