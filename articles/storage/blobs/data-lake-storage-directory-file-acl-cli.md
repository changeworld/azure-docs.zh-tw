---
title: 在 Azure Data Lake Storage Gen2 中為檔案和 ACL 使用 Azure CLI
description: 使用 Azure CLI，在具有階層命名空間的儲存體帳戶中，管理目錄、檔案以及目錄存取控制清單 (ACL)。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 05/18/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 159f3c63a647ff565e838b01dbaaadf947fb8ada
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142613"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Azure CLI 來管理 Azure Data Lake Storage Gen2 中的目錄、檔案，以及 ACL

本文介紹如何使用 [Azure 命令列介面 (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)，在具有階層命名空間的儲存體帳戶，建立並管理目錄、檔案以及權限。 

[範例](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  | [提供意見](https://github.com/Azure/azure-cli-extensions/issues)反應

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 (HNS) 的儲存體帳戶。 遵循[下列](data-lake-storage-quickstart-create-account.md)指示以建立帳戶。
> * Azure CLI `2.6.0` 版或更高版本。

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>請確保您已安裝正確版本的 Azure CLI

1. 開啟 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或如果您已在本機[安裝](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI，請開啟命令主控台應用程式，例如 Windows PowerShell。

2. 使用下列命令，確認已安裝的 Azure CLI 版本為 `2.6.0` 或更高版本。

   ```azurecli
    az --version
   ```
   如果您的 Azure CLI 版本低於 `2.6.0`，請安裝較新的版本。 請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="connect-to-the-account"></a>連線到帳戶

1. 如果您在本機使用 Azure CLI，請執行登入命令。

   ```azurecli
   az login
   ```

   如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。

   否則，請在 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 開啟瀏覽器頁面，並輸入顯示在終端機中的授權碼。 然後，請在瀏覽器中使用您的帳戶認證登入。

   若要深入了解不同的驗證方法，請參閱[使用 Azure CLI 授與 Blob 或佇列資料的存取權](../common/authorize-data-operations-cli.md)。

2. 如果您的身分識別與多個訂閱相關聯，請將您的使用中訂閱設為將代管靜態網站的儲存體帳戶的訂閱。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   使用您的訂閱識別碼取代 `<subscription-id>` 預留位置值。

> [!NOTE]
> 本文中所述的範例會顯示 Azure Active Directory (AD) 授權。 若要深入了解授權方法，請參閱[使用 Azure CLI 授與 Blob 或佇列資料的存取權](../common/authorize-data-operations-cli.md)。

## <a name="create-a-container"></a>建立容器

容器會作為檔案的檔案系統。 您可以使用 `az storage fs create` 命令來建立檔案系統。 

這個範例會建立名為的容器 `my-file-system` 。

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>顯示容器屬性

您可以使用命令，將容器的屬性列印到主控台 `az storage fs show` 。

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>列出容器內容

使用 `az storage fs file list` 命令，列出目錄的內容。

這個範例會列出名為之容器的內容 `my-file-system` 。

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>刪除容器

使用命令來刪除容器 `az storage fs delete` 。

這個範例會刪除名為的容器 `my-file-system` 。 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>建立目錄

使用 `az storage fs directory create` 命令，建立目錄參考。 

這個範例會將名為的目錄新增至名為 `my-directory` `my-file-system` 的容器中，該容器位於名為的帳戶中 `mystorageaccount` 。

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>顯示目錄屬性

您可以使用 `az storage fs directory show` 命令，將目錄的屬性列印至主控台。

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>重新命名或移動目錄

使用 `az storage fs directory move` 命令，重新命名或移動目錄。

這個範例會將名稱的目錄重新命名 `my-directory` 為 `my-new-directory` 相同容器中的名稱。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

這個範例會將目錄移至名為的容器 `my-second-file-system` 。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>刪除目錄

使用 `az storage fs directory delete` 命令，刪除目錄。

此範例刪除名為 `my-directory` 的目錄。 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>檢查目錄是否存在

使用命令判斷容器中是否有特定的目錄 `az storage fs directory exists` 。

這個範例會顯示名為的目錄是否 `my-directory` 存在於 `my-file-system` 容器中。 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>從目錄下載

使用 `az storage fs file download` 命令，從目錄下載檔案。

此範例從名為 `my-directory` 的目錄下載名為 `upload.txt` 的檔案。 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>列出目錄內容

使用 `az storage fs file list` 命令，列出目錄的內容。

這個範例會列出名為的目錄內容 `my-directory` ，該目錄位於 `my-file-system` 名為之儲存體帳戶的容器中 `mystorageaccount` 。 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳至目錄

使用 `az storage fs directory upload` 命令，將檔案上傳至目錄。

此範例將名為 `upload.txt` 的檔案上傳至名為 `my-directory` 的目錄。 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>顯示檔案屬性

您可以使用 `az storage fs file show` 命令，將檔案的屬性列印至主控台。

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>重新命名或移動檔案

使用 `az storage fs file move` 命令，重新命名或移動檔案。

此範例會將名稱為 `my-file.txt` 的檔案重新命名為 `my-file-renamed.txt`。

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>刪除檔案

使用 `az storage fs file delete` 命令，刪除檔案。

此範例會刪除名為 `my-file.txt` 的檔案

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>管理權限

您可以取得、設定及更新目錄和檔案的存取權限。

> [!NOTE]
> 如果您使用 Azure Active Directory (Azure AD) 來授權命令，請確定已將[儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)指派給您的安全性主體。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

### <a name="get-an-acl"></a>取得 ACL

使用 `az storage fs access show` 命令，取得**目錄**的 ACL。

此範例會取得目錄的 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

使用 `az storage fs access show` 命令，取得**檔案**的存取權限。 

此範例取得檔案的 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

下圖顯示取得目錄的 ACL 之後的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

在此範例中，擁有使用者具有讀取、寫入和執行權限。 擁有群組只有讀取和執行權限。 如需存取控制清單的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="set-an-acl"></a>設定 ACL

使用 `az storage fs access set` 命令，設定**目錄**的 ACL。 

此範例會針對擁有使用者、擁有群組，或其他使用者，在目錄上設定 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

此範例會針對擁有使用者、擁有群組，或其他使用者，在目錄上設定*預設* ACL，然後將 ACL 列印到主控台。

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

使用 `az storage fs access set` 命令，設定**檔案**的 ACL。 

此範例會針對擁有使用者、擁有群組，或其他使用者，在檔案上設定 ACL，然後將 ACL 列印到主控台。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

下圖顯示設定檔案的 ACL 之後的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

在此範例中，擁有使用者和擁有群組只有讀取和寫入權限。 所有其他使用者具備寫入和執行權限。 如需存取控制清單的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="update-an-acl"></a>更新 ACL

設定此權限的另一種方法是使用 `az storage fs access set` 命令。 

藉由將 `-permissions` 參數設為簡短形式的 ACL，以更新目錄或檔案的 ACL。

這個範例會更新**目錄**的 ACL。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

這個範例會更新**檔案**的 ACL。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

您也可以藉由將 `--owner` 或 `group` 參數設為實體識別碼或使用者的使用者主體名稱 (UPN)，更新目錄或檔案的擁有使用者和群組。 

此範例會變更目錄的擁有者。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

此範例會變更檔案的擁有者。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>另請參閱

* [範例](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
* [提供意見反應](https://github.com/Azure/azure-cli-extensions/issues)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


