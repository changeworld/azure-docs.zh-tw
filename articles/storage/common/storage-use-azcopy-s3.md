---
title: 使用 AzCopy 將資料從 Amazon S3 複製到 Azure 儲存體 |Microsoft Docs
description: 使用 AzCopy 將資料從 Amazon S3 複製到 Azure 儲存體。 AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製 Blob 或檔案。
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: ac093f707167160e916c15b935cb3d8ff6bbc748
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88037111"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>使用 AzCopy 將資料從 Amazon S3 複製到 Azure 儲存體

AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製 Blob 或檔案。 本文可協助您使用 AzCopy，將物件、目錄和 bucket 從 Amazon Web Services (AWS) S3 複製到 Azure blob 儲存體。

## <a name="choose-how-youll-provide-authorization-credentials"></a>選擇您要如何提供授權認證

* 若要使用 Azure 儲存體進行授權，請使用 Azure Active Directory (AD) 或共用存取簽章 (SAS) 權杖。

* 若要使用 AWS S3 進行授權，請使用 AWS 存取金鑰和秘密存取金鑰。

### <a name="authorize-with-azure-storage"></a>Azure 儲存體的授權

請參閱 [開始使用 AzCopy](storage-use-azcopy-v10.md) 文章來下載 AzCopy，以及選擇您將如何提供授權認證給儲存體服務。

> [!NOTE]
> 本文中的範例假設您已使用命令驗證您的身分識別 `AzCopy login` 。 AzCopy 接著會使用您的 Azure AD 帳戶來授權存取 Blob 儲存體中的資料。
>
> 如果您想要使用 SAS 權杖來授與 blob 資料的存取權，您可以將該權杖附加至每個 AzCopy 命令中的資源 URL。
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

AzCopy 會使用 [URL API 的 Put 區塊](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) ，因此會直接在 AWS S3 與儲存體伺服器之間複製資料。 這些複製作業不會使用您電腦的網路頻寬。

> [!TIP]
> 本章節中的範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

 這些範例也適用于具有階層命名空間的帳戶。 [Data Lake Storage 上的多重通訊協定存取](../blobs/data-lake-storage-multi-protocol-access.md) 可讓您在這些帳戶上使用相同的 URL 語法 (`blob.core.windows.net`) 。 

### <a name="copy-an-object"></a>複製物件

`blob.core.windows.net`針對具有階層命名空間的帳戶，使用相同的 URL 語法 () 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> 本文中的範例會使用 AWS S3 bucket (的路徑樣式 Url，例如： `http://s3.amazonaws.com/<bucket-name>`) 。 
>
> 您也可以使用虛擬主控樣式的 Url (例如： `http://bucket.s3.amazonaws.com`) 。 
>
> 若要深入瞭解值區的虛擬裝載，請參閱 [虛擬主機 Bucket]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) 。

### <a name="copy-a-directory"></a>複製目錄

`blob.core.windows.net`針對具有階層命名空間的帳戶，使用相同的 URL 語法 () 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| 階層命名空間 (**範例**) | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> 此範例會附加 `--recursive` 旗標以複製所有子目錄中的檔案。

### <a name="copy-the-contents-of-a-directory"></a>複製目錄的內容

您可以使用萬用字元符號 ( * ) ，複製目錄的內容而不復制包含目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| 階層命名空間 (**範例**) | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>複製 bucket

`blob.core.windows.net`針對具有階層命名空間的帳戶，使用相同的 URL 語法 () 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| 階層命名空間 (**範例**) | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>複製所有區域中的所有 bucket

`blob.core.windows.net`針對具有階層命名空間的帳戶，使用相同的 URL 語法 () 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| 階層命名空間 (**範例**) | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>複製特定 S3 區域中的所有 bucket

`blob.core.windows.net`針對具有階層命名空間的帳戶，使用相同的 URL 語法 () 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **範例** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| 階層命名空間 (**範例**) | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>處理物件命名規則的差異

相較于 Azure blob 容器，AWS S3 有一組不同的 bucket 名稱命名慣例。 您可以在 [這裡](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)閱讀相關資訊。 如果您選擇將一組 bucket 複製到 Azure 儲存體帳戶，複製作業可能會因為命名差異而失敗。

AzCopy 會處理可能發生的兩個最常見問題;包含句點和值區的值區，其中包含連續的連字號。 AWS S3 bucket 名稱可包含句點和連續的連字號，但 Azure 中的容器無法。 AzCopy 會以連字號和連續連字號的數位取代句點，以代表連續連字號的數目 (例如：名為的值區 `my----bucket` `my-4-bucket` 。 

此外，當 AzCopy 複製檔案時，它會檢查命名衝突，並嘗試解決它們。 例如，如果有值區的名稱 `bucket-name` 和 `bucket.name` ，AzCopy 會將名為 first 的 bucket 解析為 `bucket.name` `bucket-name` ，然後再解析為 `bucket-name-2` 。

## <a name="handle-differences-in-object-metadata"></a>處理物件中繼資料的差異

AWS S3 和 Azure 允許在物件索引鍵的名稱中有不同組的字元。 您可以閱讀 AWS S3 在 [此](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)使用的字元。 在 Azure 端，blob 物件索引鍵會遵守 [c # 識別碼](https://docs.microsoft.com/dotnet/csharp/language-reference/)的命名規則。

作為 AzCopy 命令的一部分 `copy` ，您可以為選擇性的旗標提供一個值，以 `s2s-handle-invalid-metadata` 指定您要如何處理檔案的中繼資料包含不相容的索引鍵名稱。 下表描述每個旗標值。

| 旗標值 | 描述  |
|--------|-----------|
| **ExcludeIfInvalid** |  (預設選項) 中繼資料不會包含在傳送的物件中。 AzCopy 會記錄警告。 |
| **FailIfInvalid** | 不會複製物件。 AzCopy 會記錄錯誤，並將該錯誤包含在「傳輸摘要」中的失敗計數。  |
| **RenameIfInvalid**  | AzCopy 會解析不正確中繼資料索引鍵，並使用已解析的中繼資料金鑰值組，將物件複製到 Azure。 若要瞭解 AzCopy 重新命名物件索引鍵的確切步驟，請參閱下面的 [AzCopy 如何重新命名物件金鑰](#rename-logic) 一節。 如果 AzCopy 無法重新命名機碼，就不會複製該物件。 |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>AzCopy 如何重新命名物件金鑰

AzCopy 執行下列步驟：

1. 以 ' _ ' 取代不正確字元。

2. 將字串加入 `rename_` 至新的有效索引鍵開頭。

   這個金鑰將用來儲存原始的中繼資料 **值**。

3. 將字串加入 `rename_key_` 至新的有效索引鍵開頭。
   此金鑰將用來儲存原始中繼資料不正確索引 **鍵**。
   您可以使用此金鑰來嘗試和復原 Azure 端的中繼資料，因為中繼資料索引鍵會保留為 Blob 儲存體服務上的值。

## <a name="next-steps"></a>後續步驟

在這些文章中尋找更多範例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)
