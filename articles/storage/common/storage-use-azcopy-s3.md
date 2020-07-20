---
title: 使用 AzCopy 將資料從 Amazon S3 複製到 Azure 存儲 |微軟文檔
description: 使用 AzCopy 和 Amazon S3 貯體轉送資料
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941495"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>使用 AzCopy 將資料從 Amazon S3 複製到 Azure 存儲

AzCopy 是一個命令列實用程式，可用於將 Blob 或檔案複製到存儲帳戶或從存儲帳戶複製。 本文可説明您使用 AzCopy 將物件、目錄和存儲桶從 Amazon Web 服務 （AWS） S3 複製到 Azure Blob 存儲。

## <a name="choose-how-youll-provide-authorization-credentials"></a>選擇提供授權憑據的方式

* 要使用 Azure 存儲進行授權，請使用 Azure 活動目錄 （AD） 或共用訪問簽名 （SAS） 權杖。

* 要使用 AWS S3 進行授權，請使用 AWS 訪問金鑰和金鑰金鑰。

### <a name="authorize-with-azure-storage"></a>使用 Azure 存儲授權

請參閱[使用 AzCopy 入門](storage-use-azcopy-v10.md)文章下載 AzCopy，並選擇如何向存儲服務提供授權憑據。

> [!NOTE]
> 本文中的示例假定您已使用 命令`AzCopy login`驗證了身份。 然後，AzCopy 使用 Azure AD 帳戶授權訪問 Blob 存儲中的資料。
>
> 如果您希望使用 SAS 權杖來授權對 Blob 資料的訪問，則可以將該權杖追加到每個 AzCopy 命令中的資源 URL 中。
>
> 例如：`https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`。

### <a name="authorize-with-aws-s3"></a>使用 AWS S3 授權

收集 AWS 訪問金鑰和金鑰金鑰，然後設置以下環境變數：

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>複製物件、目錄和存儲桶

AzCopy 使用["從 URL 開始"API，](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)因此資料直接在 AWS S3 和存儲伺服器之間複製。 這些複製操作不使用電腦的網路頻寬。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 如果在複製操作後決定從 S3 存儲桶中刪除資料，請確保在刪除資料之前驗證資料是否正確複製到存儲帳戶。

> [!TIP]
> 本節中的示例將路徑參數與單引號 （''） 括起來。 除 Windows 命令外殼 （cmd.exe） 外，在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 （cmd.exe），則用雙引號（"）而不是單個引號（''）括起來路徑參數。

 這些示例還處理具有階層命名空間的帳戶。 [通過資料存儲湖存儲的多協定訪問](../blobs/data-lake-storage-multi-protocol-access.md)，您可以在這些帳戶上使用相同的 URL`blob.core.windows.net`語法 （ ） 。 

### <a name="copy-an-object"></a>複製物件

對於具有階層命名空間的`blob.core.windows.net`帳戶，使用相同的 URL 語法 （ ） 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **示例**（階層命名空間） | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> 本文中的示例對 AWS S3 存儲桶使用路徑樣式 URL（例如： `http://s3.amazonaws.com/<bucket-name>` 
>
> 也可以使用虛擬託管樣式 URL（例如： `http://bucket.s3.amazonaws.com` 
>
> 要瞭解有關存儲桶虛擬託管的更多詳細資訊，請參閱 [存儲桶的虛擬託管]（https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)

### <a name="copy-a-directory"></a>複製目錄

對於具有階層命名空間的`blob.core.windows.net`帳戶，使用相同的 URL 語法 （ ） 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **示例**（階層命名空間）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>複製存儲桶

對於具有階層命名空間的`blob.core.windows.net`帳戶，使用相同的 URL 語法 （ ） 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **示例**（階層命名空間）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>複製所有區域中的所有存儲桶

對於具有階層命名空間的`blob.core.windows.net`帳戶，使用相同的 URL 語法 （ ） 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **範例** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **示例**（階層命名空間）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>複製特定 S3 區域中的所有存儲桶

對於具有階層命名空間的`blob.core.windows.net`帳戶，使用相同的 URL 語法 （ ） 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **範例** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **示例**（階層命名空間）| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>處理物件命名規則的差異

與 Azure Blob 容器相比，AWS S3 具有一組不同的存儲桶名稱命名約定。 你可以在這裡閱讀關於它們[。](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) 如果選擇將一組存儲桶複製到 Azure 存儲帳戶，則複製操作可能會由於命名差異而失敗。

AzCopy 處理可能出現的兩個最常見的問題;包含包含連續連字號的句點和存儲桶。 AWS S3 存儲桶名稱可以包含句點和連續連字號，但 Azure 中的容器不能。 AzCopy 用連字號和連續連字號替換句點，數位表示連續連字號數（例如：名為 的`my----bucket`存儲桶變為`my-4-bucket`。 

此外，當 AzCopy 複製檔時，它會檢查命名衝突並嘗試解決這些問題。 例如`bucket-name`，如果有名稱和`bucket.name`的存儲桶，則 AzCopy 將首先命名的`bucket.name`存儲桶解析為`bucket-name`，然後`bucket-name-2`解析為 。

## <a name="handle-differences-in-object-metadata"></a>處理物件中繼資料的差異

AWS S3 和 Azure 允許在物件鍵名稱中設置不同的字元集。 您可以[在此處](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)閱讀有關 AWS S3 使用的字元。 在 Azure 端，blob 物件鍵符合[C# 識別碼](https://docs.microsoft.com/dotnet/csharp/language-reference/)的命名規則。

作為 AzCopy`copy`命令的一部分，可以為可選`s2s-invalid-metadata-handle`標誌提供一個值，該標誌指定如何處理檔中繼資料包含不相容金鑰名的檔。 下表描述了每個標誌值。

| 標誌值 | 描述  |
|--------|-----------|
| **排除無效** | （預設選項）中繼資料不包括在傳輸的物件中。 AzCopy 記錄警告。 |
| **失敗無效** | 不會複製物件。 AzCopy 記錄錯誤，並在傳輸摘要中顯示的失敗計數中包括該錯誤。  |
| **重命名無效**  | AzCopy 解析不正確中繼資料金鑰，並使用解析的中繼資料鍵值對將物件複製到 Azure。 要確切瞭解 AzCopy 重命名物件鍵的步驟，請參閱下面的[AzCopy 重命名物件鍵](#rename-logic)部分。 如果 AzCopy 無法重命名金鑰，則不會複製該物件。 |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>AzCopy 如何重命名物件鍵

AzCopy 執行以下步驟：

1. 將無效字元替換為"*"。

2. 將字串`rename_`添加到新有效鍵的開頭。

   此鍵將用於保存原始中繼資料**值**。

3. 將字串`rename_key_`添加到新有效鍵的開頭。
   此金鑰將用於保存原始中繼資料無效**金鑰**。
   可以使用此鍵嘗試恢復 Azure 端的中繼資料，因為中繼資料金鑰將保留為 Blob 存儲服務上的值。

## <a name="next-steps"></a>後續步驟

在以下任何文章中查找更多示例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)