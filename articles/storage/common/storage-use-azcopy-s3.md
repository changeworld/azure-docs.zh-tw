---
title: 使用 AzCopy 將資料從 Amazon S3 複製到 Azure 儲存體 |Microsoft Docs
description: 使用 AzCopy 和 Amazon S3 bucket 來傳輸資料
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941495"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>使用 AzCopy 將資料從 Amazon S3 複製到 Azure 儲存體

AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製 blob 或檔案。 本文可協助您使用 AzCopy，將物件、目錄和 bucket 從 Amazon Web Services （AWS） S3 複製到 Azure blob 儲存體。

## <a name="choose-how-youll-provide-authorization-credentials"></a>選擇您要如何提供授權認證

* 若要授權 Azure 儲存體，請使用 Azure Active Directory （AD）或共用存取簽章（SAS）權杖。

* 若要授權使用 AWS S3，請使用 AWS 存取金鑰和秘密存取金鑰。

### <a name="authorize-with-azure-storage"></a>使用 Azure 儲存體進行授權

請參閱[開始使用 AzCopy](storage-use-azcopy-v10.md)一文以下載 AzCopy，並選擇您要如何提供授權認證給儲存體服務。

> [!NOTE]
> 本文中的範例假設您已使用 `AzCopy login` 命令驗證您的身分識別。 然後，AzCopy 會使用您的 Azure AD 帳戶來授權存取 Blob 儲存體中的資料。
>
> 如果您想要使用 SAS 權杖來授權 blob 資料的存取權，您可以在每個 AzCopy 命令中，將該權杖附加至資源 URL。
>
> 例如： `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>` 。

### <a name="authorize-with-aws-s3"></a>使用 AWS S3 授權

收集您的 AWS 存取金鑰和秘密存取金鑰，然後設定這些環境變數：

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>複製物件、目錄和 bucket

AzCopy 會使用[來自 URL API 的 Put 區塊](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)，因此資料會直接複製到 AWS S3 和存放伺服器之間。 這些複製作業不會使用您電腦的網路頻寬。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 如果您決定在複製作業之後移除 S3 bucket 的資料，請務必先確認資料已正確複製到儲存體帳戶，然後再移除資料。

> [!TIP]
> 本節中的範例會以單引號（' '）括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

 這些範例也適用于具有階層式命名空間的帳戶。 [Data Lake Storage 上的多重通訊協定存取](../blobs/data-lake-storage-multi-protocol-access.md)，可讓您在這些帳戶上使用相同的 URL 語法（`blob.core.windows.net`）。 

### <a name="copy-an-object"></a>複製物件

針對具有階層式命名空間的帳戶，請使用相同的 URL 語法（`blob.core.windows.net`）。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **範例**（階層式命名空間） | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> 本文中的範例會使用 AWS S3 bucket 的路徑樣式 Url （例如： `http://s3.amazonaws.com/<bucket-name>`）。 
>
> 您也可以使用虛擬主控樣式的 Url （例如： `http://bucket.s3.amazonaws.com`）。 
>
> 若要深入瞭解值區的虛擬裝載，請參閱 [主機 Bucket 的虛擬裝載]] （https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)。

### <a name="copy-a-directory"></a>複製目錄

針對具有階層式命名空間的帳戶，請使用相同的 URL 語法（`blob.core.windows.net`）。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **範例**（階層式命名空間）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>複製值區

針對具有階層式命名空間的帳戶，請使用相同的 URL 語法（`blob.core.windows.net`）。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **範例**（階層式命名空間）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>複製所有區域中的所有值區

針對具有階層式命名空間的帳戶，請使用相同的 URL 語法（`blob.core.windows.net`）。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **範例**（階層式命名空間）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>複製特定 S3 區域中的所有 bucket

針對具有階層式命名空間的帳戶，請使用相同的 URL 語法（`blob.core.windows.net`）。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **範例** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **範例**（階層式命名空間）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>處理物件命名規則中的差異

相較于 Azure blob 容器，AWS S3 具有一組不同的值區名稱命名慣例。 您可以在[這裡](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)閱讀相關資訊。 如果您選擇將一組 bucket 複製到 Azure 儲存體帳戶，則複製作業可能會因為命名差異而失敗。

AzCopy 會處理兩個最常發生的問題;包含句點和 bucket 的值區，其中包含連續的連字號。 AWS S3 值區名稱可以包含句點和連續的連字號，但 Azure 中的容器無法。 AzCopy 會以連字號和連續連字號取代句點，其數位代表連續連字號的數目（例如：名為 `my----bucket` 的值區會變成 `my-4-bucket`。 

此外，當 AzCopy 複製檔案時，它會檢查命名衝突並嘗試加以解決。 例如，如果有 `bucket-name` 和 `bucket.name`名稱的值區，則 AzCopy 會先將名為 `bucket.name` 的值區解析成 `bucket-name`，然後再進行 `bucket-name-2`。

## <a name="handle-differences-in-object-metadata"></a>處理物件中繼資料的差異

AWS S3 和 Azure 允許在物件索引鍵的名稱中有不同的字元集。 您可以閱讀 AWS S3 在[此](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)使用的字元。 在 Azure 端，blob 物件金鑰會遵守[ C#識別碼](https://docs.microsoft.com/dotnet/csharp/language-reference/)的命名規則。

在 AzCopy `copy` 命令中，您可以提供選擇性的 [`s2s-invalid-metadata-handle`] 旗標值，指定您要如何處理檔案的中繼資料包含不相容的索引鍵名稱的檔案。 下表描述每個旗標值。

| 旗標值 | 說明  |
|--------|-----------|
| **ExcludeIfInvalid** | （預設選項）中繼資料不會包含在傳送的物件中。 AzCopy 會記錄一則警告。 |
| **FailIfInvalid** | 不會複製物件。 AzCopy 會記錄錯誤，並在傳輸摘要中出現的失敗計數中包含該錯誤。  |
| **RenameIfInvalid**  | AzCopy 會解析不正確中繼資料索引鍵，並使用已解析的中繼資料金鑰值組，將物件複製到 Azure。 若要確切瞭解 AzCopy 重新命名物件索引鍵所需的步驟，請參閱下面的 [如何將物件金鑰重新命名一節](#rename-logic)。 如果 AzCopy 無法重新命名索引鍵，則不會複製物件。 |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>AzCopy 如何重新命名物件索引鍵

AzCopy 會執行下列步驟：

1. 以 ' _ ' 取代不正確字元。

2. 將字串 `rename_` 新增至新有效金鑰的開頭。

   這個金鑰將用來儲存原始的中繼資料**值**。

3. 將字串 `rename_key_` 新增至新有效金鑰的開頭。
   此金鑰將用來儲存原始中繼資料不正確索引**鍵**。
   您可以使用此金鑰來嘗試和復原 Azure 端的中繼資料，因為中繼資料索引鍵會保留為 Blob 儲存體服務上的值。

## <a name="next-steps"></a>後續步驟

在這些文章中尋找更多範例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)