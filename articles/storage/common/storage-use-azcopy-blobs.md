---
title: 使用 AzCopy v10 在 Azure Blob 儲存體之間傳輸資料 |Microsoft Docs
description: 本文包含 AzCopy 範例命令的集合，可協助您建立容器、複製檔案，以及同步處理本機檔案系統與容器之間的目錄。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 7ff8f3d18564140b4654b1591eec5c0e1f40b7cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89077903"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>使用 AzCopy 和 Blob 儲存體傳輸資料

AzCopy 是一種命令列公用程式，可讓您用來在儲存體帳戶之間複製資料。 本文包含適用于 Blob 儲存體的範例命令。

> [!TIP]
> 本文中的範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

## <a name="get-started"></a>開始使用

請參閱 [開始使用 AzCopy](storage-use-azcopy-v10.md) 文章以下載 AzCopy，並瞭解您可以提供授權認證給儲存體服務的方式。

> [!NOTE]
> 本文中的範例假設您已使用命令驗證您的身分識別 `AzCopy login` 。 AzCopy 接著會使用您的 Azure AD 帳戶來授權存取 Blob 儲存體中的資料。
>
> 如果您想要使用 SAS 權杖來授與 blob 資料的存取權，您可以將該權杖附加至每個 AzCopy 命令中的資源 URL。
>
> 例如： `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'` 。

## <a name="create-a-container"></a>建立容器

您可以使用 [azcopy make](storage-ref-azcopy-make.md) 命令來建立容器。 本節中的範例會建立名為的容器 `mycontainer` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **範例** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| 階層命名空間 (**範例**)  | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

如需詳細的參考檔，請參閱 [azcopy](storage-ref-azcopy-make.md)。

## <a name="upload-files"></a>上傳檔案

您可以使用 [azcopy 複製](storage-ref-azcopy-copy.md) 命令，從您的本機電腦上傳檔案和目錄。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳目錄
> * 上傳目錄的內容 
> * 上傳特定檔案

> [!TIP]
> 您可以使用選擇性旗標來調整上傳作業。 以下是一些範例。
>
> |狀況|旗標|
> |---|---|
> |以附加 Blob 或分頁 Blob 的形式上傳檔案。|**--blob 類型** = \[BlockBlob \| PageBlob \| AppendBlob\]|
> |上傳至特定存取層 (例如封存層)。|**--區塊-blob 層** = \[無 \| 熱 \| 冷 \| 封存\]|
> 
> 如需完整清單，請參閱 [選項](storage-ref-azcopy-copy.md#options)。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **範例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

您也可以使用萬用字元符號來上傳檔案 ( * ) 在檔案路徑或檔案名中的任何位置。 例如： `'C:\myDirectory\*.txt'` 、或 `C:\my*\*.txt` 。

### <a name="upload-a-directory"></a>上傳目錄

此範例會將目錄 (和該目錄中的所有檔案) 複製到 blob 容器。 結果會是容器中相同名稱的目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

若要複製到容器內的目錄，只需在命令字串中指定該目錄的名稱即可。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

如果您指定不存在於容器中的目錄名稱，AzCopy 會依該名稱建立新的目錄。

### <a name="upload-the-contents-of-a-directory"></a>上傳目錄的內容

您可以使用萬用字元符號 ( * ) ，上傳目錄的內容而不復制包含目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **範例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> 附加 `--recursive` 旗標以上傳所有子目錄中的檔案。

### <a name="upload-specific-files"></a>上傳特定檔案

您可以使用完整檔案名、使用萬用字元 ( * ) 的部分名稱，或使用日期和時間，來上傳特定檔案。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-path` 選項。 使用分號 () 來分隔個別的檔案名 `;` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

在此範例中，AzCopy 會傳送 `C:\myDirectory\photos` 目錄和檔案 `C:\myDirectory\documents\myFile.txt` 。 您必須包含在 `--recursive` 目錄中傳送所有檔案的選項 `C:\myDirectory\photos` 。

您也可以使用選項來排除檔案 `--exclude-path` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-pattern` 選項。 指定包含萬用字元的部分名稱。 使用 semicolin () 來分隔名稱 `;` 。 

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

您也可以使用選項來排除檔案 `--exclude-pattern` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

`--include-pattern`和 `--exclude-pattern` 選項只適用于檔案名，而不會套用至路徑。  如果您想要複製存在於目錄樹狀結構中的所有文字檔，請使用 `–recursive` 選項來取得整個目錄樹狀結構，然後使用 `–include-pattern` 和指定 `*.txt` 來取得所有文字檔。

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>上傳在日期和時間之後修改過的檔案 

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-after` 選項。 以 ISO-8601 格式指定日期和時間 (例如： `2020-08-19T15:04:00Z`) 。 

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **範例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

如需詳細參考，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

## <a name="download-files"></a>下載檔案

您可以使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令，將 blob、目錄和容器下載到本機電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 下載目錄
> * 下載目錄的內容
> * 下載特定檔案

> [!TIP]
> 您可以使用選擇性旗標來調整您的下載作業。 以下是一些範例。
>
> |狀況|旗標|
> |---|---|
> |自動解壓縮檔案。|**--解壓縮**|
> |指定您希望複製相關記錄專案的詳細程度。|**--記錄層級** = \[警告 \| 錯誤 \| 資訊 \| 無\]|
> |指定是否及如何覆寫目的地端的衝突檔案和 blob。|**--覆寫** = \[true \| false \| ifSourceNewer \| 提示字元\]|
> 
> 如需完整清單，請參閱 [選項](storage-ref-azcopy-copy.md#options)。

> [!NOTE]
> 如果 `Content-md5` blob 的屬性值包含雜湊，AzCopy 會計算所下載資料的 md5 雜湊，並驗證儲存在 blob 屬性中的 md5 雜湊是否 `Content-md5` 符合計算的雜湊。 如果這些值不相符，除非您附加 `--check-md5=NoCheck` 或複製命令來覆寫此行為，否則下載會失敗 `--check-md5=LogOnly` 。

### <a name="download-a-file"></a>下載檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>下載目錄

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

此範例會產生名為 `C:\myDirectory\myBlobDirectory` 的目錄，其中包含所有下載的檔案。

### <a name="download-the-contents-of-a-directory"></a>下載目錄的內容

您可以使用萬用字元符號 ( * ) ，在不復制包含目錄本身的情況下下載目錄的內容。

> [!NOTE]
> 目前，只有沒有階層命名空間的帳戶支援此案例。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> 附加 `--recursive` 旗標以下載所有子目錄中的檔案。

### <a name="download-specific-files"></a>下載特定檔案

您可以使用完整檔案名、使用萬用字元 ( * ) 的部分名稱，或使用日期和時間來下載特定檔案。 

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-path` 選項。 使用 semicolin () 來分隔個別的檔案名 `;` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

在此範例中，AzCopy 會傳送 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 目錄和檔案 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` 。 您必須包含在 `--recursive` 目錄中傳送所有檔案的選項 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 。

您也可以使用選項來排除檔案 `--exclude-path` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-pattern` 選項。 指定包含萬用字元的部分名稱。 使用 semicolin () 來分隔名稱 `;` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

您也可以使用選項來排除檔案 `--exclude-pattern` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

`--include-pattern`和 `--exclude-pattern` 選項只適用于檔案名，而不會套用至路徑。  如果您想要複製存在於目錄樹狀結構中的所有文字檔，請使用 `–recursive` 選項來取得整個目錄樹狀結構，然後使用 `–include-pattern` 和指定 `*.txt` 來取得所有文字檔。

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>下載在日期和時間之後修改過的檔案 

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-after` 選項。 以 ISO-8601 格式指定日期和時間 (例如： `2020-08-19T15:04:00Z`) 。 

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

如需詳細參考，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

#### <a name="download-previous-versions-of-a-blob"></a>下載先前版本的 blob

如果您已啟用 [blob 版本](../blobs/versioning-enable.md)設定，則可以下載一或多個舊版的 blob。 

首先，建立包含 [版本識別碼](../blobs/versioning-overview.md)清單的文字檔。 每個版本識別碼都必須出現在不同的行上。 例如： 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

然後，將 [azcopy copy](storage-ref-azcopy-copy.md) 命令與選項搭配使用 `--list-of-versions` 。 指定包含版本清單之文字檔的位置 (例如： `D:\\list-of-versions.txt`) 。  

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-directory-path>' --list-of-versions '<list-of-versions-file>'`|
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |

每個已下載檔案的名稱會以版本識別碼開頭，後面接著 blob 的名稱。 

## <a name="copy-blobs-between-storage-accounts"></a>在儲存體帳戶間複製 Blob

您可以使用 AzCopy 將Bblob 複製到其他儲存體帳戶。 複製作業是同步的，因此當命令傳回時，表示已複製所有檔案。 

AzCopy 會使用 [伺服器對伺服器](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [api](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)，因此會直接在儲存體伺服器之間複製資料。 這些複製作業不會使用您電腦的網路頻寬。 您可以藉由設定環境變數的值，來增加這些作業的輸送量 `AZCOPY_CONCURRENCY_VALUE` 。 若要深入瞭解，請參閱 [優化輸送量](storage-use-azcopy-configure.md#optimize-throughput)。

> [!NOTE]
> 此案例在目前的版本中有下列限制。
>
> - 您必須將 SAS 權杖附加至每個來源 URL。 如果您使用 Azure Active Directory (AD) 提供授權認證，您只能從目的地 URL 中省略 SAS 權杖。 請確定您已在目的地帳戶中設定適當的角色。 請參閱 [選項1：使用 Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)。
>-  Premium 區塊 blob 儲存體帳戶不支援存取層。 將設定 `s2s-preserve-access-tier` 為 (例如：) ，以從複製作業中省略 blob 的存取層 `false` `--s2s-preserve-access-tier=false` 。

本區段包含下列範例：

> [!div class="checklist"]
> * 將 blob 複製到另一個儲存體帳戶
> * 將目錄複寫到另一個儲存體帳戶
> * 將容器複製到另一個儲存體帳戶
> * 將所有容器、目錄和檔案複製到另一個儲存體帳戶

這些範例也適用于具有階層命名空間的帳戶。 [Data Lake Storage 上的多重通訊協定存取](../blobs/data-lake-storage-multi-protocol-access.md) 可讓您在這些帳戶上使用相同的 URL 語法 (`blob.core.windows.net`) 。

> [!TIP]
> 您可以使用選擇性旗標來調整複製操作。 以下是一些範例。
>
> |狀況|旗標|
> |---|---|
> |將 blob 複製為區塊、分頁或附加 Blob。|**--blob 類型** = \[BlockBlob \| PageBlob \| AppendBlob\]|
> |複製到特定的存取層 (例如封存層) 。|**--區塊-blob 層** = \[無 \| 熱 \| 冷 \| 封存\]|
> |自動解壓縮檔案。|**--解壓縮** = \[gzip \| deflate\]|
> 
> 如需完整清單，請參閱 [選項](storage-ref-azcopy-copy.md#options)。

### <a name="copy-a-blob-to-another-storage-account"></a>將 blob 複製到另一個儲存體帳戶

`blob.core.windows.net`針對具有階層命名空間的帳戶，使用相同的 URL 語法 () 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>將目錄複寫到另一個儲存體帳戶

`blob.core.windows.net`針對具有階層命名空間的帳戶，使用相同的 URL 語法 () 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| 階層命名空間 (**範例**) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>將容器複製到另一個儲存體帳戶

`blob.core.windows.net`針對具有階層命名空間的帳戶，使用相同的 URL 語法 () 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| 階層命名空間 (**範例**) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>將所有容器、目錄和 blob 複製到另一個儲存體帳戶

`blob.core.windows.net`針對具有階層命名空間的帳戶，使用相同的 URL 語法 () 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| 階層命名空間 (**範例**) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>同步處理檔案

您可以同步處理本機檔案系統與 blob 容器的內容。 您也可以同步處理彼此的容器和虛擬目錄。 同步處理是單向的。 換句話說，您可以選擇這兩個端點中的哪一個是來源，哪一個是目的地。 同步處理也會使用伺服器對伺服器 Api。 本節中提供的範例也適用于具有階層命名空間的帳戶。 

> [!NOTE]
> 目前的 AzCopy 版本不會在其他來源和目的地之間進行同步處理 (例如：檔案儲存體或 Amazon Web Services (AWS) S3 bucket 值區) 。

[Sync](storage-ref-azcopy-sync.md)命令會比較檔案名和上次修改的時間戳記。 將 `--delete-destination` 選擇性旗標設定為的值， `true` `prompt` 如果這些檔案不再存在於來原始目錄中，則刪除目的地目錄中的檔案。

如果您將旗標設定 `--delete-destination` 為 `true` AzCopy 刪除檔案，而不提供提示。 如果您想要在 AzCopy 刪除檔案之前出現提示，請將旗標設定 `--delete-destination` 為 `prompt` 。

> [!NOTE]
> 若要防止意外刪除，請務必先啟用虛 [刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) 功能，然後再使用 `--delete-destination=prompt|true` 旗標。

> [!TIP]
> 您可以使用選擇性旗標來調整同步處理作業。 以下是一些範例。
>
> |狀況|旗標|
> |---|---|
> |指定在下載時，應如何驗證嚴格的 MD5 雜湊。|**--check-md5** = \[NoCheck \| LogOnly \| FailIfDifferent \| FailIfDifferentOrMissing\]|
> |根據模式排除檔案。|**--排除-路徑**|
> |指定您想要同步處理相關記錄專案的詳細程度。|**--記錄層級** = \[警告 \| 錯誤 \| 資訊 \| 無\]|
> 
> 如需完整清單，請參閱 [選項](storage-ref-azcopy-sync.md#options)。

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>使用本機檔案系統的變更來更新容器

在此情況下，容器是目的地，而本機檔案系統則是來源。 

|    |     |
|--------|-----------|
| **語法** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>將有變更的本機檔案系統上傳至容器

在此情況下，本機檔案系統是目的地，而容器是來源。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **範例** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>更新另一個容器中有變更的容器

此命令中出現的第一個容器為來源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>使用其他檔案共用中的目錄變更來更新目錄

此命令中出現的第一個目錄是來源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>後續步驟

在這些文章中尋找更多範例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [教學課程：使用 AzCopy 將內部部署資料移轉至雲端儲存體](storage-use-azcopy-migrate-on-premises-data.md)

- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)
